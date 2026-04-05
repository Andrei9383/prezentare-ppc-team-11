# WindMon — Script Prezentare 5-6 Minute în Română (3 Vorbitori)

**Timp Total:** ~5m 30s | **Ritm:** ~130 cuvinte/min pentru vorbire naturală

---

## VORBITOR 1: ANDREI (Introducere & Problema) — ~1m 45s (230 cuvinte)

### ⏱ Slide 1 (15s) — Titlu
"Bună, sunt Andrei, și sunt aici cu Șerban și Petru din Team 11. Vă prezentăm **WindMon** — o platformă de monitorizare în timp real pentru turbinele eoliene. Acordați-ne 6 minute, și veți vedea cum am construit o soluție completă de inginerie de date care transformă date brute din senzori în informații cu valoare imediată pentru operatorii de energie eoliană."

### ⏱ Slide 2 (45s) — Provocarea
"Iată problema pe care o rezolvăm: PPC România operează **4 parcuri eoliene** cu **99 turbine**, generând **peste 384.000 de citiri de senzori zilnic**. Fiecare turbină are 8 senzori — putere activă, viteză vânt, temperaturi — transmițând la fiecare 5 minute. Dar până acum, toți acești date stau în AWS S3 ca fișiere Parquet cu **zero vizibilitate în timp real**. Fără detecție de anomalii. Fără alerte. Fără informații utile.

Deci întrebarea era: Cum procesezi 384K+ de citiri zilnic cu control de calitate în timp real, detecție de anomalii și informații semnificative — totul într-un hackathon?"

### ⏱ Slide 3 (30s) — Ce Am Construit
"Am construit o **platformă end-to-end** care ingestionează date brute din S3, execută 7 reguli de validare pentru controlul calității, detectează anomalii folosind SQL, generează informații bazate pe AI, și livrează totul printr-un **dashboard React** cu alerte în timp real, urmărire de anomalii și vizualizare geospațială.

Arhitectura gestionează trei surse de date, aplică caching inteligent, integrează AI-ul DeepSeek și include monitorizare Prometheus. Totul este containerizat și gata pentru producție."

**→ Tranziție la Șerban pentru deep dive backend**

---

## VORBITOR 2: ȘERBAN (Arhitectura Backend) — ~2m (265 cuvinte)

### ⏱ Slides 4-5 (50s) — Prezentare Arhitectură
"Acum să ne adâncim în arhitectură. Sistemul are trei straturi: **Inginerie de Date** pe stânga, **Stratul de Date** în mijloc cu PostgreSQL și Redis, și **Stratul de Aplicație** pe dreapta cu API-ul Spring Boot și frontend-ul React.

Pe partea de date, avem un **pipeline cu 6 etape** orkestat cu APScheduler. Etapa 1 parsează metadate Parquet din S3, Etapa 2 ingestionează citiri de senzori, Etapa 3 execută 7 validări de calitate a datelor, Etapa 4 detectează anomalii folosind SQL, Etapa 5 agregrează incidente, și Etapa 6 generează rapoarte AI zilnice. Fiecare etapă este idempotentă și incrementală — perfectă pentru procesare batch fiabilă."

### ⏱ Slide 6 (20s) — Docker
"Totul rulează în Docker: API-ul Spring Boot, PostgreSQL, Redis și orchestratorul pipeline Python. Setup multi-container cu health checks și gestionare a dependențelor."

### ⏱ Slides 8-11 (45s) — Calitatea Datelor & Detecția Anomaliilor
"Motorul de calitate a datelor validează fiecare citire individuală pe baza **7 reguli**: verificări de null, alertări pentru putere negativă, praguri de temperatură, intervale de viteză vânt, verificări de capacitate, detecție de duplicate și goluri în date. Fiecare record primește un scor de calitate: OK, DEGRADED, sau SUSPECT.

Apoi rulăm **4 detectoare de anomalii bazate pe SQL**: HIGH_WIND_ZERO_POWER detectează defecțiunile turbinei, DATA_GAP detectează problemele de comunicație, TEMPERATURE_SPIKE identifică probleme mecanice viitoare, și SUSPECT_DATA detectează problemele sistematice de calitate. Anomaliile sunt grupate în incidente per turbină."

### ⏱ Slides 13-13b (35s) — Baza de Date & Caching
"Folosim **PostgreSQL 16 cu 14 tabele** și 15+ indexuri — indexuri compozite pentru interogări de senzori, indexuri parțiale pentru anomalii și flaguri de calitate. Redis 7 asigură o **strategie de caching în 3 straturi**: cache de 30 de minute în BD pentru rapoarte, cache Redis de 5 minute pentru răspunsuri API și caching pe client. Aceasta reduce apelurile AI API cu 95%.

