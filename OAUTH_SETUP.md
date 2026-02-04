# Discord OAuth2 Setup Guide

## Fix "Invalid OAuth2_uri" Error

The error occurs because the callback URL in Discord Developer Portal doesn't match what's configured in your code.

## Step-by-Step Fix

### 1. Update Your .env File

Make sure your `.env` has:
```env
DASHBOARD_CALLBACK_URL=https://zippy-vibrancy.up.railway.app/auth/discord/callback
```

### 2. Configure Discord Developer Portal

1. Go to https://discord.com/developers/applications
2. Select your application (Client ID: 1429759235968733264)
3. Go to **OAuth2** section (left sidebar)
4. Scroll down to **Redirects**
5. Click **Add Redirect**
6. Add this **exact** URL:
   ```
   https://zippy-vibrancy.up.railway.app/auth/discord/callback
   ```
7. Click **Save Changes**

### 3. Important Notes

- The callback URL must point to your **backend API** (port 3001), NOT the frontend (port 5173)
- The frontend proxies `/auth` requests to the backend, but Discord redirects directly to the backend
- Make sure there are **no trailing slashes** in the URL
- The URL is case-sensitive

### 4. For Production

When deploying to production, update:
- `.env` file: `DASHBOARD_CALLBACK_URL=https://yourdomain.com/auth/discord/callback`
- Discord Developer Portal: Add the production callback URL
- Make sure your backend API is accessible at that URL

### 5. Restart Services

After updating:
1. Restart the dashboard API: `npm run dashboard`
2. The console will show the callback URL that needs to be added to Discord
3. Try logging in again

## Verification

When you start the dashboard API, you should see:
```
📊 Dashboard API server running on port 3001
🌐 Frontend URL: https://zippy-vibrancy.up.railway.app
🔗 OAuth Callback URL: https://zippy-vibrancy.up.railway.app/auth/discord/callback

⚠️  IMPORTANT: Add this callback URL to Discord Developer Portal:
   https://zippy-vibrancy.up.railway.app/auth/discord/callback
```

Make sure this exact URL is in your Discord Developer Portal redirects list!
