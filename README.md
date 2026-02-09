# Тренировочный дневник

Веб‑приложение для ведения тренировок с календарём, справочником упражнений, подходами, таймерами и графиками. Оптимизировано для мобильного использования (iPhone).

## Основные возможности
- Календарь тренировок с выбором даты и подсветкой сегодняшнего дня.
- Справочник упражнений по группам мышц (Грудь, Спина, Руки, Ноги, Плечи, Пресс).
- Добавление упражнений в тренировку из выпадающего списка.
- Подходы с весом, повторами и отдыхом, быстрый +1 мин.
- Таймер отдыха с визуальной полоской.
- Таймер длительности тренировки (старт с первого добавления упражнения).
- Настроение до/после тренировки (1–5 звезд).
- Графики по упражнению с выбором типа и периода.
- Экспорт данных в JSON.
- Автосинхронизация контента через Supabase (magic‑link).

## Как пользоваться
1. Выберите дату в календаре.
2. Установите настроение до/после тренировки.
3. В справочнике выберите группу мышц и упражнение.
4. Добавьте упражнение в тренировку.
5. Заполняйте подходы, используйте таймер отдыха.
6. Посмотрите графики прогресса по упражнению.

## Справочник и подходы
- Справочник хранит упражнения по группам.
- Выбор упражнения — через стандартный выпадающий список.
- Вес и повторы выбираются через листаемые списки, есть ручной ввод.
- Быстрое добавление подхода: "Повторить +1" (увеличивает повторы на 1).

## Графики
Доступные метрики:
- Макс. вес
- Тоннаж
- Средний вес
- Средние повторы
- Количество подходов
- 1RM (Epley)
- Топ‑сет (вес × повторы)

Периоды:
- С начала
- Последние 7/30/90 дней
- Свой диапазон (от/до)

## Синхронизация (Supabase)
Синхронизируется только контент:
- Тренировки
- Справочники упражнений

Интерфейсные настройки не синхронизируются.

### Таблицы Supabase
```sql
create table public.workouts (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users not null,
  date_key text not null,
  data jsonb not null,
  updated_at timestamptz default now(),
  unique (user_id, date_key)
);

create table public.catalogs (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users not null,
  "group" text not null,
  items jsonb not null default '[]'::jsonb,
  updated_at timestamptz default now(),
  unique (user_id, "group")
);
```

### RLS политики
```sql
alter table public.workouts enable row level security;
alter table public.catalogs enable row level security;

create policy "workouts_owner"
on public.workouts
for all
using (auth.uid() = user_id)
with check (auth.uid() = user_id);

create policy "catalogs_owner"
on public.catalogs
for all
using (auth.uid() = user_id)
with check (auth.uid() = user_id);
```

### Auth Redirect URL
В Supabase → Auth → URL Configuration:
- Site URL: `https://gahlan873-lang.github.io/workout-tracker/`
- Additional Redirect URLs: тот же URL

## Файлы
- `index.html` — приложение
- `README.md` — документация
