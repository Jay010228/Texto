# Complete End-to-End Deployment Guide

## ✅ Will This Monorepo Work for Both Frontend & Backend?

**Short Answer:** Not ideally for production due to Socket.io limitations.

**Details:**
- ✅ Yes, Vercel's `vercel.json` config supports both in one project
- ✅ Frontend builds and deploys correctly
- ❌ Backend Node.js server won't run persistently on Vercel serverless
- ❌ Socket.io (real-time features) won't work on serverless functions

**Recommendation:**
- Deploy **Frontend on Vercel** (this monorepo setup)
- Deploy **Backend separately** on Railway/Render/Fly.io (supports persistent Node.js servers)

---

## 📋 Step-by-Step End-to-End Commands

### Phase 1: Local Setup & Testing
```bash
# 1. Navigate to project
cd "c:\project\textogram-main (3)\textogram-main"

# 2. Install backend dependencies
cd backend
npm install

# 3. Install frontend dependencies
cd ..\frontend
npm install
cd ..

# 4. Create backend .env (if not already done)
# Already created at backend\.env

# 5. Seed the database with test data
cd backend
npm run seed
cd ..

# 6. Start backend (in one terminal)
cd backend
npm run dev

# 7. Start frontend (in another terminal)
cd frontend
npm run dev
```

### Phase 2: Git Setup
```bash
# Initialize git (if not already done)
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Textogram chat app"

# Create remote repository on GitHub, then:
git remote add origin https://github.com/YOUR_USERNAME/textogram.git
git branch -M main
git push -u origin main
```

### Phase 3: Deploy Backend (Railway.app - Recommended)

#### 3a. Create Railway Account
- Go to [railway.app](https://railway.app)
- Sign up with GitHub
- Create new project → Deploy from GitHub repo

#### 3b. Deploy via Railway CLI
```bash
# Install Railway CLI
npm install -g @railway/cli

# Login to Railway
railway login

# Link project
railway link

# Add environment variables
railway variables add MONGODB_URI="your_mongodb_uri"
railway variables add JWT_SECRET="your_jwt_secret"
railway variables add CLOUDINARY_CLOUD_NAME="your_cloud_name"
railway variables add CLOUDINARY_API_KEY="your_api_key"
railway variables add CLOUDINARY_API_SECRET="your_api_secret"
railway variables add NODE_ENV="production"

# Deploy backend
railway up

# Get backend URL
railway open
```

### Phase 4: Deploy Frontend on Vercel

#### 4a. Via Vercel CLI
```bash
# Navigate to project root (not frontend folder)
cd "c:\project\textogram-main (3)\textogram-main"

# Deploy
vercel --prod

# When prompted:
# - "Set up and deploy?" → Yes
# - "Which scope?" → Your account
# - "Link to existing project?" → No
# - "Project name?" → textogram
# - "Root directory?" → ./
# - "Want to override settings?" → No
```

#### 4b. Add Environment Variables to Vercel
```bash
# Add backend API URL (from Railway)
vercel env add VITE_API_URL
# Paste: https://your-railway-backend.up.railway.app/api

# Add backend Socket.io URL (same as above)
vercel env add VITE_SERVER_URL
# Paste: https://your-railway-backend.up.railway.app

# Redeploy with new env vars
vercel --prod
```

---

## 🔧 Update Backend CORS (Important!)

Once you have your URLs, update `backend/src/index.js`:

```javascript
app.use(cors({
    origin: ["http://localhost:5173", "https://your-vercel-frontend.vercel.app"],
    credentials: true,
}));
```

Then redeploy backend:
```bash
cd backend
git add .
git commit -m "Update CORS for production URLs"
git push
railway up
```

---

## 💾 Environment Variables Reference

### Backend (Railway)
```
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/textogram
JWT_SECRET=your-super-secret-key-here
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
NODE_ENV=production
PORT=5001
```

### Frontend (Vercel)
```
VITE_API_URL=https://your-railway-backend.up.railway.app/api
VITE_SERVER_URL=https://your-railway-backend.up.railway.app
```

---

## 🚀 Final URLs After Deployment

- **Frontend:** `https://your-vercel-app.vercel.app`
- **Backend API:** `https://your-railway-backend.up.railway.app/api`
- **Socket.io:** `https://your-railway-backend.up.railway.app`

---

## ✨ Verification Checklist

- [ ] MongoDB connection works
- [ ] Users can sign up/login
- [ ] Chat messages send correctly
- [ ] Online users list updates (Socket.io)
- [ ] Profile pictures upload (Cloudinary)
- [ ] Responsive on mobile

---

## ⚠️ Important Notes

1. **Socket.io on Vercel:** Doesn't work with serverless → Deploy backend separately
2. **Free Tier Limits:**
   - Vercel: 100 GB bandwidth/month, 12 serverless function execution hours
   - Railway: $5 free credits/month
3. **Keep .env files secret:** Never commit to GitHub
4. **Update CORS:** Before going to production with real URLs

---

## 📞 Troubleshooting

| Issue | Solution |
|-------|----------|
| API 404 errors | Check `VITE_API_URL` in Vercel env vars |
| Socket.io not connecting | Deploy backend on Railway, not Vercel |
| CORS errors | Update backend CORS origins |
| Cloudinary upload fails | Verify credentials in Railway env vars |
| Build fails on Vercel | Check `vercel.json` is in root directory |
