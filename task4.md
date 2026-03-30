# Mermaid diagrams for task 4

Ниже собраны диаграммы задания 4 для сети `«Зубич»` в формате `Mermaid`.

Их можно:

- использовать прямо в Markdown;
- вставить в `draw.io` через `Insert -> Advanced -> Mermaid`;
- брать как основу для ручной доработки схем.

---

## 1. High-level business process map

```mermaid
flowchart LR
    subgraph mainProcesses["Основные процессы"]
        booking["Запись пациента"]
        intake["Прием и оформление"]
        diagnosis["Диагностика и план лечения"]
        treatment["Лечение"]
        labOrder["Передача заказа в лабораторию"]
        labWork["Изготовление изделия"]
        returnClinic["Возврат в клинику и завершение лечения"]
        payment["Оплата и закрытие случая"]
    end

    subgraph supportProcesses["Вспомогательные процессы"]
        procurement["Закупка материалов и расходников"]
        hr["Кадровое оформление сотрудника"]
        itSupport["ИТ-поддержка рабочего места и оргтехники"]
        finance["Финансовый учет и отчетность"]
    end

    booking --> intake --> diagnosis --> treatment
    treatment --> labOrder --> labWork --> returnClinic --> payment

    procurement -.-> treatment
    procurement -.-> labWork
    hr -.-> intake
    hr -.-> treatment
    itSupport -.-> booking
    itSupport -.-> intake
    itSupport -.-> payment
    finance -.-> payment
    finance -.-> procurement
```

---

## 2. Core clinical and laboratory process

```mermaid
flowchart TB
    startNode(["Старт"])
    endNode(["Завершение"])

    subgraph patientLane["Пациент"]
        patientRequest["Обращение в клинику"]
        patientArrive["Прибытие на прием"]
        patientPay["Оплата"]
    end

    subgraph receptionLane["Администратор"]
        createBooking["Создание записи"]
        confirmArrival["Подтверждение явки и оформление"]
        closeVisit["Закрытие визита"]
    end

    subgraph dentistLane["Врач"]
        performExam["Осмотр и диагностика"]
        planTreatment["Формирование плана лечения"]
        needLab{"Нужна лаборатория?"}
        performTreatment["Лечение / установка"]
    end

    subgraph labLane["Лаборатория"]
        createLabOrder["Получение заказа"]
        fabricateItem["Изготовление изделия"]
        returnItem["Передача изделия в клинику"]
    end

    startNode --> patientRequest --> createBooking --> patientArrive --> confirmArrival
    confirmArrival --> performExam --> planTreatment --> needLab
    needLab -->|"Да"| createLabOrder --> fabricateItem --> returnItem --> performTreatment
    needLab -->|"Нет"| performTreatment
    performTreatment --> patientPay --> closeVisit --> endNode
```

---

## 3. Procurement process

```mermaid
flowchart TB
    startProc(["Старт"])
    endProc(["Завершение"])

    subgraph deptLane["Подразделение"]
        identifyNeed["Выявление потребности"]
        createRequest["Создание заявки"]
    end

    subgraph headLane["Руководитель подразделения"]
        approveRequest{"Заявка согласована?"}
    end

    subgraph financeLane["Финансы"]
        checkBudget{"Бюджет подтвержден?"}
    end

    subgraph procurementLane["Закупки"]
        prepareOrder["Подготовка заказа поставщику"]
    end

    subgraph supplierLane["Поставщик"]
        deliverGoods["Поставка материалов"]
    end

    subgraph warehouseLane["Склад / кладовщик"]
        receiveGoods["Приемка поставки"]
        updateInventory["Обновление учета остатков"]
    end

    startProc --> identifyNeed --> createRequest --> approveRequest
    approveRequest -->|"Нет"| createRequest
    approveRequest -->|"Да"| checkBudget
    checkBudget -->|"Нет"| createRequest
    checkBudget -->|"Да"| prepareOrder --> deliverGoods --> receiveGoods --> updateInventory --> endProc
```

---

## 4. IT support process