De asemenea, avem rate limiting cu token bucket în Redis pentru a preveni abuzuri și a controla costurile."

**→ Tranziție la Petru pentru frontend & AI**

---

## VORBITOR 3: PETRU (Frontend & Integrare AI) — ~2m (260 cuvinte)

### ⏱ Slide 14 (10s) — Secțiune Spring Boot
"Trecând la stratul de aplicație..."

### ⏱ Slide 15 (40s) — REST API
"Expunem **25+ puncte de termină REST** prin Spring Boot 3.2. Toate documentate cu Swagger OpenAPI. Avem puncte de capăt pentru prezentare flotă, căutare turbină, timeseries de senzori, preluare de anomalii, raportare de incidente și informații bazate pe AI. Autentificarea este sensibilă la tenant — fiecare organizație vede numai parcurile și turbinele sale."

### ⏱ Slide 17 (50s) — Integrare AI
"Funcția cu valoare adăugată este **raportarea zilnică bazată pe AI** folosind modelul DeepSeek LLM. Trimitem date structurate de anomalii, context de incident și tendințe de senzori API-ului, care sintetizează informații lizibile de oameni: 'Turbina WP-15 experimentează 3 vârfuri de temperatură în 2 ore — se recomandă inspecție de întreținere.'

Am construit un model complet **circuit breaker** pentru reziliență — dacă API-ul DeepSeek este inactiv, returnăm rapoarte în cache sau degradare gracioză. Monitorizez totul cu dashboarduri Prometheus și Grafana urmărind latența API, rata hit cache și sănătatea sistemului."

### ⏱ Slides 18-25 (30s) — Frontend & Capturi de Ecran
"Frontend-ul este **React 19 cu Vite 8**. Avem 6 pagini: un dashboard live cu statistici flote și diagrame în timp real, o vedere hartă geospațială arătând locațiile tuturor turbinelor, o interfață de gestionare a anomaliilor, rapoarte AI zilnice și pagini de detaliu turbină cu timeseries de senzori.

Totul stilizat cu Tailwind, diagrame interactive prin Recharts și grilaje de date folosind TanStack Table."

### ⏱ Închiere (15s) — Apel la Acțiune
"Am construit asta în mai puțin de 48 de ore. O platformă completă, de nivel producție. Este containerizată, testată, monitorizată și gată să se scaleze. Vă mulțumesc!"

---

## DEFALCARE TIMP

| Parte | Vorbitor | Slide-uri | Timp |
|-------|----------|-----------|------|
| Intro | Andrei | 1–3 | 1:45 |
| Backend | Șerban | 4–15 | 2:00 |
| Frontend & AI | Petru | 16–25 | 1:45 |
| **Total** | | | **5:30** |

---

## SFATURI PENTRU PREZENTARE

1. **Folosiți un Instrument de Prezentare** — Avan­sați slide-urile cu taste săgeți sau telecomandă
2. **Vorbți Natural** — Acestea sunt linii directoare, nu script cuvânt-cu-cuvânt. Adaptați tonul și ritmul
3. **Subliniați Numerele Cheie** — 99 turbine, 384K de citiri, 6 etape, 7 validări, 25 de puncte de termină
4. **Faceți Pauze pentru Impact** — După arătarea diagramelor de arhitectură sau capturilor de ecran din dashboard
5. **Contactul cu Ochii** — Concentrați-vă pe judecători/public, nu pe slide-uri
6. **Coordonați Tranzițiile** — Tranziții lejere între vorbitori (de ex., "Peste la Șerban pentru backend...")
7. **Un Arc de Poveste Singular** — Problemă → Soluție → Demo → Impact. Păstrați-o liniară, evitați săriturile
8. **Ieșiți Puternic** — Sfârșiți cu gânduri de producție, scalabilitate și domeniu curajos livrat în 48 de ore

---

## SETUP MICROFON VORBITORI (Opțional)

Dacă folosiți o telecomandă de prezentare cu microfon integrat:
- **Andrei:** Vorbește primul, introduce echipa și problema
- **Șerban:** Preia la jumătatea arhitecturii, deține backend + stratul de date
- **Petru:** Închide cu frontend, AI și gânduri finale

**Fraze de tranziție recomandate:**
- Andrei → Șerban: *"Pentru a construi asta, am creat un backend destul de sofisticat. Peste la Șerban..."*
- Șerban → Petru: *"Toate astea funcționează datorită caching-ului inteligent și designului API. Lasă-mă pe Petru să-ți arate experiența utilizatorului..."*
- Petru → Închiere: *"Totul rulează în containere și se scalează orizontal. Vă mulțumesc!"*
