# WORK21 Frontend — Руководство для Claude

## Обзор проекта

WORK21 Frontend — веб-приложение на Next.js 14, соединяющее студентов Школы 21 с заказчиками. Платформа предоставляет интерфейс для создания проектов, подачи заявок, управления задачами и интеграции с AI-агентами.

## Технологический стек

| Технология | Версия | Назначение |
|------------|--------|------------|
| Next.js | 14.2.0 | React-фреймворк с App Router |
| React | 18.2.0 | UI библиотека |
| TypeScript | 5.3.3 | Типизация |
| Tailwind CSS | 3.4.1 | Стилизация |
| lucide-react | 0.350.0 | Иконки |
| clsx | 2.1.0 | Утилита для классов |

## Структура проекта

```
src/
├── app/                          # Next.js App Router
│   ├── layout.tsx                # Корневой layout (метаданные, провайдеры)
│   ├── page.tsx                  # Главная страница (лендинг)
│   ├── providers.tsx             # Провайдеры контекстов
│   ├── globals.css               # Глобальные стили, CSS-переменные
│   ├── login/page.tsx            # Страница входа
│   ├── register/page.tsx         # Страница регистрации
│   ├── students/page.tsx         # Лендинг для студентов
│   ├── customers/page.tsx        # Лендинг для заказчиков
│   ├── how-it-works/page.tsx     # Как это работает
│   └── dashboard/                # Защищённая зона (требует авторизации)
│       ├── layout.tsx            # Layout дашборда с sidebar
│       ├── page.tsx              # Главная дашборда
│       ├── projects/             # Проекты
│       │   ├── page.tsx          # Список проектов
│       │   ├── new/page.tsx      # Создание проекта
│       │   └── [id]/             # Детали проекта
│       │       ├── page.tsx      # Страница проекта
│       │       └── apply/page.tsx # Подача заявки
│       ├── applications/page.tsx # Мои заявки (для студентов)
│       ├── profile/page.tsx      # Профиль пользователя
│       ├── rating/page.tsx       # Рейтинг и лидерборд
│       ├── settings/page.tsx     # Настройки
│       ├── students/page.tsx     # Список студентов (для заказчиков)
│       └── users/[id]/page.tsx   # Профиль другого пользователя
├── components/                   # React-компоненты
│   ├── index.ts                  # Реэкспорт всех компонентов
│   ├── Header.tsx                # Шапка сайта
│   ├── Footer.tsx                # Подвал сайта
│   ├── Hero.tsx                  # Hero-секция лендинга
│   ├── Features.tsx              # Секция преимуществ
│   ├── AIAgents.tsx              # Секция AI-агентов
│   ├── HowItWorks.tsx            # Секция "Как это работает"
│   ├── CTA.tsx                   # Call-to-action секция
│   └── ThemeToggle.tsx           # Переключатель темы
└── lib/                          # Утилиты и контексты
    ├── api.ts                    # API-клиент для backend
    ├── auth-context.tsx          # Контекст авторизации
    └── theme-context.tsx         # Контекст темы (light/dark)
```

## Архитектурные решения

### App Router (Next.js 14)

- Все страницы используют `'use client'` для клиентского рендеринга
- Директории с `page.tsx` автоматически становятся маршрутами
- `[id]` — динамические сегменты URL
- `layout.tsx` — общие обёртки для группы страниц

### Контексты (React Context)

**AuthContext** (`lib/auth-context.tsx`):
```tsx
const { user, isLoading, isAuthenticated, login, register, logout, refreshUser } = useAuth();
```
- Хранит текущего пользователя
- Управляет JWT-токеном в localStorage
- Автоматический редирект при истечении токена

**ThemeContext** (`lib/theme-context.tsx`):
```tsx
const { theme, toggleTheme } = useTheme();
```
- Поддержка light/dark тем
- Сохранение в localStorage
- Мгновенное переключение без мерцания

### API-клиент

Расположен в `lib/api.ts`. Структура:

```typescript
// Импорт
import { api, User, Project, ApiError } from '@/lib/api';

// Использование
const user = await api.users.getMe();
const projects = await api.projects.getList();
const estimation = await api.estimator.estimate(prompt);
```

**Модули API:**
- `api.auth` — регистрация, вход
- `api.users` — пользователи, профили, лидерборд
- `api.projects` — проекты, задачи, заявки
- `api.estimator` — интеграция с LLM для оценки

## Стилизация

### CSS-переменные (темизация)

Определены в `globals.css`:

```css
:root, .dark {
  --color-bg: #0a0a0f;
  --color-card: #12121a;
  --color-border: #1e1e2a;
  --color-text: #e5e5e5;
  --color-text-secondary: #9ca3af;
  --color-accent-green: #10b981;
  --color-accent-blue: #3b82f6;
  --color-accent-violet: #8b5cf6;
}

.light {
  --color-bg: #f8fafc;
  --color-card: #ffffff;
  /* ... */
}
```

### Использование в компонентах

```tsx
// Inline стили с CSS-переменными
<div style={{ background: 'var(--color-card)', color: 'var(--color-text)' }}>

// Tailwind-классы
<div className="bg-accent-green text-white">

// Кастомные классы
<div className="glass-card btn-primary gradient-text">
```

