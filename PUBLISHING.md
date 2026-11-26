# Публикация на GitHub

Проект уже подготовлен: есть `LICENSE`, `.gitignore`, манифест PWA, service worker,
workflow для GitHub Pages и workflow, собирающий `LinkVault.zip` по тегу.

## 1. Репозиторий

Локальный git-репозиторий создан, первый коммит сделан, `origin` указывает на
`https://github.com/milleran41/MyLinks.git`. Если нужен другой репозиторий:

```bash
git remote set-url origin https://github.com/<user>/<repo>.git
```

Отправить на GitHub:

```bash
git push -u origin main
```

Рекомендуемые настройки репозитория (Settings → General):

- Description: `Local link manager — links, notes and screenshots in one table. Works offline, installs as an app.`
- Topics: `pwa`, `indexeddb`, `link-manager`, `bookmarks`, `vanilla-js`, `offline`, `microsoft-store`
- Website: `https://milleran41.github.io/MyLinks/`

## 2. GitHub Pages (адрес приложения)

Файл `index.html` открывает приложение, поэтому Pages нужен и для ссылки «открыть в
браузере», и для упаковки в Microsoft Store (магазину нужен публичный HTTPS-адрес).

1. Settings → Pages → Build and deployment → Source: **GitHub Actions**.
2. Сделайте push в `main` (или запустите workflow `Deploy web app to GitHub Pages`
   вручную на вкладке Actions).
3. Адрес приложения: `https://milleran41.github.io/MyLinks/`
   (сам файл: `https://milleran41.github.io/MyLinks/LinkVault.html`).

Workflow копирует в артефакт только нужные для сайта файлы — исходники, документация
и `.github` в публикацию не попадают.

## 3. Релиз со скачиваемым архивом

`LinkVault.zip` больше не хранится в репозитории: архив собирается автоматически.

```bash
git tag v1.1.0
git push origin v1.1.0
```

Workflow `Release` соберёт архив (`LinkVault.html`, `readme.txt`, `icon.png`, `README.md`,
`LICENSE`) и приложит его к релизу. Постоянная ссылка на последнюю версию:

```
https://github.com/milleran41/MyLinks/releases/latest/download/LinkVault.zip
```

Версия в теге, в `readme.txt` и в `CACHE_NAME` внутри `sw.js` должна совпадать — при
изменении `sw.js` браузер обновляет кэш приложения.

## 4. Проверка перед публикацией

```bash
# локальный статический сервер (service worker работает только по http/https)
python -m http.server 8123 --bind 127.0.0.1
```

Откройте `http://127.0.0.1:8123/` и проверьте в DevTools → Application: манифест
загружен, иконки на месте, service worker активен. Затем — установка
(Edge: `⋯` → Приложения → Установить этот сайт как приложение) и работа офлайн.

Для Microsoft Store — см. [STORE.md](STORE.md).
