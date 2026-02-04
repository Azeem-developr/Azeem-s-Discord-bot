# Dashboard Setup Guide

## Quick Start

### 1. Install Dependencies

```bash
# Root directory
npm install

# Dashboard directory
cd dashboard
npm install
cd ..
```

### 2. Configure Environment Variables

Copy `.env.example` to `.env` and fill in:

- `DISCORD_CLIENT_ID` - From Discord Developer Portal → OAuth2
- `DISCORD_CLIENT_SECRET` - From Discord Developer Portal → OAuth2
- `PUBLIC_BASE_URL` - Your public domain (default: https://zippy-vibrancy.up.railway.app)
- `DASHBOARD_URL` - Your dashboard frontend URL (default: https://zippy-vibrancy.up.railway.app)
- `PORT` - Server port (Railway sets this automatically in production)
- `SESSION_SECRET` - Random string for session encryption

### 3. Set Up Discord OAuth2

1. Go to https://discord.com/developers/applications
2. Select your application
3. Go to **OAuth2** section
4. Add redirect URI: `https://zippy-vibrancy.up.railway.app/auth/discord/callback`
5. Copy **Client ID** and **Client Secret**

### 4. Start Services

**Terminal 1 - Bot:**
```bash
npm start
```

**Terminal 2 - Dashboard API:**
```bash
npm run dashboard
```

**Terminal 3 - Dashboard Frontend:**
```bash
cd dashboard
npm run dev
```

### 5. Access Dashboard

Open https://zippy-vibrancy.up.railway.app in your browser and login with Discord.

## Production Deployment

### Backend (Dashboard API)

1. Set environment variables on your server
2. Use PM2 or similar to run: `node src/dashboard/server.js`
3. Set up reverse proxy (nginx) to forward `/api` and `/auth` to port 3001

### Frontend

1. Build the React app:
```bash
cd dashboard
npm run build
```

2. Serve the `dist` folder with a static file server or CDN
3. Update `DASHBOARD_URL` in `.env` to your production URL

### Environment Variables for Production

```env
PUBLIC_BASE_URL=https://zippy-vibrancy.up.railway.app
DASHBOARD_URL=https://zippy-vibrancy.up.railway.app
DASHBOARD_CALLBACK_URL=https://zippy-vibrancy.up.railway.app/auth/discord/callback
SESSION_SECRET=strong-random-secret-here
NODE_ENV=production
```

## Troubleshooting

### OAuth2 Redirect Error
- Check redirect URI matches exactly in Discord Developer Portal
- Verify `DASHBOARD_URL` is correct
- Check browser console for errors

### API Connection Errors
- Ensure dashboard API is running on port 3001
- Check CORS settings in `server.js`
- Verify proxy configuration in `vite.config.js`

### Bot Client Not Available
- Make sure bot is running before dashboard API
- Check that bot client is properly exported from `src/index.js`
