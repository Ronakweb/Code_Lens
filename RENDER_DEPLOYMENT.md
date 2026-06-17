# 🚀 Render Deployment Guide

This guide walks you through deploying your FastAPI backend and Next.js frontend to Render.

## 📁 Project Structure

```
Code-Lens/
├── backend/                 # FastAPI application
│   ├── main.py
│   ├── requirements.txt
│   ├── Dockerfile          # ✅ Created
│   └── app/
├── frontend/               # Next.js application
│   ├── package.json
│   ├── Dockerfile          # ✅ Created
│   ├── nginx.conf          # ✅ Created
│   ├── .env.production     # ✅ Created
│   └── src/
└── RENDER_DEPLOYMENT.md    # ✅ This file
```

## 🔧 1. Deploy FastAPI Backend

### Option A: Using Docker (Recommended)

1. **Go to Render Dashboard** → **New** → **Web Service**
2. **Connect your GitHub repository**
3. **Configure the service:**
   - **Name**: `code-lens-backend`
   - **Root Directory**: `backend`
   - **Runtime**: `Docker`
   - **Region**: Choose closest to your users

4. **Environment Variables** (Add these in Render dashboard):
   ```
   GOOGLE_API_KEY=your_google_api_key_here
   ALLOWED_ORIGINS=["https://your-frontend-url.onrender.com"]
   ```

### Option B: Without Docker

1. **Same steps as above, but choose:**
   - **Runtime**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`

### 🔗 Your backend will be available at:
```
https://code-lens-backend.onrender.com
```

## 🎨 2. Deploy Next.js Frontend

### For Static Site Deployment (Recommended)

1. **Go to Render Dashboard** → **New** → **Static Site**
2. **Connect your GitHub repository**
3. **Configure the site:**
   - **Name**: `code-lens-frontend`
   - **Root Directory**: `frontend`
   - **Build Command**: `npm install && npm run build`
   - **Publish Directory**: `.next` (for Next.js) or `out` (for static export)

4. **Environment Variables**:
   ```
   NEXT_PUBLIC_API_URL=https://code-lens-backend.onrender.com
   NEXT_TELEMETRY_DISABLED=1
   ```

### For Web Service Deployment (if you need SSR)

1. **Choose Web Service instead of Static Site**
2. **Configure:**
   - **Runtime**: `Node`
   - **Build Command**: `npm install && npm run build`
   - **Start Command**: `npm start`

### 🔗 Your frontend will be available at:
```
https://code-lens-frontend.onrender.com
```

## 🔄 3. Connect Frontend ↔ Backend

### Update CORS Origins

After deploying, update your backend's CORS origins:

1. **In your `.env` file or Render environment variables:**
   ```
   ALLOWED_ORIGINS=["https://code-lens-frontend.onrender.com"]
   ```

2. **Redeploy your backend service**

### Update Frontend API URL

1. **In Render dashboard, update the frontend environment variable:**
   ```
   NEXT_PUBLIC_API_URL=https://code-lens-backend.onrender.com
   ```

2. **Redeploy your frontend service**

## ⚙️ 4. Important Render Configuration

### Backend Requirements
- ✅ Listen on `0.0.0.0` (not `127.0.0.1`)
- ✅ Use `$PORT` environment variable or default to `10000`
- ✅ Include all dependencies in `requirements.txt`

### Frontend Requirements
- ✅ Build command generates static files
- ✅ Environment variables use `NEXT_PUBLIC_` prefix
- ✅ API calls use absolute URLs

### Free Tier Limitations
- 🔔 Services sleep after 15 minutes of inactivity
- 🔔 Static sites stay always active
- 🔔 750 hours/month per service
- 🔔 500MB RAM limit

## 🚨 Troubleshooting

### Backend Issues
```bash
# Check logs in Render dashboard
# Common issues:
# - Wrong PORT configuration
# - Missing environment variables
# - CORS errors
```

### Frontend Issues
```bash
# Check build logs
# Common issues:
# - Wrong build command
# - Missing environment variables
# - API URL pointing to wrong backend
```

### Testing Deployment
```bash
# Test backend health
curl https://code-lens-backend.onrender.com/health

# Test frontend
curl https://code-lens-frontend.onrender.com
```

## 🔐 Security Notes

1. **Never commit API keys** - Use Render environment variables
2. **Update CORS origins** - Don't leave as `["*"]` in production
3. **Use HTTPS** - Render provides SSL certificates automatically
4. **Environment separation** - Use different services for staging/production

## 📝 Deployment Checklist

- [ ] Backend Dockerfile created
- [ ] Frontend Dockerfile created (optional)
- [ ] Environment variables configured
- [ ] CORS origins updated
- [ ] GitHub repository connected to Render
- [ ] Backend service deployed and healthy
- [ ] Frontend service deployed and accessible
- [ ] Frontend can communicate with backend
- [ ] Custom domain configured (optional)

## 🔄 Continuous Deployment

Render automatically deploys when you push to your connected GitHub branch:

- **Main branch** → **Production deployment**
- **Staging branch** → **Staging deployment** (if configured)

---

**Need help?** Check the [Render documentation](https://render.com/docs) or your deployment logs in the Render dashboard.