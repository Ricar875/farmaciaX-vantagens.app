# Loyalty Program — White-Label Template

Deploy on Vercel
1. Create a new repository on GitHub and upload only the `index.html` file (in the root directory).
2. Go to Vercel → "Add New Project" → import the repository.
3. No configuration is required — Vercel detects it as a static site and publishes directly.

Customize for each client.
Open `index.html`, go to the `CONFIG` block at the end of the file (near the `<script>` tag) and change:
- `brand`: client's brand name (appears at the top, on the card, and in the footer)
- `city`: customer's city/region
- `whatsapp`: contact number in the format 5575999999999
- `monthlySavings`: savings counter value

To change the colors, edit the variables at the top of `<style>` (`--navy`, `--gold`, `--coral`, `--river`).

To replace the example partners with the client's actual partners, edit the `partners` array in `<script>`.

## Administration panel (admin.html)
There is now a separate page — `admin.html` — where you (or the client) log in as administrator and add/remove real partners. This appears automatically on the website, without editing code.

Steps:
1. In `admin.html`, fill in `supabaseUrl` and `supabaseAnonKey` (the same ones from `index.html`).
2. In `adminEmails`, enter the email address you will be managing (this can be yours or the client's).
3. In Supabase, go to **Authentication → Users** and manually create this admin user (or create it through the site's registration and then promote it).
4. Run this additional SQL in the **SQL Editor**:

```sql
create table public.partners (
id uuid default gen_random_uuid() primary key,
cat text not null,
name text not null,
description text not null,
tag text not null,
created_at timestamptz default now()
);

alter table public.partners enable row level security;

Create the policy "anyone can see partners" on public.partners.
for select using (true);

Create policy "admin can add partners" on public.partners
for insert with check (auth.jwt() ->> 'email' in (' your-email@example.com '));

Create policy "admin can remove partners" on public.partners
for delete using (auth.jwt() ->> 'email' in (' your-email@example.com '));
```

Replace `' your-email@ example.com'` with the admin's actual email address (it must be the same as the one in `adminEmails` in `admin.html`).

5. Upload the `admin.html` file to the same repository as `index.html` (in the root directory, together). After deployment, the dashboard will be located at ` yoursite.vercel.app/admin.html` .

As long as no partners are registered, the site automatically displays examples (pharmacy, restaurant, etc.) — so it's never empty.

To activate login with an account (Supabase — free)
The page already has all the login/registration code ready. All that's missing is connecting it to a real database, which is free to start with:

1. Create an account at https://supabase.com and a new project.
2. In **Project Settings → API**, copy the **Project URL** and **anon public key**.
3. In `index.html`, find the `CONFIG` block and paste these two values ​​into `supabaseUrl` and `supabaseAnonKey`.
4. In Supabase, go to **SQL Editor** and run:

```sql
create table public.members (
id uuid references auth.users on delete cascade primary key,
name text not null,
card_number text not null,
created_at timestamp with time zone default now()
);

alter table public.members enable row level security;

create policy "select own profile" on public.members
for select using (auth.uid() = id);

create policy "insert own profile" on public.members
for insert with check (auth.uid() = id);
```

5. In **Authentication → Providers**, leave "Email" enabled (it's the default).
6. In **Authentication → Settings**, if you want to skip email confirmation (faster for testing), disable "Confirm email".

After that, the "Log in" button opens the actual registration/login process: the person creates an account, receives an automatically generated card number, and upon returning to the site is already logged in.

What's on the page?
Animated membership card (physical card design with a glow effect)
- Category/partner search
- 9 discount categories
Featured Partners List
- "How it works" section
Call for companies that want to join the club (WhatsApp)


