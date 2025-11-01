# Todo App - Django + Svelte 5

A full-stack todo application built with Django REST Framework backend and Svelte 5 frontend.

## Features

- Create, read, update, and delete todos
- Mark todos as complete/incomplete
- Real-time UI updates
- Clean and responsive design
- Django REST API backend
- Svelte 5 with TypeScript frontend

## Tech Stack

**Backend:**
- Django 5.2
- Django REST Framework
- django-cors-headers
- SQLite database

**Frontend:**
- Svelte 5 (with runes)
- SvelteKit
- TypeScript
- Vite

## Local Development

### Backend Setup

1. Install Python dependencies:
```bash
pip install -r requirements.txt
```

2. Run migrations:
```bash
python manage.py migrate
```

3. Start the Django development server:
```bash
python manage.py runserver
```

The API will be available at `http://localhost:8000/api/`

### Frontend Setup

1. Navigate to the frontend directory:
```bash
cd frontend
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

The frontend will be available at `http://localhost:5173/`

## API Endpoints

- `GET /api/todos/` - List all todos
- `POST /api/todos/` - Create a new todo
- `GET /api/todos/{id}/` - Retrieve a specific todo
- `PATCH /api/todos/{id}/` - Update a todo
- `DELETE /api/todos/{id}/` - Delete a todo

## Deployment to Vercel

### Deploy Frontend (SvelteKit)

1. Push your code to a Git repository (GitHub, GitLab, etc.)

2. Go to [Vercel](https://vercel.com) and import your repository

3. Configure the project:
   - **Framework Preset**: SvelteKit
   - **Root Directory**: `frontend`
   - **Build Command**: `npm run build`
   - **Output Directory**: `.svelte-kit`

4. Add environment variable:
   - `VITE_API_URL`: Your Django backend URL (e.g., `https://your-backend.vercel.app/api`)

5. Deploy!

### Deploy Backend (Django)

**Note**: Deploying Django on Vercel has limitations as it's designed for serverless functions. For production, consider using:
- **Railway** (recommended for Django)
- **Render**
- **PythonAnywhere**
- **DigitalOcean App Platform**
- **Heroku**

For a simple Vercel deployment (with limitations):

1. Ensure `vercel.json` is in your root directory
2. Push to your Git repository
3. Import to Vercel
4. Configure:
   - **Framework Preset**: Other
   - **Root Directory**: `.` (root)
   - **Build Command**: `pip install -r requirements.txt`

5. Add environment variables:
   - `SECRET_KEY`: Your Django secret key
   - `DEBUG`: `False`

**Alternative**: Deploy backend separately on Railway or Render, and frontend on Vercel.

## Project Structure

```
djsvelte/
├── backend/              # Django project settings
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── todos/               # Django todos app
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   └── urls.py
├── frontend/            # Svelte frontend
│   ├── src/
│   │   └── routes/
│   │       └── +page.svelte
│   ├── package.json
│   └── svelte.config.js
├── requirements.txt
├── manage.py
└── README.md
```

## License

MIT
