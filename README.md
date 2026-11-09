# FinPower Go

FinPower Go is the mobile companion to the FinPower loan-management system. Sales reps manage clients and
loans in the field and generate PDF statements on the spot. Built with React Native, Expo SDK 58,
TypeScript and Supabase. A read-only client portal is available for existing Supabase auth users linked
to a client record; self-service client account creation is not included.

Client and loan applications can be created in-app by sales representatives. The Reports screen previews client, customer, loan,
repayment, outstanding-loan and transaction-statement reports and exports them as PDF or Excel-compatible
CSV. Settings includes password updates, an optional daily on-device portfolio reminder, report date and
currency formats, quick-help answers and app information.
Financial transaction history is intentionally not deletable from the mobile UI. Client records are archived instead of hard-deleted so loan and statement history remains auditable.

Representatives can record payments against approved loans from the loan details screen. FinPower Go
adds each payment to transaction history and recalculates total repaid and the remaining scheduled
balance from the loan's monthly repayment schedule.

- **Unit:** IS323 Mobile Application Development, PNG University of Technology

## Setup (from a clean clone)

Requires Node.js 22 LTS. Expo Go can be used for standard app development. Daily local reminders require
an Android development build because Expo Go does not include the required native notification module.

```bash
npm ci                 # reproduce the checked-in dependency lockfile
cp .env.example .env   # PowerShell: Copy-Item .env.example .env
# Fill in the local Supabase URL and anon/publishable key in .env
npm run doctor
npm run typecheck
npm run lint
npm start              # scan the QR code with Expo Go (use `npm run tunnel` if it won't connect)
```

Before the first EAS build, link the app to its EAS project if needed. Add `EXPO_PUBLIC_SUPABASE_URL` and
`EXPO_PUBLIC_SUPABASE_ANON_KEY` to the matching
`development`, `preview` and `production` environments in the EAS project settings. The build profiles
select those environments; credentials are deliberately not stored in `eas.json`. These `EXPO_PUBLIC_`
values are embedded in the app, so use only the Supabase project URL and public anon/publishable key,
never a service-role key. To build and install the Android development client, configure the `development`
environment, run `npm run build:dev` and install the resulting APK on the Android device. Open the
installed development build to enable local reminders.

Backend: create a Supabase project, run `supabase/schema.sql` in the SQL editor, register a user in the app,
then run `supabase/seed.sql` for sample data. Existing projects must apply the migrations in
`supabase/migrations/` in timestamp order. In particular, apply
`supabase/migrations/20261004020000_repair_loan_action_rpcs.sql` to ensure loan approval and rejection RPCs
are present, granted to authenticated users, and visible in the PostgREST schema cache.
Enable Supabase Realtime for `clients`, `loans` and
`transactions` to receive cross-device updates while the app is open.

## Environment variables

| Name                            | Description                                              |
| ------------------------------- | -------------------------------------------------------- |
| `EXPO_PUBLIC_SUPABASE_URL`      | Supabase project URL                                     |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | Public anon/publishable key (never the service-role key) |

## APIs and services

Supabase (Auth, Postgres, RLS), Open-Meteo (weather), Frankfurter (exchange rates), Expo Location,
Expo Print, Expo Sharing, AsyncStorage.

## Android release identity

The Android application ID is `com.finpowergo.mobile`. The app version is `1.2.0`, with Android
version code `3` in `app.json`. Production EAS builds use remote versioning and automatically increment
the remote Android build number; verify its current value in the EAS project before release.

## Build

```bash
npm run build:apk   # eas build --platform android --profile preview     -> signed APK
npm run build:aab   # eas build --platform android --profile production  -> signed AAB
```

## Documentation

See `docs/`: requirements, SDK 58 stack, data model and RLS, screen flow, API table, milestone checklist.

## Update highlights

- Role-aware UI: sales-rep workspace vs read-only client portal.
- Realtime query invalidation keeps clients, loans, transactions, dashboard and reports synchronized.
- Client editing added; client deletion changed to archive to preserve financial history.
- Mobile hard-delete/reset actions removed from the operational UI.
- Database RLS hardened so client accounts cannot create or mutate sales-rep operational records.
- Supabase RPCs enforce sales-rep role server-side for approvals, payments and status changes.
- Environment examples contain placeholders only; never commit a real `.env` file.

## Known limitations

- Client access is read-only and requires an existing Supabase auth user linked to a client record; self-service client account creation is not included.
- Daily reminders are local device notifications and require an Android development build; remote push delivery is not configured.
- The app requires a configured Supabase project and its schema/migrations; example environment values are placeholders only.

## AI assistance

Claude was used to draft the initial scaffold and documentation. Generated material was reviewed and
revised by the project author; dependency compatibility can be checked with `npm run doctor`.
"# FinPowerGo" 
