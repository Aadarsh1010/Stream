# StreamHub - Video Streaming Platform

A full-stack video streaming platform built with Next.js 14, TypeScript, PostgreSQL, and Prisma.

## Prerequisites

Before running this project, make sure you have:

- **Node.js 18+** installed
- **PostgreSQL 16** running locally (or modify `.env` to connect to your database)
- **Git** (optional)

## Quick Start

### 1. Install Dependencies

```bash
npm install
```

### 2. Setup Database

#### Option A: Using Docker (Recommended)
If you have Docker installed:

```bash
docker-compose up -d
```

This will start PostgreSQL on port 5432 with:
- User: `postgres`
- Password: `password`
- Database: `streamhub`

#### Option B: Manual PostgreSQL Setup
1. Install PostgreSQL from [postgresql.org](https://www.postgresql.org/download/)
2. Create a database:
   ```sql
   CREATE DATABASE streamhub;
   ```
3. Update `.env` with your connection string:
   ```
   DATABASE_URL="postgresql://username:password@localhost:5432/streamhub"
   ```

### 3. Generate Prisma Client & Run Migration

```bash
npx prisma generate
npx prisma migrate dev --name init
```

### 4. Configure Environment Variables

Edit `.env` file:

```env
DATABASE_URL="postgresql://postgres:password@localhost:5432/streamhub?schema=public"
NEXTAUTH_SECRET="generate-a-random-32-character-string-here"
NEXTAUTH_URL="http://localhost:3000"
```

**Generate NEXTAUTH_SECRET:**
```bash
# On Linux/Mac
openssl rand -base64 32

# On Windows (PowerShell)
[Convert]::ToBase64String((1..32 | ForEach-Object { Get-Random -Maximum 256 }))
```

### 5. Start Development Server

```bash
npm run dev
```

Visit: http://localhost:3000

---

## Features

- **Authentication**: Sign up / Login with email & password (NextAuth.js)
- **Dashboard**: Browse your video collection with search & filters
- **Content Management**: Add Movies & Anime with metadata
- **Video Playback**: Native HTML5 video, HLS streams, and iframe fallback
- **Video Extraction**: Automatic URL extraction from hosting pages
- **Wishlist**: Save favorite content
- **Watch History**: Track viewing progress
- **Dark Theme**: Modern dark UI with custom color palette

---

## Tech Stack

- **Frontend**: Next.js 14 App Router, React 18, TypeScript, TailwindCSS
- **Backend**: Next.js API Routes
- **Database**: PostgreSQL
- **ORM**: Prisma
- **Auth**: NextAuth.js (Credentials Provider)
- **Video**: Plyr.js, HLS.js
- **Utilities**: bcryptjs, axios, cheerio

---

## API Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/signup` | Create new user account |
| GET/POST | `/api/auth/[...nextauth]` | NextAuth callbacks |
| GET | `/api/content` | Fetch user's content (supports ?type= & ?search=) |
| POST | `/api/content` | Create new content |
| GET | `/api/content/[id]` | Get content details with video links |
| DELETE | `/api/content/[id]` | Delete content |
| POST | `/api/content/[id]/video-links` | Submit & extract video URL |
| POST | `/api/content/[id]/wishlist` | Toggle wishlist |
| POST | `/api/extract-video` | Extract direct video URL from page |
| POST | `/api/watch-history` | Save watch progress |

---

## Video Extraction

The platform supports automatic video extraction from:

- **Direct video files**: `.mp4`, `.webm`, `.mkv`, `.avi`, `.m3u8`
- **Embedded players**: OG tags, `<source>` tags, JSON configs in scripts
- **Special hosts**:
  - `gofile.io` - API integration
  - `shipcdn.buzz` - Direct CDN verification

Extraction strategy (in order):
1. Direct URL check
2. `<source>` / `<video>` tags
3. Open Graph meta tags
4. Regex pattern matching
5. JSON blobs in script tags
6. Player configurations (jwplayer, plyr, etc.)
7. Host-specific extractors

---

## Project Structure

```
src/
├── app/
│   ├── page.tsx              # Dashboard (protected)
│   ├── layout.tsx            # Root layout with SessionProvider
│   ├── globals.css           # Dark theme styles
│   ├── login/page.tsx        # Login page
│   ├── signup/page.tsx       # Signup page
│   ├── content/[id]/page.tsx # Content detail + player
│   └── api/                  # API routes
├── components/
│   ├── Navbar.tsx
│   ├── ContentCard.tsx
│   ├── AddContentModal.tsx
│   ├── SubmitVideoLinkModal.tsx
│   └── VideoPlayer.tsx
├── lib/
│   ├── prisma.ts             # Prisma client singleton
│   ├── auth.ts               # NextAuth configuration
│   └── extractVideo.ts       # Video extraction logic
└── types/
    ├── index.ts              # TypeScript interfaces
    └── plyr.d.ts             # Plyr type declarations
```

---

## Troubleshooting

### Database Connection Error
- Ensure PostgreSQL is running (`pg_isready` or `docker ps`)
- Check DATABASE_URL in `.env` is correct
- Verify port 5432 is accessible

### NextAuth Errors
- Ensure NEXTAUTH_SECRET is set (32+ random characters)
- Check NEXTAUTH_URL matches your dev URL

### Video Not Playing
- Verify directUrl is populated (check API response)
- HLS streams require CORS headers on the server
- Iframe fallback requires the source to allow embedding

### Prisma Issues
```bash
# Reset database and migrations
npx prisma migrate reset

# Regenerate client after schema changes
npx prisma generate
```

---

## License

MIT

---

## Author

Built with Claude Code