### Кастомные утилитарные классы

| Класс | Описание |
|-------|----------|
| `.glass-card` | Стеклянный эффект (backdrop-blur) |
| `.btn-primary` | Зелёная кнопка с hover-эффектом |
| `.btn-secondary` | Серая кнопка |
| `.btn-outline` | Кнопка с обводкой |
| `.gradient-text` | Градиентный текст |
| `.bg-grid` | Фоновая сетка |

## Роли пользователей

### Student (Студент)
- Просмотр открытых проектов
- Подача заявок на проекты
- Выполнение задач
- Просмотр рейтинга и лидерборда

### Customer (Заказчик)
- Создание проектов
- Просмотр заявок
- Назначение исполнителей
- Оценка результатов

### Навигация по ролям

```tsx
// dashboard/layout.tsx
const studentNavigation = [
  { name: 'Проекты', href: '/dashboard/projects' },
  { name: 'Мои заявки', href: '/dashboard/applications' },
  // ...
];

const customerNavigation = [
  { name: 'Мои проекты', href: '/dashboard/projects' },
  { name: 'Создать проект', href: '/dashboard/projects/new' },
  // ...
];
```

## Интеграция с Backend

### Переменные окружения

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_ESTIMATOR_API_URL=http://localhost:8080
```

### Аутентификация

1. Токен хранится в `localStorage.access_token`
2. Автоматически добавляется в заголовок `Authorization: Bearer <token>`
3. При 401 ошибке — автоматический logout

### Типы данных

```typescript
interface User {
  id: number;
  email: string;
  first_name: string;
  last_name: string;
  role: 'student' | 'customer' | 'admin';
  rating_score: number;
  // ...
}

interface Project {
  id: number;
  title: string;
  description: string;
  budget: number;
  status: 'draft' | 'open' | 'in_progress' | 'review' | 'completed' | 'cancelled';
  // ...
}
```

## Паттерны разработки

### Защита страниц

```tsx
// Вариант 1: useEffect в компоненте
useEffect(() => {
  if (!isLoading && !isAuthenticated) {
    router.push('/login');
  }
}, [isLoading, isAuthenticated, router]);

// Вариант 2: HOC withAuth
export default withAuth(ProtectedPage, { allowedRoles: ['customer'] });
```

### Обработка ошибок API

```tsx
try {
  const data = await api.projects.create(projectData);
} catch (err) {
  if (err instanceof ApiError) {
    setError(err.message); // Сообщение от сервера
  } else {
    setError('Произошла ошибка');
  }
}
```

### Состояния загрузки

```tsx
const [isLoading, setIsLoading] = useState(true);
const [isSubmitting, setIsSubmitting] = useState(false);

// Показ спиннера
{isLoading && <Loader2 className="animate-spin" />}
```

## Компоненты лендинга

Все компоненты экспортируются из `components/index.ts`:

```tsx
import { Header, Hero, Features, AIAgents, HowItWorks, CTA, Footer } from '@/components';
```

## Команды

```bash
# Разработка
npm run dev          # Запуск dev-сервера на :3000

# Сборка
npm run build        # Production build
npm run start        # Запуск production

# Линтинг
npm run lint         # ESLint
```

## Docker

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

```yaml
# docker-compose.yml
services:
  frontend:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://backend:8000
```

---

## Критические правила для Claude

### ✅ ДЕЛАТЬ

1. Использовать `'use client'` для страниц с интерактивностью
2. Использовать CSS-переменные для цветов (поддержка тем)
3. Типизировать все props и состояния
4. Обрабатывать состояния loading/error/success
5. Использовать `lucide-react` для иконок
6. Следовать структуре App Router

### ❌ НЕ ДЕЛАТЬ

1. Не использовать хардкод цветов (кроме accent в Tailwind)
2. Не создавать новые CSS-файлы без необходимости
3. Не использовать `getServerSideProps` (это Pages Router)
4. Не хранить секреты в коде
5. Не генерировать MD-файлы без явного запроса

### Примеры кода

**Новая страница:**
```tsx
'use client';

import { useState, useEffect } from 'react';
import { useAuth } from '@/lib/auth-context';

export default function NewPage() {
  const { user, isLoading } = useAuth();
  const [data, setData] = useState(null);

  if (isLoading) {
    return <div>Загрузка...</div>;
  }

  return (
    <div style={{ background: 'var(--color-bg)' }}>
      {/* Контент */}
    </div>
  );
}
```

**Новый компонент:**
```tsx
import { SomeIcon } from 'lucide-react';

interface Props {
  title: string;
  onClick?: () => void;
}

export default function MyComponent({ title, onClick }: Props) {
  return (
    <button 
      onClick={onClick}
      className="glass-card px-4 py-2 rounded-lg"
      style={{ color: 'var(--color-text)' }}
    >
      <SomeIcon className="w-5 h-5" />
      {title}
    </button>
  );
}
```

**API вызов:**
```tsx
import { api, ApiError } from '@/lib/api';

async function fetchData() {
  try {
    const result = await api.projects.getList();
    setProjects(result);
  } catch (err) {
    if (err instanceof ApiError) {
      console.error('API Error:', err.status, err.message);
    }
  }
}
```

