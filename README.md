# majordomo-mysensor-php8

**MajorDomo MySensors (PHP 8 fork)** — модуль [MySensors](https://www.mysensors.org/) для [MajorDomo](http://smartliving.ru/): приём и отправка сообщений от сенсоров/актуаторов через шлюз (TCP или COM-порт), привязка значений к свойствам объектов, OTA-обновление прошивок узлов.

> Форк оригинального модуля, адаптированный для работы на **PHP 7.x и PHP 8.x** (8.0–8.3).

## Возможности

- Поддержка шлюзов двух типов: **Serial (COM)** и **TCP (Ethernet/WiFi)**
- Автоматическая регистрация узлов и сенсоров (Presentation / Set / Req / Internal / Stream)
- Автовыдача Node ID (авто или ручной режим)
- Привязка значений сенсоров к свойствам объектов MajorDomo (`LINKED_OBJECT.LINKED_PROPERTY`)
- Управление через свойства объектов (Set с поддержкой ACK и очереди для спящих узлов)
- OTA-обновление прошивок узлов из `.hex` (Intel HEX → bin, CRC16)
- Мониторинг состояния: батарея, heartbeat, offline-детект, очередь отправки
- Веб-интерфейс: шлюзы, узлы, сенсоры, прошивки, очередь, журнал

## Требования

| Компонент | Версия |
|---|---|
| PHP | **7.x или 8.x** (проверено на 8.1–8.3) |
| MajorDomo | последняя стабильная |
| Расширения PHP | `sockets` (для TCP-шлюза), `mbstring` (рекомендуется), `json` |

> **Примечание для PHP 8.2+:** функция `utf8_encode()` объявлена устаревшей — модуль использует собственную обёртку `msUtf8Encode()`, поэтому предупреждений нет.

## Установка

1. Скопируйте папку модуля в `/var/www/html/modules/mysensor` (или в `modules` вашей установки MajorDomo).
2. В панели MajorDomo откройте **Модули → Модули и приложения → MySensors** — модуль установится автоматически (создаст таблицы `msgates`, `msnodes`, `msnodeval`, `msnodesens`, `msbins`, `mssendstack`, `msnodestate`).
3. Добавьте шлюз:
   - **Serial**: URL вида `COM3` или `/dev/ttyUSB0`, скорость 115200
   - **TCP**: URL вида `192.168.1.10:5003`
4. Запустите цикл: добавить `/var/www/html/scripts/cycle_mysensor.php` в автозапуск (MajorDomo сам предложит запустить цикл при первом открытии настроек).

## Изменения (форк ivanovohostru-cloud)

Совместимость с PHP 8.x:

- PHP 4-стиль конструкторы (`mysensor()`, `PhpSerial()`, `IntelHex()`, `MySensorMasterCom()`, `MySensorMasterTCP()`) заменены на `__construct()`
- Кривые смещения строк `$str{0}` → `$str[0]` (fatal в PHP 8)
- Вызовы вида `MySensorMaster::connect()` → `parent::connect()`
- Устаревший `utf8_encode()` заменён на обёртку с `mb_convert_encoding()` (fallback на `utf8_encode` при отсутствии mbstring)
- `list()` + `explode()` переписан явно
- Исправлен баг в `crcA001()` (неопределённая переменная)
- Исправлен регистр имени класса `MySensorMasterTcp` → `MySensorMasterTCP`

## Структура

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

## Лицензия

GPL 2.0 (как оригинальный проект).
