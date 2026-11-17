// Supabase: Project Settings -> API sahifasidan oling.
// Faqat "anon public" kalitni qo'ying. "service_role" kalitni HECH QACHON bu yerga yozmang.
window.APP_CONFIG = {
  SUPABASE_URL: "",      // masalan: https://abcdxyz.supabase.co
  SUPABASE_ANON_KEY: ""  // uzun "anon public" kalit
};
-- Supabase -> SQL Editor ichida bir marta ishga tushiring.

create table if not exists books (
  id uuid primary key default gen_random_uuid(),
  title text not null,
  author text default '',
  category text default 'Kitoblar',
  emoji text default '📖',
  color text default '#1864ab',
  description text default '',
  chapters jsonb not null default '[]',
  published boolean not null default true,
  created_at timestamptz not null default now()
);

create table if not exists settings (
  key text primary key,
  value text not null default ''
);

alter table books enable row level security;
alter table settings enable row level security;

-- Hamma faqat e'lon qilingan kitoblarni o'qiy oladi; admin (login qilgan) hammasini ko'radi
create policy "books read" on books for select
  using (published = true or auth.role() = 'authenticated');
-- Yozish, o'zgartirish, o'chirish faqat login qilgan admin uchun
create policy "books admin write" on books for all to authenticated
  using (true) with check (true);

create policy "settings read" on settings for select using (true);
create policy "settings admin write" on settings for all to authenticated
  using (true) with check (true);

insert into settings (key, value) values
  ('app_title', '📚 Kitoblar'),
  ('app_subtitle', 'O''zbekcha tarjimadagi qiziqarli kitoblar'),
  ('accent_color', '#2a6df4'),
  ('notice', '')
on conflict (key) do nothing;
