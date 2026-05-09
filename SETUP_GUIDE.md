# Music Making Dreams - Setup Guide

## 📋 Prerequisites

- **Node.js** 16+ ([Download](https://nodejs.org/))
- **PostgreSQL** 12+ ([Download](https://www.postgresql.org/download/))
- **Docker & Docker Compose** (Optional, for containerized setup)
- **Git** ([Download](https://git-scm.com/))

## 🚀 Quick Start - Local Development

### 1. Clone Repository
```bash
git clone https://github.com/fiorecarp07/Musicmakingdreams.git
cd Musicmakingdreams
```

### 2. Setup Database

#### Option A: Using PostgreSQL CLI
```bash
# Create database
psql -U postgres -d postgres -c "CREATE DATABASE musicmakingdreams;"

# Run schema
psql -U postgres -d musicmakingdreams -f server/db/schema.sql
```

#### Option B: Using pgAdmin
1. Open pgAdmin
2. Create new database named `musicmakingdreams`
3. Execute `server/db/schema.sql` in the query tool

### 3. Setup Environment Variables

```bash
# Copy environment template
cp .env.example .env
```

Edit `.env`:
```env
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=musicmakingdreams
DB_USER=postgres
DB_PASSWORD=your_password

# Server
PORT=5000
NODE_ENV=development

# JWT
JWT_SECRET=your_super_secret_key_here

# Frontend
REACT_APP_API_URL=http://localhost:5000/api
```

### 4. Install Dependencies

```bash
# Backend
cd server
npm install

# Frontend
cd ../client
npm install

# Root (for concurrent commands)
cd ..
npm install
```

### 5. Run Development Server

```bash
# From root directory
npm run dev
```

This starts both backend (port 5000) and frontend (port 3000) concurrently.

**Frontend:** http://localhost:3000  
**Backend API:** http://localhost:5000/api  
**API Health Check:** http://localhost:5000/api/health

---

## 🐳 Docker Setup (Recommended for Production)

### Prerequisites
- Docker Desktop installed and running

### 1. Start Services
```bash
docker-compose up -d
```

### 2. Initialize Database
```bash
docker-compose exec db psql -U postgres -d musicmakingdreams -f /docker-entrypoint-initdb.d/schema.sql
```

### 3. Access Application
- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:5000/api

### 4. Stop Services
```bash
docker-compose down
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f server
docker-compose logs -f client
docker-compose logs -f db
```

---

## 🛠️ Development Workflow

### Start Backend Only
```bash
cd server
npm run dev
```

### Start Frontend Only
```bash
cd client
npm start
```

### Run Tests
```bash
# Backend
cd server
npm test

# Frontend
cd client
npm test
```

### Build for Production
```bash
cd client
npm run build
```

---

## 📁 Project Structure

```
.
├── server/
│   ├── routes/           # API endpoints
│   ├── middleware/       # Authentication, validation
│   ├── db/               # Database schema
│   ├── uploads/          # File storage
│   ├── index.js          # Server entry point
│   └── package.json
├── client/
│   ├── src/
│   │   ├── components/   # React components
│   │   ├── pages/        # Page components
│   │   ├── context/      # React context (Auth)
│   │   ├── api/          # API services
│   │   ├── App.tsx       # Main app
│   │   └── index.tsx     # React entry point
│   ├── public/
│   └── package.json
├── docker-compose.yml
└── README.md
```

---

## 🔐 API Authentication

### Get Authentication Token
1. **Register:**
   ```bash
   curl -X POST http://localhost:5000/api/auth/register \
     -H "Content-Type: application/json" \
     -d '{
       "email": "user@example.com",
       "username": "username",
       "password": "password"
     }'
   ```

2. **Login:**
   ```bash
   curl -X POST http://localhost:5000/api/auth/login \
     -H "Content-Type: application/json" \
     -d '{
       "email": "user@example.com",
       "password": "password"
     }'
   ```

3. **Use Token:** Include in request headers
   ```bash
   Authorization: Bearer YOUR_TOKEN_HERE
   ```

---

## 📤 File Upload

### Supported Formats
- **Audio:** MP3, WAV, OGG (max 50MB)
- **Images:** JPEG, PNG (max 50MB)

### Upload Beat
```bash
curl -X POST http://localhost:5000/api/beats \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "title=My Beat" \
  -F "genre=Hip Hop" \
  -F "mood=Dark" \
  -F "bpm=90" \
  -F "price=9.99" \
  -F "audioFile=@beat.mp3" \
  -F "coverImage=@cover.jpg"
```

---

## 🔍 API Endpoints Cheatsheet

### Authentication
- `POST /api/auth/register` - Create account
- `POST /api/auth/login` - Login
- `GET /api/auth/me` - Get current user

### Beats
- `GET /api/beats` - List beats (query: genre, mood, search, page)
- `GET /api/beats/:id` - Get beat details
- `POST /api/beats` - Upload beat (requires auth)
- `PUT /api/beats/:id` - Update beat (requires auth)
- `DELETE /api/beats/:id` - Delete beat (requires auth)

### Users
- `GET /api/users/:id` - Get profile
- `GET /api/users/:id/beats` - Get user's beats
- `PUT /api/users/:id` - Update profile (requires auth)

### Shopping
- `GET /api/cart` - Get cart (requires auth)
- `POST /api/cart/add` - Add to cart (requires auth)
- `DELETE /api/cart/:beatId` - Remove from cart (requires auth)
- `POST /api/orders` - Create order (requires auth)
- `GET /api/orders` - Get orders (requires auth)

### Comments
- `GET /api/comments/beat/:beatId` - Get comments
- `POST /api/comments` - Add comment (requires auth)
- `PUT /api/comments/:commentId` - Update comment (requires auth)
- `DELETE /api/comments/:commentId` - Delete comment (requires auth)

---

## 🐛 Troubleshooting

### Port Already in Use
```bash
# Kill process on port
# macOS/Linux
lsof -ti:3000 | xargs kill -9
lsof -ti:5000 | xargs kill -9

# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Database Connection Error
1. Verify PostgreSQL is running
2. Check credentials in `.env`
3. Ensure database `musicmakingdreams` exists
4. Check schema was imported: `psql -U postgres -d musicmakingdreams -c "\dt"`

### Module Not Found Errors
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

### CORS Errors
- Ensure frontend and backend URLs are correct in `.env`
- Check CORS is enabled in backend `index.js`

---

## 📚 Additional Resources

- [React Documentation](https://react.dev)
- [Express.js Guide](https://expressjs.com)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [Tailwind CSS](https://tailwindcss.com)
- [JWT Auth](https://jwt.io)

---

## 🤝 Contributing

1. Create a feature branch: `git checkout -b feature/AmazingFeature`
2. Commit changes: `git commit -m 'Add AmazingFeature'`
3. Push to branch: `git push origin feature/AmazingFeature`
4. Open a Pull Request

---

## 📞 Support

For issues or questions:
1. Check existing [GitHub Issues](https://github.com/fiorecarp07/Musicmakingdreams/issues)
2. Create a new issue with detailed description
3. Include error logs and steps to reproduce

---

**Happy Coding! 🎵🚀**
