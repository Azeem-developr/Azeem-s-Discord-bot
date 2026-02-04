# Quick Start Guide

## Dashboard is Already Running!

If you see `EADDRINUSE: address already in use :::3001`, it means the dashboard API server is **already running** in another terminal.

## Check if Dashboard is Running

**Option 1: Check your terminals**
- Look for a terminal running `npm run dashboard`
- If you see it, the server is already running!

**Option 2: Test the connection**
- Open https://zippy-vibrancy.up.railway.app/api/auth/me in your browser
- If you get a response (even an error), the server is running

## If You Want to Restart the Dashboard

### Kill the existing process:

**PowerShell:**
```powershell
Stop-Process -Id 16836 -Force
```

**Or find and kill:**
```powershell
Get-Process | Where-Object {$_.Id -eq 16836} | Stop-Process -Force
```

Then run:
```bash
npm run dashboard
```

## Complete Startup Sequence

1. **Terminal 1 - Bot:**
   ```bash
   npm start
   ```

2. **Terminal 2 - Dashboard API:**
   ```bash
   npm run dashboard
   ```
   (If you see "EADDRINUSE", it's already running - skip this step!)

3. **Terminal 3 - Dashboard Frontend:**
   ```bash
   cd dashboard
   npm run dev
   ```

4. **Open Browser:**
   - Go to https://zippy-vibrancy.up.railway.app
   - Login with Discord

## Troubleshooting

### Port 3001 Already in Use
- The dashboard API is already running
- You can use it as-is, or kill the process and restart

### Port 5173 Already in Use
- The frontend dev server is already running
- Just open https://zippy-vibrancy.up.railway.app in your browser

### MongoDB Connection Errors
- The dashboard will work without MongoDB
- Database features will show empty data
- Fix MongoDB connection to enable full functionality
