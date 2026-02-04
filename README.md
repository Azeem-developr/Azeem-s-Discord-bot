# Discord CFD Prop Firm Bot

A production-ready Discord bot built with Node.js and discord.js v14 for managing a CFD prop firm Discord server, complete with an admin dashboard.

## Features

- ✅ User Verification & Onboarding
- ✅ Support Ticket System
- ✅ Invite Tracking System
- ✅ User Stats & Leaderboards
- ✅ Welcome Messages
- ✅ Giveaway System
- ✅ Admin Broadcast
- ✅ **Admin Dashboard** (React + Express)

## Setup

### Prerequisites

- Node.js 18+ installed
- MongoDB Atlas account (or local MongoDB)
- Discord Bot Token
- Discord Server with admin permissions

### Step 1: Install Dependencies

```bash
# Install bot dependencies
npm install

# Install dashboard dependencies
cd dashboard
npm install
cd ..
```

### Step 2: Configure Environment Variables

Create a `.env` file in the root directory:

```env
# Bot Configuration
DISCORD_BOT_TOKEN=your_bot_token_here
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/database?appName=appname

# Dashboard Configuration
DISCORD_CLIENT_ID=your_discord_client_id
DISCORD_CLIENT_SECRET=your_discord_client_secret
PUBLIC_BASE_URL=https://zippy-vibrancy.up.railway.app
DASHBOARD_URL=https://zippy-vibrancy.up.railway.app
DASHBOARD_CALLBACK_URL=https://zippy-vibrancy.up.railway.app/auth/discord/callback
SESSION_SECRET=your-random-session-secret-here

# Bot presence (optional – or change from Dashboard → Bot Profile)
BOT_ACTIVITY_TYPE=Watching
BOT_ACTIVITY_NAME=Discord Bot
BOT_STATUS=online

# Optional links (dashboard, support, docs, invite – update in .env to change)
SUPPORT_URL=
DOCS_URL=
INVITE_URL=
WEBSITE_URL=

# Server Configuration (set these after bot is in server)
VERIFIED_ROLE_ID=
WELCOME_CHANNEL_ID=
VERIFICATION_CHANNEL_ID=
TICKET_CATEGORY_ID=
INVITE_LOG_CHANNEL_ID=
ADMIN_ROLE_ID=
```

**Configuration:** All important links (dashboard, support, docs, invite) and the bot’s default status/activity are read from `.env`. You can change them without editing code. You can also update the bot’s status and activity instantly from the dashboard (**Bot Profile** page).

### Step 3: Get Discord IDs

1. **Enable Developer Mode** in Discord (User Settings → Advanced → Developer Mode)
2. Right-click on roles/channels to copy their IDs
3. Update the `.env` file with the IDs

### Step 4: Create Discord Application

1. Go to https://discord.com/developers/applications
2. Create a new application
3. Go to **Bot** section:
   - Create a bot
   - Copy the bot token → `DISCORD_BOT_TOKEN`
   - Enable **Message Content Intent** (if needed)
4. Go to **OAuth2** section:
   - Copy **Client ID** → `DISCORD_CLIENT_ID`
   - Copy **Client Secret** → `DISCORD_CLIENT_SECRET`
   - Add redirect URI: `https://zippy-vibrancy.up.railway.app/auth/discord/callback`

### Step 5: Invite Bot to Server

1. Go to **OAuth2 → URL Generator**
2. Select scopes: `bot`, `applications.commands`
3. Select bot permissions:
   - ✅ Manage Channels
   - ✅ Manage Roles
   - ✅ View Channels
   - ✅ Send Messages
   - ✅ Read Message History
   - ✅ Embed Links
   - ✅ Attach Files
   - ✅ Use Slash Commands
   - ✅ Manage Server (for invite tracking)
4. Copy the generated URL and open it in your browser
5. Select your server and authorize

### Step 6: Configure Server

1. **Create Roles:**
   - Create a "Verified" role (or use existing)
   - Create an "Admin" role (or use existing admin role)
   - Copy their IDs to `.env`

2. **Create Channels:**
   - Create a verification channel (only channel visible to unverified users)
   - Create a welcome channel
   - Create an invite log channel
   - Create a category for tickets (optional, but recommended)
   - Copy their IDs to `.env`

