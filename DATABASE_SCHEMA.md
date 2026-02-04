# MongoDB Schema Structure

This document describes the complete database schema structure for the Discord bot and dashboard.

## Connection

- **Single Shared Connection**: Both bot and dashboard use `src/database/connection.js` for MongoDB connection
- **Connection Pooling**: Max 10 connections, min 2 connections
- **Auto-reconnect**: Handles disconnections automatically
- **Index Initialization**: All indexes are created on startup

## Collections

### 1. serverconfigs

**Purpose**: Stores guild-level settings (welcome messages, role IDs, channel IDs)

**Schema**:
```javascript
{
  guildId: String (required, unique, indexed),
  welcomeMessage: String (nullable),
  welcomeChannelId: String (nullable),
  verifiedRoleId: String (nullable),
  verificationChannelId: String (nullable),
  inviteLogChannelId: String (nullable),
  ticketCategoryId: String (nullable),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `guildId` (unique)

**Usage**: 
- Dashboard saves settings via `/api/servers/:guildId/settings` POST
- Bot reads settings for welcome messages, verification, tickets, etc.

---

### 2. users

**Purpose**: Tracks user stats, invites, XP, and verification status per guild

**Schema**:
```javascript
{
  userId: String (required),
  guildId: String (required),
  xp: Number (default: 0),
  level: Number (default: 1),
  totalInvites: Number (default: 0),
  validInvites: Number (default: 0),
  ticketsOpened: Number (default: 0),
  isVerified: Boolean (default: false),
  verifiedAt: Date (nullable),
  joinedAt: Date (default: now),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `userId + guildId` (compound unique)
- `guildId + xp` (for leaderboard queries)

**Usage**:
- Created when user joins guild
- Updated when user verifies, opens tickets, or invites members

---

### 3. tickets

**Purpose**: Support ticket system - tracks all support tickets

**Schema**:
```javascript
{
  ticketId: String (required, unique),
  userId: String (required),
  guildId: String (required),
  channelId: String (required),
  purpose: String (required),
  description: String (required),
  status: String (enum: ['open', 'closed', 'resolved'], default: 'open'),
  openedAt: Date (default: now),
  closedAt: Date (nullable),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `ticketId` (unique, auto-created)
- `userId + guildId` (compound)

**Usage**:
- Created when user opens a ticket via modal
- Updated when ticket is closed/resolved
- Dashboard displays all tickets for a server

---

### 4. giveaways

**Purpose**: Manages giveaway events with multiple prizes

**Schema**:
```javascript
{
  giveawayId: String (required, unique),
  guildId: String (required),
  channelId: String (required),
  messageId: String (required),
  title: String (required),
  description: String (required),
  duration: Number (milliseconds),
  endsAt: Date (required),
  prizes: [{
    category: String,
    prize: String,
    winnerCount: Number (default: 1)
  }],
  allowedRoles: [String] (role IDs),
  restrictedRoles: [String] (role IDs),
  participants: [String] (user IDs),
  winners: [{
    userId: String,
    prizeCategory: String,
    prize: String
  }],
  isActive: Boolean (default: true),
  ended: Boolean (default: false),
  createdBy: String (user ID, required),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `giveawayId` (unique, auto-created)
- `guildId + isActive` (compound)
- `endsAt` (for cleanup queries)

**Usage**:
- Created via `/giveaway create` command
- Updated when users join or winners are selected
- Dashboard displays all giveaways

---

### 5. invitelogs

**Purpose**: Tracks which user invited which member

**Schema**:
```javascript
{
  inviterId: String (required),
  invitedId: String (required),
  guildId: String (required),
  inviteCode: String (required),
  joinedAt: Date (default: now),
  isValid: Boolean (default: true),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `inviterId + guildId` (compound)
- `invitedId + guildId` (compound)

**Usage**:
- Created when a new member joins
- Used to calculate invite leaderboards
- Dashboard displays invite logs with filters

---

### 6. winners

**Purpose**: Stores giveaway winners (auto-deletes after 30 days)

**Schema**:
```javascript
{
  userId: String (required),
  guildId: String (required),
  giveawayId: String (required),
  prizeCategory: String (required),
  prize: String (required),
  wonAt: Date (default: now),
  createdAt: Date (auto),
  updatedAt: Date (auto)
}
```

**Indexes**:
- `wonAt` (TTL index - expires after 30 days)
- `userId + guildId` (compound)
- `giveawayId`

**Usage**:
- Created when giveaway winners are selected
- Automatically deleted after 30 days via TTL index
- Dashboard can display recent winners

---

## Connection Flow

1. **Bot Startup** (`src/index.js`):
   - Calls `connectMongoDB()` from shared module
   - Calls `initializeModels()` to create indexes
   - Continues even if connection fails (graceful degradation)

2. **Dashboard Startup** (`src/dashboard/server.js`):
   - Calls `connectMongoDB()` from shared module (reuses connection if bot already connected)
   - Calls `initializeModels()` to ensure indexes exist
   - API routes use `requireDatabase` middleware to wait for connection

3. **API Routes**:
   - All database routes use `requireDatabase` middleware
   - Middleware waits up to 5 seconds for connection
   - Returns 503 if database not available

## Best Practices

1. **Always use the shared connection module** - Never create separate mongoose connections
2. **Wait for connection** - Use `waitForConnection()` or `requireDatabase` middleware
3. **Handle errors gracefully** - Database failures shouldn't crash the application
4. **Use indexes** - All queries should use indexed fields for performance
5. **Validate data** - Mongoose schemas handle validation automatically

## Environment Variables

```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/?appName=appname
```

The connection module automatically adds `/discordbot` database name if not specified.
