# 📘 Product Requirements Document: KitchenFlow Trainer
**Version**: 1.0  
**Owner**: Jared Sanford  
**Reviewed By**: Chef Matt, Design Lead, Engineering Lead, QA Lead  
**Date**: 2025-06-06  

---

## 1. Purpose & Scope
KitchenFlow Trainer is a mobile-first, offline-capable app for restaurant kitchens. It reduces onboarding time, ensures consistency in cooking and plating, enforces cleanliness and safety standards, and gives managers real-time training insights. Initially deployed at Lakeside Kitchen.

---

## 2. Problem Statement
- Staff turnover creates constant training needs.  
- Inconsistent plating affects quality.  
- Paper checklists are error-prone and unauditable.  
- No system to monitor individual progress or identify weak points.

---

## 3. Goals & Success Metrics  

| Objective                      | Metric                                               | Target       |
|-------------------------------|------------------------------------------------------|--------------|
| Reduce onboarding time        | Avg. days to complete core modules                  | ↓ 30% in 6 mo|
| Improve plating consistency   | AI photo check pass rate (first try)                | ≥ 75%        |
| Boost confidence              | Post-training survey “Satisfied” rate               | ≥ 80%        |
| Increase adoption             | New hires logging in within week                    | ≥ 90%        |
| Maintain engagement           | Avg. trainee sessions (30-day)                      | ≥ 3/week     |

---

## 4. Personas
- **New Cook (Novice)** – Needs visual, guided cooking and confidence.  
- **Prep Cook (Experienced)** – Wants to learn Lakeside-specific techniques.  
- **Sous-Chef/Trainer** – Reviews builds, sees AI failures, and mentors.  
- **Chef/Owner** – Views analytics, compliance, and quality trends.

---

## 5. User Stories
- As a line cook, I want timed, step-by-step cook mode so I don’t mess up.  
- As a trainer, I want to see who failed AI checks so I can help them.  
- As a closer, I want a cleaning checklist so nothing is missed.  
- As a manager, I want reports that show our best and worst performers.

---

## 6. Functional Requirements  

### 6.1 Authentication
- **FR-1**: PIN login with session cookie.  
- Auto logout after 30 minutes (non-functional req).

### 6.2 Guided Cook Mode
- **FR-2**: Step-by-step instructions with media, overlays, timers.  
- **FR-3**: Step state persists on page reload.  
- **FR-4**: Auto-navigate to AI check if `quality_check = true` on final step.  

🔹 **Prompt for AI Cook Mode Step Guidance**  
```plaintext
SYSTEM: You are KitchenFlow’s sous-chef. Present the next step clearly.
USER: {dishName}, step {currentStep}/{totalSteps}. Instruction: "{instruction}".
```

### 6.3 AI Quality Check
- **FR-5**: Take photo of plated dish from camera.  
- **FR-6**: Return `{ isCorrectlyBuilt, feedback[] }`.  
- **FR-7**: Save result locally and to user profile.  

🔹 **Prompt for AI Dish Validation**  
```plaintext
SYSTEM: You are a culinary judge. Score {userImage} against {referenceImage}.
Return build accuracy 0–100. isCorrectlyBuilt = score ≥ 80.
Give 1–3 actionable tips.
```

### 6.4 Quick Reference
- **FR-8**: Searchable table of temperatures, prep procedures, allergens, safety, etc.

### 6.5 Shift Prep Board
- **FR-9**: Editable par/stock display with color-coded status.  
- **FR-10**: “Need to Prep?” wizard (planned for v2).

### 6.6 Cleaning & Close-Down
- **FR-11**: Daily and weekly cleaning tasks tracked per user. Media optional.

### 6.7 Progress & Gamification
- **FR-12**: XP, badges, levels, build streaks.  
- **FR-13**: Last failed steps/AI checks stored for review.

---

## 7. Non-Functional Requirements  

| Requirement     | Target                           |
|----------------|----------------------------------|
| Performance     | 95% pages < 1s load on Wi-Fi     |
| Reliability     | ≥ 99.5% uptime                   |
| Accessibility   | WCAG 2.2 AA                      |
| Offline Mode    | Cache static assets + 3 dishes   |
| Security        | OWASP mitigated; AI images purge ≤ 24h |

---

## 8. Technical Architecture  

- **Frontend**: Next.js 14, Tailwind, Shadcn UI  
- **State**: Hooks + Edge KV for progress  
- **AI**: Genkit (Google Vision) via API routes  
- **Auth**: HttpOnly cookie via `middleware.ts`  
- **UI Reference**: See `ui.txt` for layout/colors

---

## 9. Milestones  

| Phase            | Deliverables                                | Target Date   |
|------------------|---------------------------------------------|---------------|
| Design Sprint    | Wireframes, UI, route map                   | 2025-07-01    |
| MVP Alpha        | Auth + 3 dishes + closing checklist         | 2025-08-15    |
| Pilot Launch     | Deployed to 5 Lakeside tablets              | 2025-09-01    |
| MVP Beta         | Cook mode + AI photo check + progress       | 2025-10-10    |
| Full Launch      | All dishes + PrepBoard + QuickRef           | 2025-11-30    |
| v2 Expansion     | Authoring UI + multi-location support       | Q1 2026       |

---

## 10. Acceptance Checklist

- [ ] Auth required on all app routes  
- [ ] App installable as PWA on iPads  
- [ ] AI check returns in ≤ 5s  
- [ ] Timer delay enforced (`force_wait=true`)  
- [ ] Checklist state saves per user per day  
- [ ] Shift Prep % logic verified  
- [ ] CSV export for closing results  
- [ ] AI fails create feedback cards  
- [ ] Offline works for last 3 viewed dishes  

---

## 11. Risks & Mitigations  

| Risk                        | Level  | Mitigation                                    |
|-----------------------------|--------|-----------------------------------------------|
| Wi-Fi outages               | Med    | Offline caching, queue photo uploads          |
| AI scoring false negatives  | Low    | Manual override + trainer review              |
| Image privacy               | Med    | Auto-delete within 24h, blur faces (if any)   |

---

## 12. Glossary  
- **Genkit**: Google’s AI workflow SDK (for photo checks)  
- **Par Level**: Ideal inventory amount  
- **TCS Food**: Time/Temperature Control for Safety  
- **QuickRefItem**: JSON shape for temp/prep/safety facts  
