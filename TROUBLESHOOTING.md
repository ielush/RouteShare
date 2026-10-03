# Troubleshooting Guide

## Issue 1: Server Error When Opening Share Link

**What's happening:** When you open `https://routeshare.ielush.com/route/{id}`, you see a server error instead of the route.

**Causes and Solutions:**

### 1. Missing Environment Variables
The most common cause is that your Vercel deployment doesn't have the required environment variables set.

**Fix:**
1. Go to your Vercel dashboard
2. Select your RouteShare project
3. Go to **Settings → Environment Variables**
4. Add/verify these variables are set:
   ```
   NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
   NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=your_key
   NEXT_PUBLIC_MAPBOX_TOKEN=your_mapbox_token
   NEXT_PUBLIC_SITE_URL=https://routeshare.ielush.com
   ```
5. Redeploy after adding/updating variables

### 2. Supabase Connection Issues
Check if your Supabase database is accessible:

**Debug steps:**
- Verify the Supabase URL and key are correct
- Check that the `routes` table exists in Supabase
- Verify Row Level Security (RLS) policies allow anonymous reads:
  ```sql
  ALTER TABLE routes ENABLE ROW LEVEL SECURITY;
  
  CREATE POLICY "Enable read access for all users" ON routes
  FOR SELECT USING (true);
  ```
- Check Supabase logs for connection errors

### 3. Check Server Logs
Look at your Vercel deployment logs:
1. Vercel Dashboard → Your Project → Deployments
2. Click the latest deployment
3. Go to **Logs** tab
4. Filter for errors with the route ID

---

## Issue 2: Discord Preview Not Showing

**What's happening:** When you share `https://routeshare.ielush.com/route/{id}` in Discord, it doesn't show a preview (title, description, image).

**Causes and Solutions:**

### 1. NEXT_PUBLIC_SITE_URL Not Set
This is the most common cause. Discord uses Open Graph (OG) metadata to generate previews, and the OG image URL is built using this variable.

**Fix:**
1. Ensure `NEXT_PUBLIC_SITE_URL` is set in your Vercel environment variables
2. Redeploy after setting it
3. Wait a few minutes and try sharing again (Discord caches previews)

### 2. Mapbox Token Missing
Without a valid Mapbox token, the dynamic OG image can't be generated. Discord will show the fallback.

**Fix:**
1. Set `NEXT_PUBLIC_MAPBOX_TOKEN` in Vercel environment variables
2. Make sure the token is valid and has permissions for Static Images API
3. Redeploy

### 3. Discord Cache
Discord caches previews. If you've already shared a link without proper OG data:

**Fix:**
- Clear Discord's cache:
  1. Use Discord's link unfurl tool: https://discordapp.com/api/oauth2/authorize
  2. Or use an online preview checker: https://www.opengraph.xyz/
- Share the link again after a few minutes

### 4. Route Doesn't Exist in Database
If the route ID doesn't exist or the data is corrupted:

**Fix:**
1. Generate and save a new route
2. Copy the share link and test it
3. Check Supabase to verify the data exists

---

## Testing Discord Previews Locally

Before deploying, test OG metadata locally:

1. **Using og-debugger:**
   - Deploy a test version or use a temporary domain
   - Visit: https://www.opengraph.xyz/
   - Enter your route URL
   - See what metadata is being read

2. **Using curl:**
   ```bash
   curl -I "https://routeshare.ielush.com/route/{id}"
   # Check for og: meta tags in headers
   ```

3. **Using browser DevTools:**
   - Open the page in browser
   - Right-click → View Page Source
   - Search for `<meta property="og:` tags

---

## Quick Checklist

Before sharing links, verify:

- [ ] Route was successfully saved (you got a share link in the app)
- [ ] `NEXT_PUBLIC_SITE_URL` is set to `https://routeshare.ielush.com`
- [ ] `NEXT_PUBLIC_SUPABASE_URL` and key are correct
- [ ] `NEXT_PUBLIC_MAPBOX_TOKEN` is set (for dynamic OG images)
- [ ] You redeployed after updating environment variables
- [ ] The route ID exists in your Supabase database
- [ ] Supabase RLS policies allow public read access

---

## Getting Help

If issues persist:

1. Check Vercel deployment logs for specific errors
2. Check Supabase logs for connection errors
3. Verify all environment variables in Vercel Settings
4. Try creating a new test route to isolate the issue
5. Check that your domain DNS is correctly pointed to Vercel
