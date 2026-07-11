# UZI Mods — каталог

Публичный каталог контента для приложения [**UZI Mods**](https://github.com/e0xextazy/uzi-mods).

- `catalog.json` — манифест: список доступных компонентов (в git).
- `previews/` — превью-картинки компонентов (в git).
- **Сами zip-компоненты — в [Releases](../../releases), НЕ в git** (git не для гигабайтных
  бинарников; в Releases до 2 ГБ на файл, бесплатно). `.gitignore` намеренно блокирует `*.zip`.

Приложение читает `catalog.json` по raw-ссылке и качает каждый компонент по его `downloadUrl`
(ссылка на релиз-ассет).

> **raw-URL каталога** (укажи его в приложении как `catalogUrl`):
> `https://raw.githubusercontent.com/e0xextazy/uzi-mods-catalog/main/catalog.json`

## Как добавить компонент

1. Собери zip компонента (структура файлов как для установки).
2. Загрузи zip в **Release** этого репозитория (вкладка Releases → создать/выбрать релиз →
   прикрепить файл). Скопируй прямую ссылку на ассет — это `downloadUrl`.
3. Посчитай `sha256` и размер файла:
   - Windows PowerShell: `Get-FileHash .\component.zip -Algorithm SHA256` и `(Get-Item .\component.zip).Length`
   - Linux/macOS: `sha256sum component.zip` и `stat -c%s component.zip`
   - Либо через приложение: экран **«Инструменты редактора»** посчитает `sha256`/размер и
     предложит `install`-операции по содержимому zip.
4. Добавь запись в `components` в `catalog.json` (схема ниже), при желании положи превью в
   `previews/` и укажи его raw-ссылку в `preview`.
5. Закоммить `catalog.json` (+ превью). Новинка появится у пользователей при следующем запуске.

## Схема записи компонента

```jsonc
{
  "id": "zangetsu-redux",          // уникальный, только буквы/цифры/-/_/.  (используется как имя папки)
  "name": "Zangetsu Redux",
  "category": "redux",              // redux|gunpack|armor|map|sound|timecycle|tracer|hitEffect|crosshair|blips
  "author": "Zangetsu",
  "version": "1.2.0",
  "description": "Тёмный реалистичный редукс…",
  "preview": "https://raw.githubusercontent.com/e0xextazy/uzi-mods-catalog/main/previews/zangetsu.jpg",
  "downloadUrl": "https://github.com/e0xextazy/uzi-mods-catalog/releases/download/v1/zangetsu-1.2.0.zip",
  "sizeBytes": 734003200,
  "sha256": "ab12…",               // lowercase hex; приложение проверяет целостность после скачивания
  "install": [
    // значения "op" — camelCase
    { "op": "swapFile", "from": "update/update.rpf", "to": "update/update.rpf" },
    { "op": "copy",     "from": "x64/audio/sfx/foo.awc", "to": "x64/audio/sfx/foo.awc" },
    { "op": "rpfReplace",
      "archive": "update/update.rpf/x64/textures/script_txds.rpf",
      "entry": "chv.ytd",
      "from": "crosshair/chv.ytd" }
  ]
}
```

**Операции (`op`):** `copy` / `swapFile` — подстановка файла в папку игры; `rpfReplace` /
`rpfAdd` / `rpfDelete` — правка entry **внутри** `.rpf` (`archive` = путь к архиву,
`entry` = имя записи, `from` = путь в zip). Пути `from`/`to`/`archive`/`entry` — относительные,
без `..`.

## Правила каталога

Добавляй только **проверенные и разрешённые** на целевом сервере редуксы — это защита
пользователей от банов и сломанных сборок.
