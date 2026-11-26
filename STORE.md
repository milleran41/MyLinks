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

## 2. Partner Center: аккаунт и имя продукта

1. <https://partner.microsoft.com/dashboard> → регистрация (индивидуальный аккаунт
   Microsoft Store стоит разово; для физлица нужен проверенный профиль).
2. **Apps and games → New product → MSIX or PWA app** → зарезервируйте имя `LinkVault`.
3. Откройте продукт → **Product management → Product identity** и скопируйте три значения:
   - `Package/Identity/Name`
   - `Package/Identity/Publisher` (вид `CN=...`)
   - `Package/Properties/PublisherDisplayName`

Эти значения должны попасть в пакет, иначе магазин отклонит загрузку.

## 3. Сборка MSIX в PWABuilder

1. <https://www.pwabuilder.com> → вставьте адрес приложения → **Start**.
2. Проверьте отчёт (manifest, service worker, HTTPS — всё должно быть зелёным).
3. **Package for Stores → Windows** → введите три значения из Product identity.
4. Скачайте пакет. В архиве будет `.msix` (тестовая подпись — только для локальной
   установки, для магазина её не используйте) и `appxmanifest.xml`.
5. Локальная проверка (необязательно): установите тестовый сертификат из архива,
   затем `Add-AppxPackage .\LinkVault.msix` в PowerShell и запустите приложение из меню
   «Пуск». Проверьте сохранение ссылок, скриншоты через `Ctrl+V`, кнопки «Сохранить» /
   «Загрузить» и работу без интернета.

Альтернатива без PWABuilder — собрать MSIX вручную (`makeappx` + `signtool` из Windows
SDK) по тому же `appxmanifest.xml`. Для магазина это не требуется: Microsoft подписывает
пакет сама при сертификации.

## 4. Заявка в Partner Center

**New submission** для продукта `LinkVault`:

1. **Packages** — загрузите `.msix`. Версия в манифесте должна быть выше предыдущей
   (например `1.1.0.0`).
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
