# מִשְׁנַת הַשִּׁעוּרִים

פלטפורמה מרכזית לניהול שיעורי תורה, בתי כנסת ושירותי קהילה.

## מה יש פה

```
src/
├── App.tsx                    # Routing + Tenant + Auth context
├── types/index.ts             # TypeScript types מלאים
├── lib/
│   ├── supabase.ts            # Client + כל ה-API functions
│   ├── voice.ts               # TTS (ElevenLabs) + STT (Whisper)
│   └── automation.ts          # Email + WhatsApp + Push
├── styles/
│   └── design-system.css      # Design system מלא — Ember+Slate+Gold
├── components/
│   ├── payments/NedarimModal  # תשלום נדרים פלוס מלא
│   └── ...
├── pages/
│   ├── public/                # דפים ציבוריים
│   ├── dashboard/             # פורטל מגיד
│   ├── portal/                # פורטלי token (גבאי/ארגון)
│   └── admin/                 # לוח ניהול
└── supabase/
    ├── schema.sql             # DB schema מלא עם RLS + triggers
    └── functions/
        ├── send-email/        # 8 תבניות מייל — Resend
        ├── nedarim-charge/    # תשלום נדרים פלוס
        ├── nedarim-webhook/   # אישור + קבלה
        ├── ivr-agent/         # ימות המשיח — חיפוש חכם
        └── cron-daily/        # stale alerts + reminders
```

## התקנה

```bash
npm install
cp .env.example .env.local
# מלא את הערכים ב-.env.local
npm run dev
```

## Supabase Setup

1. צור פרויקט חדש ב-https://supabase.com
2. הרץ את `supabase/schema.sql` ב-SQL Editor
3. הגדר Edge Functions secrets (ראה `.env.example`)
4. הפעל Edge Functions:
```bash
supabase functions deploy send-email
supabase functions deploy nedarim-charge
supabase functions deploy nedarim-webhook
supabase functions deploy ivr-agent
supabase functions deploy cron-daily
```

## Cron Jobs (Supabase)

הוסף ב-SQL Editor:
```sql
-- Daily reminders + stale check — כל יום ב-06:00
SELECT cron.schedule('daily-tasks', '0 6 * * *',
  $$SELECT net.http_post(url:='https://YOUR_PROJECT.supabase.co/functions/v1/cron-daily',
    headers:='{"Content-Type":"application/json","Authorization":"Bearer SERVICE_ROLE_KEY"}'::jsonb,
    body:='{"job":"all"}'::jsonb) $$);
```

## Stack

- **Frontend**: React 18 + TypeScript + Vite
- **UI**: Custom Design System (Ember+Slate+Gold)
- **Fonts**: Fraunces + DM Sans + Noto Sans Hebrew
- **Backend**: Supabase (Postgres + Auth + Storage + Realtime + Edge Functions)
- **Search**: pgvector (semantic) + FTS (Hebrew)
- **Email**: Resend
- **Payments**: Nedarim Plus
- **Voice IVR**: ימות המשיח + OpenAI
- **Hosting**: Vercel
