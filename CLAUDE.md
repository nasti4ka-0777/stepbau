# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

STEPBAU is a full-stack web application for a German construction company (Nachunternehmer) specializing in Trockenbau, Bodenverlegung, and Innenausbau. It consists of a React frontend landing page and a Python FastAPI backend for contact form submissions with MongoDB storage.

## Commands

### Frontend (run from `frontend/`)
- **Dev server:** `yarn start` (uses Craco, serves on port 3000)
- **Build:** `yarn build`
- **Tests:** `yarn test` (Jest via CRA)
- **Single test:** `yarn test -- --testPathPattern=<pattern>`
- **Install deps:** `yarn install`

### Backend (run from `backend/`)
- **Run server:** `uvicorn server:app --reload`
- **Install deps:** `pip install -r requirements.txt`
- **Tests:** `pytest` (from project root)
- **API test script:** `python backend_test.py` (integration tests against running server)

## Architecture

### Frontend (`frontend/src/`)
- **React 19** with Create React App + Craco for config overrides
- **Tailwind CSS** with shadcn/ui components (in `components/ui/`)
- **Path alias:** `@/` maps to `src/` (configured in `craco.config.js` and `jsconfig.json`)

**Page sections** are composed in `App.js` in order: Header, HeroSection, AboutSection, ServicesSection, WhyUsSection, PortfolioSection, CoverageSection, ContactSection, Footer, CookieBanner.

**Internationalization:** `LanguageContext` provides DE/EN switching. Translations live in `lib/i18n.js` as a flat key-value object keyed by language code. Components access translations via `useLanguage()` hook: `const { t, lang, toggleLang } = useLanguage()`. Default language is German (`de`).

**Scroll animations:** `useScrollAnimation()` hook uses Intersection Observer. Elements with `.animate-on-scroll` fade in when visible.

**Styling conventions:**
- Dark theme throughout (`#0a0a0a` background, `#111111` cards)
- Neon glow effects (`.neon-border`, `.neon-border-hover`)
- Glass morphism (`.glass` class with backdrop blur)
- Fonts: Montserrat (headings), Poppins (body), JetBrains Mono (technical)
- Design system documented in `design_guidelines.json`

### Backend (`backend/`)
- **FastAPI** with async MongoDB via Motor driver
- **Entry point:** `server.py` — defines routes, models, and DB connection
- **API prefix:** all routes under `/api` (e.g., `/api/contact`)
- **Models:** `ContactForm` (request validation with EmailStr) and `ContactSubmission` (response with auto-generated id and timestamp)
- **Environment variables** (in `backend/.env`): `MONGO_URL`, `DB_NAME`, `CORS_ORIGINS`

### Contact Form Flow
1. Frontend `ContactSection` submits POST to `${REACT_APP_BACKEND_URL}/api/contact`
2. Backend validates with Pydantic (`ContactForm`), creates `ContactSubmission`, stores in MongoDB `contact_submissions` collection
3. Toast notifications (sonner) show success/error on frontend
