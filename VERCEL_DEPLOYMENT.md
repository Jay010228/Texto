# Vercel Deployment Guide

## Prerequisites
- Vercel account (free at vercel.com)
- Git repository with this code pushed to GitHub, GitLab, or Bitbucket

## Deployment Steps

### 1. Push to GitHub
```bash
git add .
git commit -m "Setup for Vercel deployment"
git push origin main
```

### 2. Import Project to Vercel
1. Go to [vercel.com](https://vercel.com)
2. Click "Add New" → "Project"
3. Select your repository
4. Click "Import"

### 3. Configure Environment Variables
In the Vercel dashboard, go to **Settings → Environment Variables** and add:

**From backend/.env:**
- `MONGODB_URI` - Your MongoDB connection string
- `JWT_SECRET` - Your JWT secret key
- `CLOUDINARY_CLOUD_NAME` - Cloudinary cloud name
- `CLOUDINARY_API_KEY` - Cloudinary API key
- `CLOUDINARY_API_SECRET` - Cloudinary API secret

**Optional Frontend Variables:**
- `VITE_API_URL` - Leave empty to use `/api` (recommended)
- `VITE_SERVER_URL` - Leave empty to use same domain

### 4. Deploy
Click "Deploy" and wait for the build to complete.

### 5. Verify Deployment
- Frontend: `https://your-vercel-app.vercel.app`
- Backend API: `https://your-vercel-app.vercel.app/api`

## Important Notes

⚠️ **Socket.io Limitations**: 
- Vercel serverless functions don't support persistent WebSocket connections for Socket.io.
- For production, consider using a dedicated server (Railway, Render, etc.) for the backend.

### Alternative: Deploy Backend Separately
If you need real-time features:
1. Deploy backend on [Railway.app](https://railway.app) or [Render.com](https://render.com)
2. Update `VITE_SERVER_URL` in Vercel to point to your backend server
3. Update `VITE_API_URL` to your backend API URL

## Troubleshooting

**API calls returning 404:**
- Check environment variables are set in Vercel dashboard
- Verify `vercel.json` routes are correct

**Socket.io not connecting:**
- Deploy backend separately on a service that supports persistent connections
- Update `VITE_SERVER_URL` environment variable

**Build failures:**
- Check that both `backend/package.json` and `frontend/package.json` exist
- Ensure all dependencies are properly listed
