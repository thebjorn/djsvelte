# Deployment Guide

This guide will help you deploy the Todo app to production.

## Architecture

- **Frontend**: Svelte 5 app deployed on Vercel
- **Backend**: Django REST API deployed on Railway (recommended) or Render

## Option 1: Deploy Frontend to Vercel + Backend to Railway (Recommended)

### Step 1: Deploy Backend to Railway

1. Create a [Railway](https://railway.app) account

2. Install Railway CLI (optional):
```bash
npm i -g @railway/cli
```

3. Create a new project:
   - Go to Railway dashboard
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Connect your repository
   - Railway will auto-detect Django

4. Configure environment variables in Railway:
   - `SECRET_KEY`: Generate a new secret key (use `python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"`)
   - `DEBUG`: `False`
   - `ALLOWED_HOSTS`: Add your Railway domain

5. Add a `railway.json` file (optional, for custom configuration):
```json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "python manage.py migrate && gunicorn backend.wsgi",
    "restartPolicyType": "ON_FAILURE"
  }
}
```

6. Railway will automatically:
   - Install dependencies from `requirements.txt`
   - Run migrations
   - Start the server with gunicorn

7. Note your Railway URL (e.g., `https://your-app.railway.app`)

### Step 2: Deploy Frontend to Vercel

1. Navigate to the frontend directory:
```bash
cd frontend
```

2. Login to Vercel:
```bash
vercel login
```

3. Deploy:
```bash
vercel
```

4. Follow the prompts:
   - Setup and deploy? Yes
   - Which scope? Your account
   - Link to existing project? No
   - Project name: (accept default or choose your own)
   - In which directory is your code located? `./`
   - Override settings? No

5. Set environment variable:
```bash
vercel env add VITE_API_URL
```
Enter your Railway backend URL with `/api` suffix (e.g., `https://your-app.railway.app/api`)

6. Redeploy to apply environment variable:
```bash
vercel --prod
```

Your app is now live!

## Option 2: Deploy Frontend to Vercel via Dashboard

1. Push your code to GitHub

2. Go to [Vercel Dashboard](https://vercel.com/new)

3. Import your repository

4. Configure:
   - **Framework Preset**: SvelteKit
   - **Root Directory**: `frontend`
   - **Build Command**: `npm run build`
   - **Output Directory**: (leave as default)
   - **Environment Variables**:
     - `VITE_API_URL`: Your Railway backend URL + `/api`

5. Click Deploy

## Option 3: Deploy Backend to Render

1. Create a [Render](https://render.com) account

2. Create a new Web Service:
   - Connect your GitHub repository
   - Name: your-app-backend
   - Environment: Python 3
   - Build Command: `pip install -r requirements.txt && python manage.py collectstatic --noinput && python manage.py migrate`
   - Start Command: `gunicorn backend.wsgi:application`

3. Add environment variables:
   - `SECRET_KEY`: Your Django secret key
   - `DEBUG`: `False`
   - `PYTHON_VERSION`: `3.11.0` (or your version)

4. Click "Create Web Service"

5. Use the Render URL for your `VITE_API_URL` in Vercel

## Testing Your Deployment

1. Visit your Vercel URL
2. Try creating a todo
3. Check if it persists after refresh
4. Test marking todos as complete/incomplete
5. Test deleting todos

## Troubleshooting

### Frontend can't connect to backend
- Check CORS settings in Django (`CORS_ALLOW_ALL_ORIGINS` should be `True` or configure specific origins)
- Verify `VITE_API_URL` environment variable is set correctly in Vercel
- Check browser console for errors

### Backend errors
- Check Railway/Render logs
- Verify all environment variables are set
- Ensure database migrations ran successfully
- Check `ALLOWED_HOSTS` includes your domain

### Static files not loading
- Run `python manage.py collectstatic` in production
- Verify WhiteNoise is configured correctly
- Check `STATIC_ROOT` and `STATIC_URL` settings

## Database

Currently using SQLite which works for development. For production:

### Railway with PostgreSQL

1. Add PostgreSQL plugin in Railway
2. Railway will automatically set `DATABASE_URL`
3. Install `psycopg2-binary`:
```bash
pip install psycopg2-binary
```
4. Update `settings.py` to use DATABASE_URL:
```python
import dj_database_url

DATABASES = {
    'default': dj_database_url.config(
        default=f'sqlite:///{BASE_DIR / "db.sqlite3"}',
        conn_max_age=600
    )
}
```

5. Add `dj-database-url` to requirements.txt

## Next Steps

- Set up proper SECRET_KEY management
- Configure domain name
- Add user authentication
- Set up monitoring and error tracking
- Configure CSRF settings for production
- Update CORS to only allow your frontend domain
