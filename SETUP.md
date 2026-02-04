# Setup Guide

## Prerequisites

- Node.js 18+ installed
- MongoDB Atlas account (or local MongoDB)
- Discord Bot Token
- Discord Server with admin permissions

## Step 1: Install Dependencies

```bash
npm install
```

## Step 2: Configure Environment Variables

Edit the `.env` file and set the following:

```env
DISCORD_BOT_TOKEN=your_bot_token_here
MONGO_URI=your_mongodb_connection_string

# After adding bot to server, get these IDs and add them:
VERIFIED_ROLE_ID=
WELCOME_CHANNEL_ID=
VERIFICATION_CHANNEL_ID=
TICKET_CATEGORY_ID=
INVITE_LOG_CHANNEL_ID=
ADMIN_ROLE_ID=
```

## Step 3: Get Discord IDs

1. **Enable Developer Mode** in Discord (User Settings → Advanced → Developer Mode)
2. Right-click on roles/channels to copy their IDs
3. Update the `.env` file with the IDs

## Step 4: Invite Bot to Server

1. Go to https://discord.com/developers/applications
2. Select your bot application
3. Go to OAuth2 → URL Generator
4. Select scopes: `bot`, `applications.commands`
5. Select bot permissions:
   - Manage Channels
   - Manage Roles
   - View Channels
   - Send Messages
   - Read Message History
   - Embed Links
   - Attach Files
   - Use Slash Commands
6. Copy the generated URL and open it in your browser
7. Select your server and authorize

## Step 5: Configure Server

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

## Step 6: Run the Bot

```bash
npm start
```

For development with auto-reload:
```bash
npm run dev
```

## Step 7: Initial Setup Commands

1. In the verification channel, run `/verify` (admin only) to create the verification button
2. In a channel, run `/ticketpanel` (admin only) to create the ticket panel
3. Configure welcome message: `/settings welcome` (admin only)

## Features Overview

### User Features
- `/verify` - Get verification button (admin)
- `/myinvites` - View your invite count
- `/mystats` - View your stats and leaderboard rank
- `/invites leaderboard` - View invite leaderboard
- `/ticket close` - Close your ticket

### Admin Features
- `/giveaway create` - Create a new giveaway
- `/broadcast` - Send DM to all server members
- `/settings welcome` - Configure welcome message
- `/ticketpanel` - Create ticket panel

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

## Database Collections

- `users` - User stats, XP, invites, verification
- `tickets` - Support tickets
- `giveaways` - Giveaway data
- `invitelogs` - Invite tracking logs
- `winners` - Giveaway winners (auto-deletes after 30 days)
- `serverconfigs` - Server configuration

## Support

For issues or questions, check the code comments or create an issue in the repository.