3. **Set Permissions:**
   - Make sure the bot has proper permissions in all channels
   - Set verification channel to be visible to @everyone
   - Hide other channels from @everyone (they'll be unlocked after verification)

### Step 7: Run the Bot

```bash
# Start the bot
npm start

# For development with auto-reload
npm run dev
```

### Step 8: Run the Dashboard

```bash
# In a separate terminal, start the dashboard API
npm run dashboard

# In another terminal, start the dashboard frontend
cd dashboard
npm run dev
```

The dashboard will be available at `https://zippy-vibrancy.up.railway.app`

### Step 9: Initial Setup Commands

1. In the verification channel, run `/verify` (admin only) to create the verification button
2. In a channel, run `/ticketpanel` (admin only) to create the ticket panel
3. Configure welcome message: `/settings welcome` (admin only)
4. Or use the dashboard to configure all settings!

## Dashboard Features

The admin dashboard provides a web interface to manage your Discord bot:

- **Bot Profile**: Update the bot’s status and activity (e.g. “Watching …”) instantly; view links from `.env`
- **Server Settings**: Configure welcome messages, channels, and roles
- **Ticket Management**: View and manage support tickets
- **Invite Leaderboard**: Track invites with filtering options
- **Giveaway Management**: View and manage active giveaways
- **User Stats**: View detailed user statistics
- **Broadcast**: Send DMs to all server members

### Dashboard Setup

1. Make sure both bot and dashboard API are running
2. Navigate to `https://zippy-vibrancy.up.railway.app`
3. Login with Discord OAuth2
4. Select a server from the dropdown
5. Start managing your server!

## Project Structure

```
.
├── src/
│   ├── commands/       # Slash command handlers
│   ├── buttons/        # Button interaction handlers
│   ├── modals/         # Modal form handlers
│   ├── events/         # Discord event handlers
│   ├── models/         # Mongoose database schemas
│   ├── utils/          # Utility functions
│   ├── config/         # Configuration files
│   ├── dashboard/      # Dashboard API server
│   └── index.js        # Main bot entry point
├── dashboard/          # React frontend
│   ├── src/
│   │   ├── components/ # React components
│   │   ├── App.jsx     # Main app component
│   │   └── main.jsx    # Entry point
│   └── package.json    # Frontend dependencies
└── package.json        # Bot dependencies
```

## Database Collections

- `users` - User stats, XP, invites, verification
- `tickets` - Support tickets
- `giveaways` - Giveaway data
- `invitelogs` - Invite tracking logs
- `winners` - Giveaway winners (auto-deletes after 30 days)
- `serverconfigs` - Server configuration

## Commands

### User Commands

#### `/myinvites`
View your invite count.

**Usage:**
```
/myinvites
```

**Response:** Shows your total invites and valid invites.

---

#### `/mystats`
View your stats and leaderboard rank.

**Usage:**
```
/mystats
```

**Response:** Shows:
- Level & XP (with progress bar)
- Total and valid invites
- Tickets opened
- Leaderboard rank
- Verification status

---

#### `/invites leaderboard`
View the invite leaderboard.

**Usage:**
```
/invites leaderboard [limit:10-25]
```

**Options:**
- `limit` (optional): Number of users to show (1-25, default: 10)

**Response:** Shows top inviters with medals (🥇🥈🥉).

---

#### `/ticket close`
Close your support ticket.

**Usage:**
```
/ticket close
```

**Note:** Can only be used in a ticket channel. Closes the ticket and deletes the channel after 5 seconds.

---

### Admin Commands

#### `/verify`
Create verification button panel (Admin only).

**Usage:**
```
/verify
```

**Permissions:** Administrator or Admin role

**Response:** Creates an embed with a "Verify" button in the current channel.

---

#### `/ticketpanel`
Create support ticket panel (Admin only).

**Usage:**
```
/ticketpanel
```

**Permissions:** Administrator or Admin role

**Response:** Creates an embed with "Open Support Ticket" button.

---

#### `/giveaway create`
Create a new giveaway (Admin only).

**Usage:**
```
/giveaway create title:<title> description:<description> duration:<hours> prizes:<format> [allowed_roles:<ids>] [restricted_roles:<ids>]
```

**Options:**
- `title` (required): Giveaway title
- `description` (required): Giveaway description
- `duration` (required): Duration in hours (minimum: 1)
- `prizes` (required): Prizes format: `"Category1:Prize1:WinnerCount,Category2:Prize2:WinnerCount"`
  - Example: `"First Place:$100:1,Second Place:$50:2"`
- `allowed_roles` (optional): Comma-separated role IDs that can join
- `restricted_roles` (optional): Comma-separated role IDs that cannot join

**Example:**
```
/giveaway create title:"New Year Giveaway" description:"Win amazing prizes!" duration:24 prizes:"Gold:$100:1,Silver:$50:2,Bronze:$25:3"
```

**Response:** Creates a giveaway embed with a "Join Giveaway" button. Winners are automatically selected when the giveaway ends.

---

#### `/broadcast`
Send a DM to all server members (Admin only).

**Usage:**
```
/broadcast message:<message>
```

**Options:**
- `message` (required): Message to send to all members

**Permissions:** Administrator or Admin role

**Response:** Sends a DM to all server members (excluding bots). Shows success/failure count.

**Warning:** ⚠️ This sends a DM to ALL members. Use with caution!

---

#### `/settings welcome`
Configure welcome message (Admin only).

**Usage:**
```
/settings welcome message:<message> [channel:<channel>]
```

**Options:**
- `message` (required): Welcome message
  - Use `{user}` for username mention
  - Use `{server}` for server name
- `channel` (optional): Channel for welcome messages

**Example:**
```
/settings welcome message:"Welcome {user} to {server}! 🎉"
```

**Response:** Updates the welcome message configuration.

---

## Troubleshooting

### Bot not responding
- Check if bot is online
- Verify bot token is correct
- Check bot permissions in server

### Commands not showing
- Wait a few minutes for commands to register globally
- Restart the bot
- Check console for errors

### Invite tracking not working
- Make sure bot has "Manage Server" permission
- Check invite log channel ID is correct
- Verify MongoDB connection

### Tickets not creating
- Check ticket category ID
- Verify bot has "Manage Channels" permission
- Check MongoDB connection

### Dashboard not loading
- Ensure both bot and dashboard API are running
- Check `DASHBOARD_URL` and `DASHBOARD_PORT` in `.env`
- Verify Discord OAuth2 credentials are correct
- Check browser console for errors

### MongoDB connection errors
- Verify your IP is whitelisted in MongoDB Atlas (use `0.0.0.0/0` for testing)
- Check MongoDB connection string is correct
- Ensure MongoDB cluster is running

## License

ISC

## Support

For issues or questions, check the code comments or create an issue in the repository.
