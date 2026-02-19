# FormCraft — Weighted Form Builder

A full-stack form builder with Google Auth, weighted scoring, analytics, and shareable links.

---

## Features
- ✅ Google OAuth login (real Gmail sign-in)
- ✅ Form builder: Multiple Choice, Checkboxes, Dropdown, Linear Scale, Text, File Upload
- ✅ Weighted answer options (assign point values per option)
- ✅ Customizable score thresholds → conclusions (e.g. ≤15 = Conservative)
- ✅ Form templates (Risk Assessment, Survey, Quiz, Blank)
- ✅ Customizable themes (6 color themes)
- ✅ Publish & share forms via unique link (/f/xxxxxxxx)
- ✅ Response viewing with analytics
- ✅ Required/optional question toggle
- ✅ Edit, duplicate, delete forms

---

## Setup

### 1. Google OAuth Setup
1. Go to https://console.cloud.google.com/
2. Create a project → APIs & Services → Credentials
3. Create OAuth 2.0 Client ID (Web application)
4. Add Authorized JavaScript origins:
   - `http://localhost:3000` (dev)
   - `https://your-domain.com` (prod)
5. Add Authorized redirect URIs:
   - `http://localhost:3000`
   - `https://your-domain.com`
6. Copy your Client ID

### 2. Backend Setup
```bash
cd backend
pip install -r requirements.txt

# Create .env file
echo "GOOGLE_CLIENT_ID=your-client-id.apps.googleusercontent.com" > .env
echo "DB_PATH=formcraft.db" >> .env

# Run
uvicorn main:app --reload --port 8000
```

### 3. Frontend Setup
```bash
cd frontend

# Create .env file
echo "REACT_APP_API_URL=http://localhost:8000" > .env
echo "REACT_APP_GOOGLE_CLIENT_ID=your-client-id.apps.googleusercontent.com" >> .env

npm install
npm start
```

The app will be at http://localhost:3000

---

## Deployment (Production)

### Backend — Deploy to Railway / Render / Fly.io
1. Push your code to GitHub
2. Connect to Railway.app or Render.com
3. Set environment variables:
   - `GOOGLE_CLIENT_ID`
   - `DB_PATH` (use `/data/formcraft.db` for persistent disk)
4. Start command: `uvicorn main:app --host 0.0.0.0 --port $PORT`

### Frontend — Deploy to Vercel / Netlify
1. Build: `npm run build`
2. Deploy the `build/` folder
3. Set environment variables:
   - `REACT_APP_API_URL` = your backend URL
   - `REACT_APP_GOOGLE_CLIENT_ID` = your Google Client ID
4. Add a redirect rule so all routes go to `index.html`:
   - Netlify: `_redirects` file with `/* /index.html 200`
   - Vercel: `vercel.json` with rewrites

### Update Google OAuth
After deploying, add your production domain to:
- Authorized JavaScript origins
- Authorized redirect URIs
in Google Cloud Console.

---

## Scoring Formula
The scoring works as follows:
- **Multiple Choice**: Selected option's weight is added to total
- **Checkboxes**: All selected options' weights are summed
- **Linear Scale**: Selected value × scale_weight is added

Conclusions are determined by score thresholds you define per form:
- Score ≤ 15 → "Conservative"
- Score ≤ 24 → "Moderately Conservative"
- etc.

The last threshold should have a very high max (e.g. 9999) to catch all remaining scores.

---

## File Structure
```
formcraft/
├── backend/
│   ├── main.py          # FastAPI app
│   └── requirements.txt
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── App.js       # All React components
│   │   └── index.js
│   └── package.json
└── .env.example
```
