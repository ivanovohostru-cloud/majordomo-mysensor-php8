# majordomo-mysensor-php8

[English](#english) | [Русский](#русский)

---

## English

**MajorDomo MySensors (PHP 8 fork)** — a [MySensors](https://www.mysensors.org/) module for [MajorDomo](http://smartliving.ru/): receive and send messages from sensors/actuators via a gateway (TCP or COM port), link values to object properties, OTA firmware updates for nodes.

> Fork of the original module, adapted to work on **PHP 7.x and PHP 8.x** (8.0–8.3).

### Features

- Two gateway types: **Serial (COM)** and **TCP (Ethernet/WiFi)**
- Automatic registration of nodes and sensors (Presentation / Set / Req / Internal / Stream)
- Automatic Node ID assignment (auto or manual mode)
- Linking sensor values to MajorDomo object properties (`LINKED_OBJECT.LINKED_PROPERTY`)
- Control via object properties (Set with ACK support and a queue for sleeping nodes)
- OTA firmware updates from `.hex` files (Intel HEX → bin, CRC16)
- Status monitoring: battery, heartbeat, offline detection, send queue
- Web interface: gates, nodes, sensors, firmware, queue, log

### Requirements

| Component | Version |
|---|---|
| PHP | **7.x or 8.x** (tested on 8.1–8.3) |
| MajorDomo | latest stable |
| PHP extensions | `sockets` (for TCP gateway), `mbstring` (recommended), `json` |

> **Note for PHP 8.2+:** `utf8_encode()` is deprecated — the module uses its own `msUtf8Encode()` wrapper, so no warnings are emitted.

### Installation

1. Copy the module folder to `/var/www/html/modules/mysensor` (or the `modules` directory of your MajorDomo installation).
2. In the MajorDomo panel open **Modules → Modules and applications → MySensors** — the module installs automatically (creates tables `msgates`, `msnodes`, `msnodeval`, `msnodesens`, `msbins`, `mssendstack`, `msnodestate`).
3. Add a gateway:
   - **Serial**: URL like `COM3` or `/dev/ttyUSB0`, baud rate 115200
   - **TCP**: URL like `192.168.1.10:5003`
4. Run the cycle: add `/var/www/html/scripts/cycle_mysensor.php` to autostart (MajorDomo will offer to start the cycle on first open of the module settings).

### Changes (ivanovohostru-cloud fork)

PHP 8.x compatibility:

- PHP 4-style constructors (`mysensor()`, `PhpSerial()`, `IntelHex()`, `MySensorMasterCom()`, `MySensorMasterTCP()`) replaced with `__construct()`
- Curly brace string offsets `$str{0}` → `$str[0]` (fatal in PHP 8)
- `MySensorMaster::connect()` calls → `parent::connect()`
- Deprecated `utf8_encode()` replaced with a `mb_convert_encoding()` wrapper (falls back to `utf8_encode` when mbstring is unavailable)
- `list()` + `explode()` rewritten explicitly
- Fixed a bug in `crcA001()` (undefined variable)
- Fixed class name case `MySensorMasterTcp` → `MySensorMasterTCP`
- Bundled Font Awesome distribution trimmed to essentials: only `all.min.css` + webfonts (~1 MB instead of ~1100 files of the full dist)

### Structure

```
modules/mysensor/
  mysensor.class.php   — module class (panel, DB, message handling)
  phpMS.php            — abstract gateway base class + protocol dictionaries
  phpMSTcp.php         — TCP gateway
  phpMSCom.php         — Serial gateway
  PhpSerial.php        — COM port handling
  intelhex.php         — Intel HEX parser
  crcs.php             — CRC16
scripts/cycle_mysensor.php — background gateway processing cycle
templates/mysensor/   — web interface templates
languages/            — localization (ru, ua)
```

### License

GPL 2.0 (same as the original project).

---

## Русский

**MajorDomo MySensors (PHP 8 fork)** — модуль [MySensors](https://www.mysensors.org/) для [MajorDomo](http://smartliving.ru/): приём и отправка сообщений от сенсоров/актуаторов через шлюз (TCP или COM-порт), привязка значений к свойствам объектов, OTA-обновление прошивок узлов.

> Форк оригинального модуля, адаптированный для работы на **PHP 7.x и PHP 8.x** (8.0–8.3).

### Возможности

- Поддержка шлюзов двух типов: **Serial (COM)** и **TCP (Ethernet/WiFi)**
- Автоматическая регистрация узлов и сенсоров (Presentation / Set / Req / Internal / Stream)
- Автовыдача Node ID (авто или ручной режим)
- Привязка значений сенсоров к свойствам объектов MajorDomo (`LINKED_OBJECT.LINKED_PROPERTY`)
- Управление через свойства объектов (Set с поддержкой ACK и очереди для спящих узлов)
- OTA-обновление прошивок узлов из `.hex` (Intel HEX → bin, CRC16)
- Мониторинг состояния: батарея, heartbeat, offline-детект, очередь отправки
- Веб-интерфейс: шлюзы, узлы, сенсоры, прошивки, очередь, журнал

### Требования

| Компонент | Версия |
|---|---|
| PHP | **7.x или 8.x** (проверено на 8.1–8.3) |
| MajorDomo | последняя стабильная |
| Расширения PHP | `sockets` (для TCP-шлюза), `mbstring` (рекомендуется), `json` |

> **Примечание для PHP 8.2+:** функция `utf8_encode()` объявлена устаревшей — модуль использует собственную обёртку `msUtf8Encode()`, поэтому предупреждений нет.

### Установка

1. Скопируйте папку модуля в `/var/www/html/modules/mysensor` (или в `modules` вашей установки MajorDomo).
2. В панели MajorDomo откройте **Модули → Модули и приложения → MySensors** — модуль установится автоматически (создаст таблицы `msgates`, `msnodes`, `msnodeval`, `msnodesens`, `msbins`, `mssendstack`, `msnodestate`).
3. Добавьте шлюз:
   - **Serial**: URL вида `COM3` или `/dev/ttyUSB0`, скорость 115200
   - **TCP**: URL вида `192.168.1.10:5003`
4. Запустите цикл: добавить `/var/www/html/scripts/cycle_mysensor.php` в автозапуск (MajorDomo сам предложит запустить цикл при первом открытии настроек).

### Изменения (форк ivanovohostru-cloud)

Совместимость с PHP 8.x:

- PHP 4-стиль конструкторы (`mysensor()`, `PhpSerial()`, `IntelHex()`, `MySensorMasterCom()`, `MySensorMasterTCP()`) заменены на `__construct()`
- Кривые смещения строк `$str{0}` → `$str[0]` (fatal в PHP 8)
- Вызовы вида `MySensorMaster::connect()` → `parent::connect()`
- Устаревший `utf8_encode()` заменён на обёртку с `mb_convert_encoding()` (fallback на `utf8_encode` при отсутствии mbstring)
- `list()` + `explode()` переписан явно
- Исправлен баг в `crcA001()` (неопределённая переменная)
- Исправлен регистр имени класса `MySensorMasterTcp` → `MySensorMasterTCP`
- Встроенный дистрибутив Font Awesome сокращён до необходимого: только `all.min.css` + веб-шрифты (~1 МБ вместо ~1100 файлов полного дистрибутива)

### Структура

```
modules/mysensor/
  mysensor.class.php   — класс модуля (панель, БД, обработка сообщений)
  phpMS.php            — базовый абстрактный класс шлюза + словари протокола
  phpMSTcp.php         — TCP-шлюз
  phpMSCom.php         — Serial-шлюз
  PhpSerial.php        — работа с COM-портом
  intelhex.php         — парсер Intel HEX
  crcs.php             — CRC16
scripts/cycle_mysensor.php — фоновый цикл обработки шлюзов
templates/mysensor/   — шаблоны веб-интерфейса
languages/            — локализация (ru, ua)
```

### Лицензия

GPL 2.0 (как оригинальный проект).
