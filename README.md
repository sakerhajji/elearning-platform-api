# E-Learning Platform API

REST API for an online course platform: course catalogue and lessons, video delivery with
per-user progress tracking, quizzes, PDF certificates, a discussion forum, and a full
order and payment flow.

**The problem.** An e-learning product needs far more than CRUD on courses — it has to
process and serve video, remember exactly where each learner stopped, grade quizzes, issue
certificates, host discussion, and take money reliably. This is the backend that does all
of it in one service.

**What I built.** The complete Node.js/Express backend: ~30 route modules over 20 Mongoose
models, covering authentication (JWT, two-factor, Google and GitHub OAuth), the course and
sub-course hierarchy, video upload and transcoding, progress tracking, quizzes and
responses, the forum with comments and likes, coupons, orders, and payment through both
Stripe and PayPal.

**Why it is technically interesting.** Three parts are more than plumbing:

- **Video pipeline** — uploads go through Multer to Cloudinary, with `fluent-ffmpeg`
  handling transcoding and segmentation, and a `videoProgress` model recording per-user
  playback position so a learner resumes exactly where they left off.
- **Certificates** — generated server-side with PDFKit and stamped with a QR code for
  verification.
- **Real-time and AI layers** — Socket.IO for live forum activity, plus OpenAI and Google
  Cloud Translate behind dedicated routes for content assistance and translation.

---

## Stack

| Layer | Technology |
| --- | --- |
| Runtime | Node.js 16+, Express 4 |
| Database | MongoDB with Mongoose 8 |
| Auth | JSON Web Tokens, two-factor, Passport (Google OAuth 2.0, GitHub) |
| Payments | Stripe, PayPal REST SDK |
| Media | Cloudinary, fluent-ffmpeg, Sharp, Multer |
| Documents | PDFKit, QRCode |
| Real-time | Socket.IO |
| Email | Nodemailer |
| Packaging | Docker, Docker Compose |

## Architecture

The service is organised in layers so that HTTP concerns, business logic and persistence
stay separate:

```
app.js               # Express bootstrap: middleware, route mounting, socket server
config/              # Database, third-party clients, environment loading
routes/              # HTTP surface — one module per domain (courses, quiz, orders, forum…)
controller/          # Request handling and validation
services/            # Business logic (payments, video processing, mail, AI helpers)
models/              # Mongoose schemas
middleware/          # Authentication, authorisation, upload handling
utils/               # Shared helpers
```

### Domain modules

| Area | Covers |
| --- | --- |
| Learning | `course`, `subCourse`, `courseDetails`, `category`, `concentration` |
| Video | `video`, `videoSegment`, `videoProgress` |
| Assessment | `quiz`, `response`, certificates |
| Community | `forum`, `commentForum`, `likeForum`, `post` |
| Commerce | `orders`, `historyOrders`, `payment`, `coupon` |
| Identity | `user`, `candidat`, registration, password reset, 2FA, OAuth |

## Getting started

### Prerequisites

- Node.js 16 or newer (an `.nvmrc` is included)
- MongoDB, local or hosted
- Accounts for the third-party services you intend to enable (Cloudinary, Stripe, PayPal, SMTP)

### Run with Docker

```bash
git clone https://github.com/sakerhajji/elearning-platform-api.git
cd elearning-platform-api
cp .env.example .env      # fill in your own values
docker compose up --build
```

### Run locally

```bash
nvm use
npm install
cp .env.example .env
npm run dev               # nodemon
# or
npm start
```

### Environment

Configuration is read from `.env` — never commit it. The variables the service expects:

| Variable | Purpose |
| --- | --- |
| `PORT` | HTTP port |
| `MONGO_URI` | MongoDB connection string |
| `JWT_SECRET` | Token signing key |
| `CLOUDINARY_*` | Media storage credentials |
| `STRIPE_SECRET_KEY` | Stripe payments |
| `PAYPAL_CLIENT_ID`, `PAYPAL_CLIENT_SECRET` | PayPal payments |
| `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Google OAuth |
| `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET` | GitHub OAuth |
| `SMTP_*` | Transactional email |
| `OPENAI_API_KEY` | AI-assisted endpoints |

## Status

Built as the backend for a course platform. The repository is the API only — it is
published as a portfolio piece, not as a maintained package.

## Author

**Saker Hajji** — Full-Stack Software Engineer · [github.com/sakerhajji](https://github.com/sakerhajji)
