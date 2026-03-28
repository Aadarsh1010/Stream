# Changelog

All notable changes to StreamHub will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2026-03-28

### Added
- Initial release of StreamHub
- User authentication with NextAuth.js (email/password)
- Dashboard with content grid, search, and filters (Movies/Anime/All)
- Content management (CRUD operations)
- Video link submission with automatic URL extraction
- Multi-strategy video extraction engine (6-layer approach)
- HLS.js + Plyr.js video player with iframe fallback
- Wishlist functionality with toggle
- Watch history tracking
- Dark theme UI with custom color palette
- PostgreSQL + Prisma ORM
- Comprehensive test suite (Jest)
- API documentation (OpenAPI 3.0)
- Docker deployment configuration
- CI/CD pipeline (GitHub Actions)

### Technical Details
- Next.js 14 App Router with TypeScript
- TailwindCSS for styling
- bcryptjs for password hashing
- axios + cheerio for server-side scraping
- SQLite for development, PostgreSQL for production
- ESLint + Prettier configured
- 42 source files, 208 packages

---

## [Planned] - Upcoming

- Subtitle support (.vtt, .srt)
- Mobile responsive improvements
- User profiles and preferences
- Comments and ratings system
- Multi-language UI (i18n)
- Offline downloads (PWA)
- Video recommendations engine
- Admin dashboard
- Bulk content import
- Chromecast/AirPlay support
- Integration with media servers (Jellyfin, Plex)

---

## Migration Guide

### From v0.x to v1.0.0

Initial release - no migrations needed.

---

## Support

For issues, feature requests, or questions, please use GitHub Issues or Discussions.
