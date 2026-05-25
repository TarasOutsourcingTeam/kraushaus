# Kraus Haus — конспект проєкту

## Про проєкт

Односторінковий лендінг для будівельної компанії Kraus Haus (реставрація вілл у Тоскані).
Статичний HTML, без фреймворків. Два місця розгортання:

| | URL |
|---|---|
| **GitHub Pages** | https://tarasoutsourcingteam.github.io/kraushaus/ |
| **Хостинг** | http://kraushausl-test.com (веб-рут: `/home/buchen0/kraushausl-test.com/www/`) |

**GitHub репо:** https://github.com/TarasOutsourcingTeam/kraushaus

Основний файл сайту: `kraus-haus-italy.html` (index.html на GitHub Pages редиректить на нього).

---

## SSH доступ до хостингу

```
Host:    kraushausl-test.com  (або 3458512.buchen0.web.hosting-test.net)
User:    buchen0
Key:     ~/.ssh/kraushausl_key  (ED25519)
Port:    22
```

```bash
ssh -i ~/.ssh/kraushausl_key -o IdentitiesOnly=yes buchen0@kraushausl-test.com
```

Веб-рут на сервері: `~/kraushausl-test.com/www/`

---

## Що зроблено (сесія 2026-05-26)

### 1. Знесено WordPress з хостингу
- Видалено всі WP-файли з сервера: `wp-admin/`, `wp-includes/`, `wp-content/`, всі `wp-*.php`, `xmlrpc.php`, `index.php`, `license.txt`, `readme.html`
- На сервері залишився лише дефолтний `index.html` хостингу (плейсхолдер "Поздравляем, сайт создан!")
- Актуальний `kraus-haus-italy.html` на сервер **не завантажено** — сайт живе на GitHub Pages

### 2. Видалено локальні WP-файли
Були untracked (ніколи не комітились):
- `kraushaus-theme/` та `kraushaus-theme.tar.gz`
- `populate-acf.php`

### 3. Локалізовано 18 фото портфоліо (v2.9.16)
Всі картинки блоку "The Work We're Proud Of" тягнулись із зовнішнього сайту `kraus-haus.com/wp-content/uploads/2025/03/`.

Завантажено локально в `images/` з іменуванням `portfolio-[проєкт]-[номер].jpg`:

| Проєкт | Файли |
|---|---|
| Palaia 23 | portfolio-palaia-01..03.jpg |
| Bucha 20 | portfolio-bucha20-01..03.jpg |
| Bojarka 17 | portfolio-bojarka-01..03.jpg |
| Lisnyky 23 | portfolio-lisnyky-01..03.jpg |
| Bucha 18 | portfolio-bucha18-01..03.jpg |
| Kyiv 21 | portfolio-kyiv-01..03.jpg |

Замінено всі 36 посилань в HTML (18 у `<img src>` + 18 у `lightboxData`).

### 4. Виправлено CSS портфоліо на мобільному (v2.9.16 → v2.9.19)

**Структура блоку портфоліо:**
```html
<div class="proj-grid">
  <div class="portfolio-item proj-main">  <!-- велика ліва картинка -->
  <div class="proj-side">
    <div class="portfolio-item proj-side-item">  <!-- мала права верхня -->
    <div class="portfolio-item proj-side-item">  <!-- мала права нижня -->
```

**Мобільний CSS (560px) — фінальне рішення:**
```css
.proj-grid { display: flex; }
.proj-main { flex: 1 1 0; aspect-ratio: 1/1; min-width: 0; }
.proj-side { display: flex; flex: 2 1 0; min-width: 0; }
.proj-side-item { flex: 1 1 0; aspect-ratio: 1/1; min-width: 0; }
```

Результат: 3 рівних квадратних прев'ю в один рядок. `object-fit: cover` вже задано глобально на `.portfolio-item img`.

**Ключовий баг, знайдений по дорозі:**
CSS stylesheet має два блоки `@media (max-width: 560px)`. Перший (~рядок 1280) йде **до** глобального CSS для `.proj-grid` (~рядок 1456). Через однакову специфічність глобальний CSS перебивав мобільний override. Правила для proj-* треба ставити у **другий** `@media` блок (~рядок 1634), який іде після глобального CSS.

**Lightbox:** без змін — вже використовує `object-fit: contain; max-width: 88vw; max-height: 82vh` → показує повний оригінал.

---

## Поточні версії

| Версія | Зміна |
|---|---|
| v2.9.15 | meta description, Open Graph, Twitter Card |
| v2.9.16 | локалізація 18 фото портфоліо |
| v2.9.17–18 | спроби мобільного CSS (не спрацювали через баг каскаду) |
| **v2.9.19** | **фікс CSS каскаду — мобільне портфоліо працює** |

---

## Відомі проблеми

- Мобільна версія **інших секцій** (hero, про нас, форма, футер тощо) після WP-міграції не перевірялась
- На хостингу `kraushausl-test.com` стоїть дефолтна сторінка хостингу, не Kraus Haus

---

## Що планується далі

- Перевірити і виправити мобільну версію решти секцій сайту
- Вирішити питання деплою на `kraushausl-test.com` (завантажити `kraus-haus-italy.html` + `images/` на сервер)
