# WindMon — Script Prezentare 5-6 Minute în Română (3 Vorbitori)

**Timp Total:** ~5m 30s | **Ritm:** ~130 cuvinte/min pentru vorbire naturală

---

## VORBITOR 1: ANDREI (Introducere, Problema, API, AI, Frontend, Încheiere) — ~2m 30s

### ⏱ Slide 1 (15s) — Titlu
"Bună, sunt Andrei, și sunt aici cu Șerban și Luca din Echipa 11. Vă prezentăm **WindMon** — o platformă de monitorizare în timp real pentru turbine eoliene. Acordați-ne 6 minute și veți vedea cum am construit o soluție completă de inginerie de date care transformă date brute din senzori în informații cu valoare imediată pentru operatorii de energie eoliană."

### ⏱ Slide 2 (45s) — Provocarea
"Iată problema pe care o rezolvăm: PPC România operează **4 parcuri eoliene** cu **99 de turbine**, generând **peste 384.000 de citiri de la senzori zilnic**. Fiecare turbină are 8 senzori — putere activă, viteza vântului, temperaturi — care transmit la fiecare 5 minute. Dar până acum, toate aceste date stau în AWS S3 ca fișiere Parquet, cu **zero vizibilitate în timp real**. Fără detecție de anomalii. Fără alerte. Fără informații utile.

Deci întrebarea era: Cum procesezi peste 384.000 de citiri zilnic, cu control al calității în timp real, detecție de anomalii și informații relevante — totul într-un hackathon?"

### ⏱ Slide 3 (30s) — Ce Am Construit
"Am construit o **platformă end-to-end** care ingestionează date brute din S3, execută 7 reguli de validare pentru controlul calității, detectează anomalii folosind SQL, generează rapoarte bazate pe AI și livrează totul printr-un **dashboard React** cu alerte în timp real, urmărirea anomaliilor și vizualizare geospațială.

Arhitectura gestionează trei surse de date, aplică caching inteligent, integrează AI-ul DeepSeek și include monitorizare Prometheus. Totul este containerizat și pregătit pentru producție."

**→ Tranziție: "Luca vă prezintă arhitectura sistemului."**

*(Andrei revine după Șerban — slide-urile 14-25)*

### ⏱ Slide 14 (10s) — Secțiunea Spring Boot
"Trecând la stratul aplicativ..."

### ⏱ Slide 15 (40s) — REST API
"Expunem **peste 25 de endpointuri REST** prin Spring Boot 3.2, toate documentate cu Swagger OpenAPI. Avem endpointuri pentru prezentarea flotei, căutare de turbine, timeseries de senzori, preluare de anomalii, raportare de incidente și informații generate de AI. Autentificarea ține cont de tenant — fiecare organizație vede doar parcurile și turbinele proprii."

### ⏱ Slide 17 (50s) — Integrare AI
"Funcționalitatea-cheie este **raportarea zilnică bazată pe AI** folosind modelul DeepSeek LLM. Trimitem date structurate despre anomalii, contextul incidentelor și tendințe ale senzorilor către API, care sintetizează informații ușor de citit: „Turbina WP-15 a înregistrat 3 vârfuri de temperatură în 2 ore — se recomandă inspecție de întreținere."

Am implementat un **circuit breaker** complet pentru reziliență — dacă API-ul DeepSeek este indisponibil, returnăm rapoarte din cache sau degradăm grațios. Monitorizăm totul cu dashboarduri Prometheus și Grafana care urmăresc latența API, rata de cache hit și sănătatea sistemului."

### ⏱ Slides 18-25 (30s) — Frontend & Capturi de Ecran
"Frontend-ul este **React 19 cu Vite 8**. Avem 6 pagini: un dashboard live cu statistici ale flotei și grafice în timp real, o hartă geospațială cu locațiile tuturor turbinelor, o interfață de gestionare a anomaliilor, rapoarte AI zilnice și pagini de detaliu per turbină cu timeseries de senzori.

Totul stilizat cu Tailwind, grafice interactive prin Recharts și tabele de date folosind TanStack Table."

### ⏱ Încheiere (15s) — Final
"Am construit asta în mai puțin de 48 de ore. O platformă completă, de nivel producție. E containerizată, testată, monitorizată și gata să scaleze. Vă mulțumim!"

---

## VORBITOR 2: LUCA (Arhitectura Sistemului) — ~1m 30s

### ⏱ Slides 4-5 (50s) — Prezentare Arhitectură
"Acum să intrăm în arhitectură. Sistemul are trei straturi: **Ingineria de Date** pe stânga, **Stratul de Date** în centru cu PostgreSQL și Redis, și **Stratul Aplicativ** pe dreapta cu API-ul Spring Boot și frontend-ul React.

Pe partea de date, avem un **pipeline cu 6 etape** orchestrat cu APScheduler. Etapa 1 parsează metadatele Parquet din S3, Etapa 2 ingestionează citirile senzorilor, Etapa 3 rulează cele 7 validări de calitate a datelor, Etapa 4 detectează anomalii folosind SQL, Etapa 5 agregă incidentele, iar Etapa 6 generează rapoarte AI zilnice. Fiecare etapă este idempotentă și incrementală — perfectă pentru procesare batch fiabilă."

