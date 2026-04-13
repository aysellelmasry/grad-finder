# Complete Deployment Guide

## Overview
- **Backend**: Flask API on Replit
- **Frontend**: HTML/CSS/JS on Vercel
- **Communication**: Frontend fetches from backend API

---

## Step 1: Deploy Backend to Replit

### 1.1 Prepare Your Repository
```bash
# Already done in your local repo:
# - .replit file (configures Replit environment)
# - Procfile (for production deployment)
# - requirements.txt (Python dependencies)
# - .env.example (environment variable template)
```

### 1.2 Create Replit Project
1. Go to https://replit.com and sign in with GitHub
2. Click **"Create"** → **"Import from GitHub"**
3. Paste your repository URL and click **"Import"**
4. Replit will auto-detect Python and create the project

### 1.3 Upload Data Files to Replit
1. In Replit, click the **Files** icon (left sidebar)
2. Click **"Upload file"** and select:
   - `face_encodings.pkl`
   - `photos_metadata.pkl`
   - `gdrive_file_mappingf.json`
3. Upload these to the `backend/` folder

### 1.4 Configure Environment Variables
1. Click the **Secrets** icon (lock icon on left sidebar)
2. Add the following environment variables:

```
ENCODINGS_FILE=face_encodings.pkl
METADATA_FILE=photos_metadata.pkl
GDRIVE_FILE=gdrive_file_mappingf.json
TOLERANCE=0.52
MAX_UPLOAD_MB=16
ALLOWED_ORIGINS=*
FLASK_DEBUG=False
```

**Note**: After deploying the frontend, update `ALLOWED_ORIGINS` to your Vercel URL:
```
ALLOWED_ORIGINS=https://your-app.vercel.app
```

### 1.5 Start the Backend
1. Click **"Run"** button (top)
2. Replit will install dependencies and start the server
3. You'll see a URL like: `https://[replit-name].repl.co`
4. Test it: Visit `https://[replit-name].repl.co/health` (if you have a health endpoint) or check console for startup messages

**Keep this URL for Step 3.**

---

## Step 2: Deploy Frontend to Vercel

### 2.1 Create Vercel Project
1. Go to https://vercel.com and sign in with GitHub
2. Click **"Add New..."** → **"Project"**
3. Select your repository
4. Vercel will detect it's a static HTML site

### 2.2 Configure Build Settings
- **Framework**: Select "Other" (HTML/CSS/JS)
- **Build Command**: Leave empty or `echo "No build needed"`
- **Output Directory**: `.`
- **Root Directory**: `frontend`

### 2.3 Add Environment Variables
1. In project settings, go to **"Environment Variables"**
2. Add:
   - **Name**: `REACT_APP_API_BASE`
   - **Value**: `https://[replit-name].repl.co` (from Step 1.5)
   - Click **"Add"** and **"Save"**

### 2.4 Deploy
1. Click **"Deploy"**
2. Wait for deployment to complete
3. You'll get a URL: `https://[project-name].vercel.app`

**Keep this URL for Step 3.**

---

## Step 3: Update Backend CORS Settings

### 3.1 Go Back to Replit
1. Open your Replit project
2. Click **Secrets** (lock icon)
3. Update `ALLOWED_ORIGINS` to:
   ```
   https://[project-name].vercel.app
   ```
4. The backend will restart automatically or you can restart it manually

---

## Step 4: Test the Integration

### 4.1 Test Frontend
1. Open your Vercel URL: `https://[project-name].vercel.app`
2. Try uploading a photo
3. You should see API calls going to your Replit backend
4. Results should display on the page

### 4.2 Troubleshoot if Needed

**CORS Error in Browser Console**:
- Check that `ALLOWED_ORIGINS` in Replit exactly matches your Vercel URL
- Make sure there are no extra spaces or trailing slashes

**API Timeout**:
- Replit free tier can be a bit slow; wait a few seconds
- If timeout continues, check Replit logs for errors

**404 on Images**:
- Ensure Google Drive files are accessible
- Check `gdrive_file_mappingf.json` is uploaded

---

## Environment Variables Reference

### Backend (.env or Replit Secrets):
| Variable | Default | Description |
|----------|---------|-------------|
| `ENCODINGS_FILE` | `face_encodings.pkl` | Path to face encodings file |
| `METADATA_FILE` | `photos_metadata.pkl` | Path to metadata file |
| `GDRIVE_FILE` | `gdrive_file_mappingf.json` | Path to Google Drive mapping |
| `TOLERANCE` | `0.52` | Face match tolerance (0-1) |
| `MAX_UPLOAD_MB` | `16` | Max upload size in MB |
| `ALLOWED_ORIGINS` | `*` | CORS origins (space or comma separated) |
| `FLASK_DEBUG` | `False` | Enable debug mode (don't use in production) |
| `PORT` | `5000` | Server port (Replit sets this) |

### Frontend (.env or Vercel Environment Variables):
| Variable | Default | Description |
|----------|---------|-------------|
| `REACT_APP_API_BASE` | `http://localhost:5000` | Backend API URL |

---

## Quick URLs Reference

After deployment, save these:

- **Backend API**: `https://[replit-name].repl.co`
- **Frontend App**: `https://[project-name].vercel.app`
- **API Endpoint for searching**: `https://[replit-name].repl.co/search-face`

---

## Common Issues & Fixes

### Issue: "Cannot reach backend" error in frontend

**Solution**:
1. Verify Replit URL is correct and running (visit it in browser)
2. Check Replit console for errors
3. Ensure `ALLOWED_ORIGINS` includes your Vercel domain

### Issue: Pickle file not found error

**Solution**:
1. Verify files are uploaded to Replit file system
2. Check file names match exactly (case-sensitive)
3. Look in backend folder, not root

### Issue: CORS error: "Access-Control-Allow-Origin header"

**Solution**:
1. Go to Replit Secrets
2. Set `ALLOWED_ORIGINS=https://your-vercel-app.vercel.app`
3. Restart backend or wait for auto-restart

### Issue: Face recognition taking too long

**Solution**:
- This is normal for free Replit tier
- Increase timeout in frontend if needed
- Consider upgrading Replit for better performance

---

## Production Tips

1. **Keep data secure**: Don't commit pickle files to GitHub (use .gitignore)
2. **Monitor costs**: Replit free tier has usage limits; consider paid tier for production
3. **Uptime**: Replit free projects sleep after inactivity; consider paid tier for always-on
4. **Scaling**: For large deployments, consider Render.com or Railway.app as Replit alternatives

