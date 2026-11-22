# Hosting comparison – Smart Task Manager

Phase 2 hosting deliverable. The options below are architectural candidates, not a final provider selection. Provider prices, free-tier quotas, regions and terms change; check the linked official pricing pages on the day the team makes its decision. Do not treat a free tier as a guaranteed long-term zero-cost service.

## 1. Deployment needs

- Angular is built into static HTML, JavaScript and CSS assets.
- Supabase deployment consists of a PostgreSQL project, Auth configuration, SQL migrations and Edge Functions.
- Production must use HTTPS.

- Restrict Edge Function CORS to the deployed origins. From US-09 on (accounts are a Should-have and follow after the MVP), also configure the Angular origin in the Supabase Auth redirect URLs.

- Never put Supabase secret/service-role keys in Angular build variables or public repository files.
- The team needs a reproducible local development option. Angular CLI plus the Supabase CLI/local stack supports this, but local development does not remove the production provider dependency.

## 2. Hosting options

| Option                                  | What it hosts                                       | Cost model / free-tier consideration                                                                                                                    | Advantages                                                                    | Disadvantages                                                                                                             |
| --------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Supabase hosted project                 | PostgreSQL, Auth, Data API, Edge Functions          | Free tier with quotas; paid plans and usage limits may apply. Check current [Supabase pricing](https://supabase.com/pricing).                           | Integrated backend; low operations effort; one platform for database and Auth | Vendor dependency; usage, pausing/inactivity, region and data-processing terms must be checked; potential NFR-07 conflict |
| Supabase local/self-hosted              | Local development stack or team-operated deployment | Local development has no hosted service charge; self-hosting has infrastructure, backup, update and operations costs                                    | More control over data location and offline/local development                 | Self-hosting is not zero-cost operationally; team owns upgrades, backups, security and availability                       |
| Vercel (Angular static build)           | Angular static frontend                             | Free/Hobby tier subject to fair-use and plan limits; see [Vercel pricing](https://vercel.com/pricing).                                                  | Simple Git integration and static deployment                                  | Separate provider from Supabase; confirm project/account usage terms                                                      |
| Netlify (Angular static build)          | Angular static frontend                             | Free plan/usage limits may change; see [Netlify pricing](https://www.netlify.com/pricing/).                                                             | Straightforward static hosting and previews                                   | Separate provider; build minutes/bandwidth limits                                                                         |
| GitHub Pages                            | Static Angular frontend                             | No additional hosting charge for many public repositories; see [GitHub Pages documentation](https://docs.github.com/en/pages).                          | Convenient for a public student project; no separate app server               | Angular SPA route fallback and deployment setup need care; no backend hosting                                             |
| Render / Railway or comparable app host | Custom API and optionally database for Variant B    | Free/paid plans and resource-based billing vary; check [Render pricing](https://render.com/pricing) and [Railway pricing](https://railway.com/pricing). | Suitable for a custom backend alternative                                     | Variant B needs both API and database; free resources may sleep or be limited                                             |

## 3. Candidate deployment combinations

| Combination                  | Components                                                    | Relative effort | Cost risk                                                                 | Fit                                                                                            |
| ---------------------------- | ------------------------------------------------------------- | --------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| A1 – Supabase + Vercel       | Vercel static Angular + Supabase hosted backend               | Low             | Low initially, usage/plan limits need checking                            | Recommended only if hosted processing is approved under NFR-07                                 |
| A2 – Supabase + Netlify      | Netlify static Angular + Supabase hosted backend              | Low             | Low initially, usage/plan limits need checking                            | Equivalent alternative to A1; choose based on team familiarity                                 |
| A3 – Supabase + GitHub Pages | GitHub Pages static Angular + Supabase hosted backend         | Medium          | Low                                                                       | Possible for a public demo; SPA route handling and environment configuration require testing   |
| B1 – Custom API + PostgreSQL | Static Angular host + separately hosted Node API + PostgreSQL | Medium–high     | Depends on API/database free tiers                                        | Better fit if the team needs backend control; still a third-party processor unless self-hosted |
| B2 – Local/self-hosted       | Angular + self-hosted API/PostgreSQL                          | High            | Infrastructure may be low for a local demo, but operations are team-owned | Best control over task-data processing; operational responsibility increases                   |

## 4. Recommendation and decision gate

For the selected Variant A, start with **A1 (Vercel + Supabase)** or **A2 (Netlify + Supabase)** for a student demo, after checking current plan limits, data region, retention, terms and the team's account eligibility. The specific static host is not architecturally important; the Supabase project is the more consequential dependency.

## 5. Cost verification checklist

Before deployment, record:

- Date checked and selected plan for each provider.
- Monthly price and included quotas (database storage, egress, function invocations, build minutes, bandwidth).
- Whether the project can be paused/suspended on inactivity and how that affects the live demo.
- Region and applicable data-processing terms.
- Whether the team needs a paid plan for backups, point-in-time recovery, custom domains or required availability.
- Expected monthly cost for the expected number of users and demo traffic.