### ⏱ Slide 6 (20s) — Docker
"Totul rulează în Docker: API-ul Spring Boot, PostgreSQL, Redis și orchestratorul de pipeline în Python. Configurație multi-container cu health checks și gestionarea dependențelor."

### ⏱ Slides 13-13b (35s) — Baza de Date & Caching
"Folosim **PostgreSQL 16 cu 14 tabele** și peste 15 indexuri — indexuri compozite pentru interogări de senzori, indexuri parțiale pentru anomalii și flaguri de calitate. Redis 7 oferă o **strategie de caching pe 3 niveluri**: cache de 30 de minute în baza de date pentru rapoarte, cache Redis de 5 minute pentru răspunsuri API și caching pe client. Asta reduce apelurile către API-ul AI cu 95%.

Avem și rate limiting cu token bucket în Redis pentru a preveni abuzurile și a controla costurile."

**→ Tranziție: "Acum Șerban vă prezintă validarea datelor și detecția anomaliilor."**

---

## VORBITOR 3: ȘERBAN (Validarea Datelor & Detecția Anomaliilor) — ~1m 30s

### ⏱ Slide 8 (25s) — Pipeline & Calitatea Datelor
"Motorul de calitate a datelor validează fiecare citire pe baza **7 reguli**: verificări de null, semnalare pentru putere negativă, praguri de temperatură, intervale de viteză a vântului, verificări de capacitate, detecție de duplicate și goluri în date. Fiecare înregistrare primește un scor de calitate: OK, DEGRADED sau SUSPECT."

### ⏱ Slide 10 (30s) — Cele 7 Reguli de Validare
"Mai concret: verificăm dacă lipsesc valori critice, dacă puterea activă e negativă, dacă temperaturile cutiei de viteze și ale rulmenților sunt între -40°C și 150°C, dacă viteza vântului e în intervalul 0-60 m/s, dacă puterea depășește 110% din capacitatea nominală, dacă sunt timestamp-uri duplicate și dacă există perioade cu mai puțin de 50% din citirile așteptate."

### ⏱ Slide 11 (35s) — Detecția Anomaliilor
"Apoi rulăm **4 detectoare de anomalii bazate pe SQL**: HIGH_WIND_ZERO_POWER detectează defecțiunile turbinei — vânt puternic, dar putere zero. DATA_GAP identifică pauze mai mari de 30 de minute în citiri, semn de probleme de comunicație. TEMPERATURE_SPIKE semnalează creșteri de peste 15°C în 30 de minute la cutia de viteze. Iar SUSPECT_DATA promovează orice citire cu flag SUSPECT la nivel de anomalie. Toate anomaliile sunt apoi grupate în incidente per turbină."

**→ Tranziție: "Andrei continuă cu API-ul, integrarea AI și frontend-ul."**

---

## DEFALCARE TIMP

| Parte | Vorbitor | Slide-uri | Timp |
|-------|----------|-----------|------|
| Intro, Problema, Ce am construit | Andrei | 1-3 | 1:30 |
| Arhitectură, Docker, BD & Cache | Luca | 4-6, 13-13b | 1:30 |
| Validare date & Anomalii | Șerban | 8, 10-11 | 1:30 |
| API, AI, Frontend, Încheiere | Andrei | 14-25 | 1:00 |
| **Total** | | | **5:30** |

---

## SFATURI PENTRU PREZENTARE

1. **Folosiți un instrument de prezentare** — Avansați slide-urile cu tastele săgeți sau o telecomandă
2. **Vorbiți natural** — Acestea sunt linii directoare, nu un script cuvânt cu cuvânt. Adaptați tonul și ritmul
3. **Accentuați numerele-cheie** — 99 de turbine, 384K citiri, 6 etape, 7 validări, 25 de endpointuri
4. **Faceți pauze pentru impact** — După diagramele de arhitectură sau capturile de ecran din dashboard
5. **Contactul vizual** — Concentrați-vă pe juriu/public, nu pe slide-uri
6. **Coordonați tranzițiile** — Tranziții line între vorbitori
7. **Un singur arc narativ** — Problemă → Soluție → Demo → Impact. Păstrați-l liniar, evitați săriturile
8. **Încheiați puternic** — Terminați cu ideea de producție, scalabilitate și tot ce s-a livrat în 48 de ore

---

## ORDINEA VORBITORILOR

- **Andrei:** Deschide (slide-urile 1-3), apoi revine la final (slide-urile 14-25)
- **Luca:** Arhitectura sistemului și baza de date (slide-urile 4-6, 13-13b)
- **Șerban:** Validarea datelor și detecția anomaliilor (slide-urile 8, 10-11)

**Fraze de tranziție recomandate:**
- Andrei → Luca: *"Luca vă prezintă cum e gândit sistemul pe interior."*
- Luca → Șerban: *"Acum Șerban vă arată cum validăm datele și detectăm anomaliile."*
- Șerban → Andrei: *"Andrei continuă cu API-ul, AI-ul și frontend-ul."*
- Andrei → Încheiere: *"Am construit totul în mai puțin de 48 de ore. Vă mulțumim!"*
