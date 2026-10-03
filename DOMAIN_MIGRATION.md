# Setup Guide: routeshare.vivienne.work

## What Changed

### 1. Code Fix: Share Link Button Logic
The "Generate Share Link" button now displays correctly based on the route mode:

**Manual Mode:**
- Button always appears (disabled until a route is drawn)
- Users can generate share links for custom drawn routes

**Automatic Mode:**
- Button only appears AFTER a route is successfully generated
- Clicking "Generate Route" generates the auto route first
- Then the "Generate Share Link" button becomes available

### 2. Domain References
- **OG Link Generation:** Uses `NEXT_PUBLIC_SITE_URL` environment variable
- **Share Links:** Built with `${window.location.origin}/route/${shareId}`
- **Current Domain:** `routeshare.vivienne.work`

## Setup for Deployment

### Local Development

Create a `.env.local` file in the project root:

```bash
# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=your_key

# Mapbox
NEXT_PUBLIC_MAPBOX_TOKEN=your_token

# Site Domain for OG Previews
NEXT_PUBLIC_SITE_URL=https://routeshare.vivienne.work
```

### Vercel/Production Deployment

Update these environment variables in your deployment platform:

1. **Vercel Dashboard:**
   - Go to Settings → Environment Variables
   - Update or add: `NEXT_PUBLIC_SITE_URL=https://routeshare.vivienne.work`
   
2. **Domain Configuration:**
   - Point DNS for `routeshare.ielush.com` to your Vercel deployment
   - Add the custom domain to Vercel project settings

3. **Supabase:** (if not already done)
   - No changes needed for database
   - Ensure RLS policies allow your new domain if you have CORS restrictions

## Testing

After deployment:

1. Create a test route
2. Generate a share link
3. Copy and share the link
4. Verify the link works and displays correctly
5. Check social media preview (Open Graph) by sharing on Twitter/Discord/etc.

## Important Notes

- The `.env.local` file should NOT be committed (it's in `.gitignore`)
- Share links use `window.location.origin` so they work on any domain
- OG previews use the `NEXT_PUBLIC_SITE_URL` for generating preview images
- Old links from `routeshare.vivienne.work` will not redirect (you may need to set up redirects)

## Reverting (if needed)

Simply update `NEXT_PUBLIC_SITE_URL` back to the old domain in your environment variables.
