# Публикация в Microsoft Store

Microsoft Store принимает приложение как **MSIX-пакет с упакованным PWA** (hosted app).
Отдельный `.exe` не нужен: магазин упаковывает сайт, а данные остаются в IndexedDB
браузера/Edge WebView. Пакет генерируется в PWABuilder по публичному HTTPS-адресу, а
идентификаторы берутся из Partner Center — поэтому шаги ниже выполняются в аккаунте
владельца, но всё, что нужно приложению, уже лежит в репозитории.

## Что уже готово в проекте

| Файл | Зачем |
| --- | --- |
| `manifest.webmanifest` | имя, иконки 192/512 + maskable, `start_url`, `display: standalone`, скриншоты |
| `sw.js` | service worker: установка приложения и офлайн-режим |
| `icon-192.png`, `icon-512.png`, `icon-maskable-512.png` | иконки PWA и плитки MSIX |
| `store/logo-300.png` | логотип листинга 300×300 (требование Store) |
| `store/screenshot-*-1366x768.png` | скриншоты листинга, 16:9, минимум 1366×768 |
| `store/listing-ru.md`, `store/listing-en.md` | тексты описания, ключевые слова, категория |
| `PRIVACY.md` | политика конфиденциальности (без неё заявку не примут) |
| `.github/workflows/pages.yml` | публичный HTTPS-адрес приложения для упаковки |

## 1. Адрес приложения

Сначала выполните шаги из [PUBLISHING.md](PUBLISHING.md), чтобы заработал GitHub Pages.
Адрес для магазина:

```
https://milleran41.github.io/MyLinks/LinkVault.html
```

Проверьте в браузере: страница открывается по HTTPS, в DevTools → Application есть
манифест, иконки и активный service worker, приложение устанавливается как PWA.

## 2. Аккаунт разработчика и резервирование имени

Регистрация в Store сейчас бесплатная, платить взнос не нужно — требуется только
проверка личности (государственный ID + селфи с телефона).

1. <https://storedeveloper.microsoft.com/> → **Get started for free** → **Individual developer**.
   Начинать нужно именно с этой страницы: вход напрямую в Partner Center показывает
   старый поток с платной регистрацией.
2. Войдите личным Microsoft-аккаунтом, пройдите проверку личности, заполните профиль →
   **Go to Partner Center dashboard**.
3. <https://partner.microsoft.com/ru-ru/dashboard/apps-and-games/overview> → **New product**
   → **MSIX or PWA app** → имя `LinkVault` → **Check availability** → **Reserve product name**.
   Зарезервированное имя хранится 3 месяца, пока не отправлена заявка.
4. Откройте продукт → **Product management → Product identity** и скопируйте значения:

```
Package/Identity/Name            (например 12345Publisher.LinkVault)
Package/Identity/Publisher       (CN=...)
Package/Properties/PublisherDisplayName
Package family name              (нужен только для проверки установки)
```

Первые три значения попадают в манифест пакета. Если они не совпадут с Product identity,
Partner Center отклонит загрузку — это самая частая ошибка первой отправки.

## 3. Сборка MSIX

Пакет для магазина — hosted PWA: внутри нет файлов приложения, только манифест, который
открывает `https://milleran41.github.io/MyLinks/LinkVault.html` в Edge.

1. <https://www.pwabuilder.com> → адрес `https://milleran41.github.io/MyLinks/LinkVault.html`
   → **Start**. Отчёт должен быть зелёным (manifest, service worker, HTTPS).
2. **Package for Stores → Windows** → вставьте значения из Product identity:
   Package ID = `Package/Identity/Name`, Publisher ID = `Package/Identity/Publisher`,
   Publisher display name = `Package/Properties/PublisherDisplayName` →
   **Download Package**.
3. В архиве: `LinkVault.msixbundle` (Windows 10 2004 и новее), `*.classic.appxbundle`
   (старые сборки Windows), `appxmanifest.xml` и тестовый сертификат для локальной
   установки.
4. Для магазина подпись не нужна: Partner Center подписывает пакет при сертификации.
   Тестовый сертификат из архива загружать не нужно — он только для локальной проверки.
5. Версия пакета состоит из четырёх чисел: сейчас `1.1.1.0`. Для обновления она должна быть
   выше предыдущей.

Локальная сборка без PWABuilder тоже возможна: `makeappx.exe` из Windows SDK на этой
машине есть (C:/Program Files (x86)/Windows Kits/10/App Certification Kit/makeappx.exe),
но hosted-пакет генерирует закрытый `pwa_builder.exe` от Edge — он же считает
`resources.pri` и `--app-id`, — поэтому повторять его вручную рискованно.

Проверка перед отправкой (необязательно, но полезно): установить тестовый сертификат,
выполнить `Add-AppxPackage .\LinkVault.msixbundle`, запустить приложение из «Пуск» и
проверить сохранение ссылок, Ctrl+V, «Сохранить»/«Загрузить» и работу без интернета.

## 4. Заявка в Partner Center

**New submission** для продукта `LinkVault`:

1. **Packages** — загрузите `.msix`. Версия в манифесте должна быть выше предыдущей
   (например `1.1.1.0`).
2. **Properties**:
   - Category: **Productivity**
   - Privacy policy URL: `https://github.com/milleran41/MyLinks/blob/main/PRIVACY.md`
   - Support/website: `https://github.com/milleran41/MyLinks`
   - System requirements: Windows 10 version 1809 или новее, Edge WebView2
3. **Age ratings** — заполните анкету IARC (насилие/контент отсутствуют → 3+).
4. **Store listings** — для каждого языка (ru-RU, en-US) вставьте поля из
   `store/listing-ru.md` / `store/listing-en.md`, загрузите:
   - логотип `store/logo-300.png`;
   - скриншоты `store/screenshot-ru-1366x768.png` (для ru-RU) и
     `store/screenshot-en-1366x768.png` (для en-US).
5. **Notes for certification** — короткая инструкция для тестировщиков:
   > LinkVault works fully offline and needs no account. Data is stored locally in
   > IndexedDB. To test: add a link in the "Link" column, paste a screenshot with Ctrl+V,
   > press "Save" to export a JSON backup, then "Load" and pick that file.
6. **Submit** → сертификация обычно занимает от нескольких часов до нескольких дней.

## 5. Обновления

1. Меняйте версию: `sw.js` → `CACHE_NAME`, `readme.txt` → заголовок, тег `vX.Y.Z`.
2. Push в `main` — Pages обновит сайт автоматически.
3. В PWABuilder повторите сборку с тем же Product identity, увеличив версию пакета
   (`1.1.1.0` и т. д.), и загрузите новый `.msix` в Partner Center.

## Ограничения, о которых стоит помнить

- Магазину нужен публичный HTTPS-адрес; `file://` и локальный сервер не подойдут.
- `showSaveFilePicker` (выбор места для бэкапа) поддерживается в Edge/Chrome. В
  упакованном PWA при отказе браузера приложение скачивает файл в «Загрузки» — это
  штатное поведение, отдельная доработка не нужна.
- Кнопка «О программе» открывает `readme.txt` по относительной ссылке — файл должен
  быть в публикации GitHub Pages (workflow его копирует).
