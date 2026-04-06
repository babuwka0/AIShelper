# Mermaid diagrams for task 6 - backup and recovery

Ниже приведены **две отдельные Mermaid-схемы** для задания 6:

1. схема резервного копирования информации;
2. схема восстановления ИТ-инфраструктуры после сбоев.

Они сделаны в более компактном виде, чтобы их было удобно:

- вставлять в отчет;
- открывать в Markdown;
- импортировать в draw.io через `Insert -> Advanced -> Mermaid`.

---

## 1. Схема резервного копирования информации

```mermaid
flowchart TB

    subgraph src["Источники данных"]
        db["Postgres Pro\nМИС, расписание, лечение"]
        vm["Виртуальные машины\nМИС, ALD Pro, почта,\nService Desk"]
        files["Файлы и снимки\nлаборатория, документы,\nархив изображений"]
        cfg["Конфигурации сети\nUserGate, Eltex"]
    end

    subgraph backup["Система резервного копирования"]
        rb["RuBackup\nкаталог копий,\nполитики хранения,\nконтроль restore"]
    end

    subgraph local["Локальный backup-контур"]
        localStore["Локальный backup storage\nбыстрое восстановление"]
    end

    subgraph dr["DR Site"]
        drStore["DR backup / restore\nрезервные копии\nи восстановление"]
    end

    subgraph offsite["Offsite / Offline"]
        obj["TATLIN.OBJECT / S3 в РФ\nimmutable offsite copy"]
        offline["Offline archive\nленты / съемные носители / сейф"]
    end

    db -->|"1. Часовые инкременты\n+ ежедневные полные копии"| rb
    vm -->|"2. Backup ВМ\nи снапшоты"| rb
    files -->|"3. Ежедневный\nинкрементальный backup"| rb
    cfg -->|"4. Backup конфигураций\nпри каждом изменении"| rb

    rb -->|"Основной поток (LAN)"| localStore
    rb -->|"Реплика backup"| drStore
    rb -->|"Offsite copy"| obj
    drStore -->|"Ежемесячный\noffline export"| offline
```

---

## 2. Схема восстановления ИТ-инфраструктуры после сбоев

```mermaid
flowchart TB

    start(["Инцидент / сбой"])
    detect["Обнаружение\nZabbix / пользователь / ИТ"]
    classify["Классификация\nлокальный / сервисный /\nкатастрофический"]
    isolate["Локализация\nотключение узла, VLAN,\nканала или сегмента"]
    decision{"Можно восстановить\nна основной площадке?"}

    localRecover["Локальное восстановление\nперезапуск сервиса,\nзапуск ВМ на оставшихся узлах,\nпереключение на standby"]
    drFailover["Failover на DR Site\nподъем ALD Pro,\nPostgres Pro, МИС,\nфайлов и кассы"]
    restore["Восстановление из backup\nлокальный backup /\nDR backup /\noffsite / offline"]
    validate["Проверка доступности\nМИС, БД, файлов,\nлаборатории, кассы,\nучетных записей"]
    normal["Возврат в штатный режим"]

    start --> detect --> classify --> isolate --> decision
    decision -->|"Да"| localRecover --> validate --> normal
    decision -->|"Нет"| drFailover --> restore --> validate --> normal
```

---

## 3. Короткие пояснения

### Что показывает первая схема

- из каких источников берутся данные;
- как ими управляет `RuBackup`;
- куда уходят локальные, DR, offsite и offline копии.

### Что показывает вторая схема

- как ИТ-служба реагирует на инцидент;
- когда хватает локального восстановления;
- когда требуется переход на `DR Site`;
- когда используются резервные копии.

---

## 4. Как это использовать в работе

Если нужна **одна** схема для отчета, обычно лучше вставлять:

- первую диаграмму в раздел **резервного копирования**;
- вторую диаграмму в раздел **плана восстановления после сбоев**.
