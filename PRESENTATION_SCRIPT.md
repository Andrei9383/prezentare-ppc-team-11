# WindMon — 5-6 Minute Presentation Script (3 Speakers)

**Total Time:** ~5m 30s | **Pace:** ~130 words/min for natural speaking rhythm

---

## SPEAKER 1: ANDREI (Introduction & Problem) — ~1m 45s (230 words)

### ⏱ Slide 1 (15s) — Title
"Good morning, I'm Andrei, and I'm here with Serbant and Petre from Team 11. We're presenting **WindMon** — a real-time monitoring platform for wind turbine operations. Give us 6 minutes, and you'll see how we built a complete data engineering solution that transforms raw sensor data into actionable intelligence for wind energy operators."

### ⏱ Slide 2 (45s) — The Challenge
"Here's the problem we're solving: PPC Romania operates **4 wind parks** with **99 turbines**, generating **384,000+ sensor readings daily**. Each turbine has 8 sensors — power output, wind speed, temperatures — transmitting every 5 minutes. But historically, all this data sits in AWS S3 as Parquet files with **zero real-time visibility**. No anomaly detection. No alerts. No insights.

So the question was: How do we process 384K+ readings daily with real-time quality control, anomaly detection, and meaningful insights — all within a hackathon?"

### ⏱ Slide 3 (30s) — What We Built
"We built an **end-to-end platform** that ingests raw S3 data, runs 7 validation rules for quality control, detects anomalies using SQL, generates AI-powered insights, and delivers everything through a **React dashboard** with real-time alerts, anomaly tracking, and geospatial visualization.

The architecture handles three data sources, applies intelligent caching, integrates DeepSeek AI, and includes Prometheus monitoring. Everything is containerized and production-ready."

**→ Transition to Serbant for backend deep dive**

---

## SPEAKER 2: SERBANT (Backend Architecture) — ~2m (265 words)

### ⏱ Slides 4-5 (50s) — Architecture Overview
"Now let's dive into the architecture. The system has three layers: **Data Engineering** on the left, **Data Layer** in the middle with PostgreSQL and Redis, and **Application Layer** on the right with a Spring Boot REST API and React frontend.

On the data side, we have a **6-stage pipeline** orchestrated with APScheduler. Stage 1 parses Parquet metadata from S3, Stage 2 ingests sensor readings, Stage 3 runs our 7 data quality validations, Stage 4 detects anomalies using SQL, Stage 5 aggregates incidents, and Stage 6 generates daily AI reports. Each stage is idempotent and incremental — perfect for reliable batch processing."

### ⏱ Slide 6 (20s) — Docker
"Everything runs in Docker: Spring Boot API, PostgreSQL, Redis, and the Python pipeline orchestrator. Multi-container setup with health checks and dependency management."

### ⏱ Slides 8-11 (45s) — Data Quality & Anomaly Detection
"The data quality engine validates every single reading against **7 rules**: null checks, negative power flags, temperature thresholds, wind speed ranges, capacity checks, duplicate detection, and sparse data gaps. Each record gets a quality score: OK, DEGRADED, or SUSPECT.

Then we run **4 SQL-based anomaly detectors**: HIGH_WIND_ZERO_POWER catches turbine failures, DATA_GAP detects communication issues, TEMPERATURE_SPIKE spots impending mechanical problems, and SUSPECT_DATA catches systemic quality issues. Anomalies get clustered into incidents per turbine."

### ⏱ Slides 13-13b (35s) — Database & Caching
"We use **PostgreSQL 16 with 14 tables** and 15+ indexes — composite indexes for sensor queries, partial indexes for anomalies and quality flags. Redis 7 provides a **3-layer caching strategy**: 30-minute DB cache for reports, 5-minute Redis cache for API responses, and client-side caching. This reduces AI API calls by 95%.

We also have token-bucket rate limiting in Redis to prevent abuse and control costs."

**→ Transition to Petre for frontend & AI**

---

## SPEAKER 3: PETRE (Frontend & AI Integration) — ~2m (260 words)

### ⏱ Slide 14 (10s) — Spring Boot Section
"Shifting to the application layer..."

### ⏱ Slide 15 (40s) — REST API
"We expose **25+ REST endpoints** through Spring Boot 3.2. All documented with OpenAPI Swagger. We have endpoints for fleet overview, turbine search, sensor timeseries, anomaly retrieval, incident reporting, and AI-powered insights. Authentication is tenant-aware — each organization sees only their parks and turbines."

### ⏱ Slide 17 (50s) — AI Integration
"The standout feature is **AI-powered daily reports** using DeepSeek LLM. We send structured anomaly data, incident context, and sensor trends to the API, which synthesizes human-readable insights: 'Turbine WP-15 experiencing 3 temperature spikes in 2 hours — recommend maintenance inspection.' 

We built a full **circuit breaker pattern** for resilience — if the DeepSeek API is down, we return cached reports or gracefully degrade. We monitor everything with Prometheus and Grafana dashboards tracking API latency, cache hit rates, and system health."

### ⏱ Slides 18-25 (30s) — Frontend & Screenshots
"The frontend is **React 19 with Vite 8**. We have 6 pages: a live dashboard with fleet stats and real-time charts, a geospatial map view showing all turbine locations, an anomaly management interface, daily AI reports, and deep-dive turbine detail pages with sensor timeseries.

All styled with Tailwind, interactive charts via Recharts, and data grids using TanStack Table."

### ⏱ Closing (15s) — Call to Action
"We built this in under 48 hours. A complete, production-grade platform. It's containerized, tested, monitored, and ready to scale. Thank you!"

---

## TIMING BREAKDOWN

| Part | Speaker | Slides | Time |
|------|---------|--------|------|
| Intro | Andrei | 1–3 | 1:45 |
| Backend | Serbant | 4–15 | 2:00 |
| Frontend & AI | Petre | 16–25 | 1:45 |
| **Total** | | | **5:30** |

---

## TIPS FOR DELIVERY

1. **Use a Remote/Presentation Tool** — Advance slides with arrow keys or clicker
2. **Speak Naturally** — These are guidelines, not word-for-word scripts. Adapt your tone and pacing
3. **Emphasize Key Numbers** — 99 turbines, 384K readings, 6 stages, 7 validations, 25 endpoints
4. **Pause for Impact** — After showing architecture diagrams or dashboard screenshots
5. **Make Eye Contact** — Focus on judges/audience, not slides
6. **Practice Handoffs** — Coordinate smooth transitions between speakers (e.g., "Over to Serbant for the backend...")
7. **One Story Arc** — Problem → Solution → Demo → Impact. Keep it linear, avoid jumping around
8. **Close Strong** — End with production-readiness, scalability, and the bold scope delivered in 48 hours

---

## SPEAKER MICROPHONE SETUP (Optional)

If using a presentation remote with integrated mic:
- **Andrei:** Speaks first, introduce team and problem
- **Serbant:** Picks up mid-architecture, owns backend + data layer
- **Petre:** Closes with frontend, AI, and final thoughts

**Recommended transition phrases:**
- Andrei → Serbant: *"To build this, we engineered a pretty sophisticated backend. Over to Serbant..."*
- Serbant → Petre: *"All this works because of smart caching and API design. Let me hand it to Petre to show the user experience..."*
- Petre → Close: *"This all runs in containers and scales horizontally. Thanks everyone!"*