```mermaid
flowchart TB
    startIt(["Старт"])
    endIt(["Завершение"])

    subgraph userLane["Пользователь"]
        reportIssue["Сообщение о проблеме"]
        confirmResolved["Подтверждение решения"]
    end

    subgraph serviceDeskLane["Service Desk"]
        registerTicket["Регистрация заявки"]
        updateTicket["Обновление заявки"]
        closeTicket["Закрытие заявки"]
    end

    subgraph adminLane["Системный администратор"]
        reviewTicket["Просмотр заявки"]
        diagnoseIssue["Диагностика проблемы"]
        resolveIssue["Устранение проблемы"]
        resolvedDecision{"Проблема решена?"}
    end

    subgraph headItLane["Руководитель ИТ"]
        escalateIssue["Эскалация сложного случая"]
    end

    startIt --> reportIssue --> registerTicket --> reviewTicket --> diagnoseIssue --> resolveIssue --> resolvedDecision
    resolvedDecision -->|"Да"| updateTicket --> confirmResolved --> closeTicket --> endIt
    resolvedDecision -->|"Нет"| escalateIssue --> diagnoseIssue
```

---

## 5. System and business application landscape

```mermaid
flowchart LR
    subgraph clinicalApps["Клинические приложения"]
        crm["CRM / система записи"]
        mis["МИС стоматологии"]
        diagnostic["Система диагностических данных"]
        cash["Кассовый модуль"]
    end

    subgraph laboratoryApps["Лабораторные приложения"]
        labSystem["Лабораторная система"]
        fileStore["Файловое хранилище"]
    end

    subgraph officeApps["Офисные приложения"]
        accounting["Бухгалтерская система"]
        hrSystem["Кадровая система"]
        procurementSys["Система закупок"]
        emailSys["Корпоративная почта"]
    end

    subgraph systemServices["Системные сервисы"]
        serviceDesk["Service Desk"]
        iam["Учетные записи и доступы"]
        backup["Резервное копирование"]
    end

    crm --> mis
    mis <--> diagnostic
    mis <--> labSystem
    mis --> cash
    cash --> accounting
    procurementSys <--> accounting
    hrSystem --> iam
    serviceDesk --> iam
    fileStore --> labSystem
    fileStore --> mis
    emailSys --> crm
    emailSys --> procurementSys
    backup --> mis
    backup --> crm
    backup --> labSystem
    backup --> accounting
    backup --> hrSystem
    backup --> fileStore
```

---

## 6. Process-to-application mapping

```mermaid
flowchart LR
    subgraph processGroup["Бизнес-процессы"]
        p1["Запись пациента"]
        p2["Прием и диагностика"]
        p3["Лечение"]
        p4["Передача заказа в лабораторию"]
        p5["Изготовление изделия"]
        p6["Возврат в клинику и завершение"]
        p7["Оплата и закрытие случая"]
        p8["Закупка материалов"]
        p9["Кадровое оформление"]
        p10["ИТ-поддержка"]
        p11["Финансовый учет и отчетность"]
    end

    subgraph appGroup["Приложения"]
        a1["CRM / система записи"]
        a2["МИС стоматологии"]
        a3["Система диагностических данных"]
        a4["Лабораторная система"]
        a5["Кассовый модуль"]
        a6["Система закупок"]
        a7["Бухгалтерская система"]
        a8["Кадровая система"]
        a9["Service Desk"]
        a10["Учетные записи и доступы"]
        a11["Файловое хранилище"]
    end

    p1 --> a1
    p1 --> a2
    p2 --> a2
    p2 --> a3
    p3 --> a2
    p4 --> a2
    p4 --> a4
    p4 --> a11
    p5 --> a4
    p5 --> a11
    p6 --> a4
    p6 --> a2
    p7 --> a5
    p7 --> a7
    p7 --> a2
    p8 --> a6
    p8 --> a7
    p9 --> a8
    p9 --> a10
    p9 --> a9
    p10 --> a9
    p10 --> a10
    p11 --> a7
    p11 --> a8
```

---

## 7. Short note

Если какой-то блок рендерится слишком широко, лучше:

- укорачивать подписи;
- разбивать одну большую диаграмму на две;
- использовать `flowchart LR` для карт приложений;
- использовать `flowchart TB` для процессов.
