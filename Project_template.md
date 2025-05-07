# Project_template

Это шаблон для решения проектной работы. Структура этого файла повторяет структуру заданий. Заполняйте его по мере работы над решением.

# Задание 1. Анализ и планирование

<aside>
💡

Чтобы составить документ с описанием текущей архитектуры приложения, можно часть информации взять из описания компани и условия задания. Это нормально.

</aside>

### 1. Описание функциональности монолитного приложения

**Управление отоплением:**

- Пользователи могут включать систему отопления
- Пользователи могут выключать систему отопления
- Система поддерживает заданную целевую температуру

**Мониторинг температуры:**

- Пользователи могут получать текущую температуру системы отопления
- Система поддерживает датчики температуры, но пользователь сам с ними никак не взаимодействует

### 2. Анализ архитектуры монолитного приложения

Текущее приложение написано на `Java` (версия 17) с использованием `Spring Boot`.
В частности используется модуль `Spring Data` для организации доступа к данным.
А также модуль `Spring Web` - для реализации `REST` контроллера.

В качестве БД используется `PostgreSQL`, но благодаря использованию ORM в приложении нет зависимости на SQL диалект.
БД можно быстро заменить на любую другую реляционную БД.

Приложение монолитное. Все компоненты реализованы в одном приложении.
Как следствие осложнено масштабирование приложения. 
Невозможно масштабировать какую-то отдельную часть приложения.

Чтобы развернуть приложения требуется остановка и перезапуск.
Это может вызывать недовольство у пользователей.

### 3. Определение доменов и границы контекстов

В текущем решении можно выделить только один домен - `Управление системой отопления`.
Домен условно можно разделить на два контекста:
 - **Контроль работы системы** (вкл./выкл. + установка целевой температуры)
 - **Работа с сенсорами** (при чём пользователи не могут никак влиять на их работу, добавлять или удалять их. Сенсоры используются только для автоматического контроля заданной температуры (Прим.: эта логика не реализована  - см. TO DO в HeatingSystemController.setTargetTemperature))

### **4. Проблемы монолитного решения**

Предлагается взглянуть на проблемы текущего монолитного решения с точки зрения целевой экосистемы и требований к ней.

- Текущее решение не позволяет пользователям самостоятельно обслуживать систему. Есть лишь ограниченный набор функций для управления заранее предустановленным оборудованием.
- Текущее решение ограничено только системой отопления. Другие системы не поддерживаются. Чтобы добавить системы освещения, охраны, удалённого наблюдения и другие, необходимо расширить возможности текущего решения.
- В текущем решении пользователь не может запрограммировать сценарии работы системы. Единственный параметр, который он может задать - целевая температура. Нет возможности добавить конкретные действия системы в ответ на события (например события с датчиков).
- Текущая система не даёт пользователю возможность просмотреть телеметрию и проанализировать исторические данные.
- У текущей системы нет универсальных точек подключения для устройств партнёров. Это сильно ограничивает выбор пользователей. Предпочтения пользователей по части характеристик оборудования могут сильно отличаться и компания не может удовлетворить все их потребности без возможности подключать устройства других производителей. Кому-то нужен определённый цвет или форма оборудования, кому-то важна мощность и наличие редких функций.
- В текущей системе нет понятия оборудования - есть только система отопления целиком. Невозможно создать комплекты оборудования чтобы подстроиться под различные пожелания и финансовые возможности клиентов.
- Текущая система не даёт возможности удалённого конфигурирования и установки оборудования. Система отопления может быть добавлена только сотрудником. Кроме того сотрудник должен выехать к клиенту чтоб всё установить и настроить. Это занимает много времени и затратно для компании.

### 5. Визуализация контекста системы — диаграмма С4

[Context Diagram AS IS](https://cdn-0.plantuml.com/plantuml/png/NP1FRzim3CNl_XISBqs1phxij5E7R407R4EHRDYnK6mc4Y291aaZpRSVvJzTcfj0FVuUttiKTQnZYfK6ZGX_7QSd6WLXIrdnLU5hS2TsgQgK1b220wbIWZumTXeeLzM7aBiuzZXo1AywoEUsPNTkJa7zU30MTuMLjUaejRlGEUxfLy01OxlzzF4vkgn_VlveanD5jj7BujqCvR3g6LaeRxeOZF40zNPwrFQQV-3i2Sn6mADJkHWbrVVhMiWMCKPaqtzVtsNvImPCGwGBuaHOX0BgSIA5V8AP1eVBD9rFCFP-6c_ErfOlRHdkhRd2dlzjnocKy4nHRfZoXddvzgeRBxRsj2ZsYs8PNIr1oCiLSWUJjS2czD-0O1PYkJTkjSEuzcNW-lV8MO1oIyVZWmbr3gcMlp6vgQzg-X-gtk4fc2tVtWxl76y9nNAjUltqSitTO-u5UgSEZanf1O7B_PBkJgotb6nG28-c7rFy1m00)

# Задание 2. Проектирование микросервисной архитектуры

В этом задании вам нужно предоставить только диаграммы в модели C4. Мы не просим вас отдельно описывать получившиеся микросервисы и то, как вы определили взаимодействия между компонентами To-Be системы. Если вы правильно подготовите диаграммы C4, они и так это покажут.

**Диаграмма контейнеров (Containers)**

[Container Diagram TO BE](https://www.plantuml.com/plantuml/png/ZLXVRzj64d_tf-3voKPYw-MUyjJ43fAsiUj4Bdht933IHkPP8dNaoZdtKA2AcwO76SaLk8UYk6kGA-vTLUA4aHFvAv3VwEOttAMfzT1rWSRaYZlpcvdV_Ddz9D5-h8VztjBI7uAmtHjsbBUdzI2vqMZ4_kFrRg3tXW-7YOhRKQXLgDVRKR_n86ZxSIVQ3jH0zHeRVrpRwVcX_dBhNgFl9rh5jDJQeE_z85Jn-e35txlvvo--t6rzzUdktTQzstTkRs-kh2ujwK3tbFUL7_VlHgJ5AtSbtcRWTsE_lxIqL2wsRaN3iEF7XoiTTH2qLT9w67QkUylPpzay-vW_oKwoMJxAdtZPh_YJVqlBx_6KJPTNlRylUVHVyv0Gzbkt_wQD4Ep_DVkGVQIFJxpyoDsPpPVnoI_PRzay_pO_ojvaSrgTq8OqExdXvQFiX9IF-DVnTKd2X1QDXjVPt8C4UfqihpAYqZm3gDLzJ0et2mkzExvMZ_r3hwbYB03BjahqPqtlchSL9NyT1bf75Y5fdH3E2UaVCyQJ_0a0JlCNsRlyc1v5U0Gc1QhJ_8N78jwHGUFygPUTuKFwQ9xDwFzJMEwjtDtTtMbitT_TNQspeD_jQu9aehI6rxNae7qEVhdm-Xbhcq8nGoKWz8UMti2_005e0cPNyUP3gxhp4AB_1y68465Eyt-8WGM6dIZHtLWrxzzZHFzsrOiRoVF0pD-T53IW9MWaLvyPFxqjVrgnyRxkiHT118uGtFUDIEz-lAzRYT-hPpX1dP366GDEIU9hOXcY-9AHme2tuBmJdxuVxoiTXDqMXOFabkzhxNu25zmC3xsDhQONXTvsr57hVqdgGZLZtfmLRgP52bG3bY5FyEsL8rS1nS4JJEE48t9cFsR_4iBsSnLAbY8-9P1yV0KGAr7SKR7n2JzRkZO7CG7pRaMHlZJ9EEupIlCp0fvMz0FxrTp0UeqBVkGad7ZuXmJYPUuorWsC9Lnr4qV1qkwClvcH-bfCtyZbDXaE1b6iQydu4fH3Df2uj-m0NZhDZm4SJiBNhFwzm4eZtlY_FKnqrASGc6KRYYeVQyCmBtv0oXUyH6HgaLGwGWb2Ax_V6WQavQxg3JPLic--OCMlIEO88f3GaEqOBiJL2gwhJ2xcx3sL5qSqg2L4_vUBrgQqcGEV4i8vzjlmulVQYfCS9gqWV1JxjJ6kM8vE1fG_qHVVCG1cKdu4SkL7I3eXoluWA5hRpPrFRLk35Pz7ONUvpFly1mX2viEA_7ks2e-c5usOl6XAbqVMgNjH6EW8gKoAjieNTcbCPDDxQfXSknFxZ_pGPp3_0Rj8-dEkfJ1w3YkvYPvx8X_NUbIhdYB1yM6jH_zhu5BJW_jGkWItaPo4q69EAKNQbDWvr7jHQ8WvpvyMt38LcbGvylDdz3G2JK6xQfD3FNZgClE25tK23bTW09ZVLG2m6_HLqgQMjNbhUO7dXCJuQtuv1XSl-yE5S11ypMMYYk8WI8P-B_ZQrq4KrXNkLvn8Kq2neuvb5CSM2YSLfD0kGwglacqL-d4GrGRydzaKmb4w3C5J833Jf4cgkIrj0CMrb3P_pma-0G9wcaerLCSeN8CuWdFKWGfribOihc5H9OaX_EV-ettVQ_9F2VNApJYAUaLPVLaPzap7TU5C20ulZjc4z_9CkMu7aIdSRWX9re-Bd0L7y-UqPGGLfsPGuBc9ovHWyhfBXTBTTjgqxtRST6dWqj9HWievSGF45OX_UkUCXNBnmHmai9OEJNq_xBGwGJBmTNjFnOJ5B2PhvwjYgoF9ml05a43p9X_NpFH2AueLwQeSBaYzrpHkIxmDdp10vmDynif_WeQ2m49Cl6jC1fCoBueM9A2n2GVMsDymrPaCX5XQ7javrWL-VwY6QcKVjENPYBVFG2o7YoMx3PcHyIU8Mlxi8cy1Ir2_BjJb_0Mjlbeaoxgf0pigJgAmCiDYVZ05sJJ5DrnKPnuYvixiG4gIZ1GxAjJAa2QEgWG-r-AupWuG6NfiDfYnfzqBLAXvuE5muolI-5qnDzJIt3HiScYMykzyV6uWtxFVrjYnwU8vY5GkFL2zwY7NXfG-FqApXONqLDEydaFdPyqljaaI19dTYuKlVuRKDjDWDUAW7xDGgB2Eu0M9zHXTGMLjSFHQwOsR1t5qPUoGm4wraxrS5XteJasquAqmMqA4udUv85iuiZFJHkVOYRGomP_OAUqrw4fFC-wcv-M3NaNqGaJjn2EvHFQt66Uisg76Uj1HUAx9Z3RLFSPHZd0aXC3EoqXatGTpU2VdGwCOQddYE7RhA0gKV6FHLRgHA2rf4RjCeGMo41AXUrXtoUgud1fbalmocvbblzkg8D8MWeLJdtsHeo0Vx6R8Hhp2ROPCcxSsWDOLltWlPOhQ4VENfQSuoV3Ozy4SEfwpIh6rva-vITvkxaggm2H1XrZHZE_n9qNk84TPIshB4avwhAGyFbMLyLdgXjVGGPkw3xTiNCurhOkTwvxorJsBNhZFOUKuA7KZckTN7M6oIyjkQqz9p6sqQjZ7Tk5-n5mjhaYN4gk2lnrVLsvg-D5b3D03GEKM91zRQhWoN2kmWxq_jPEviOPhFaErHKTaXeFLXipnd0bQs-yF_A0RdeVDsLQ1QRRmoiKmsqEHvPuzBXFu7s0C67ZLOvXCEgl10YaFNnUmB3ZKlDI4I7IX4oeBmEb71ePThNLX5OtTLAovMwfN16NVnCaGQAtNuCIFrIQN7p7UhJy9GcmmPf1Xp3m5h-pumDCddpAcFDIbTaApPI0_Fez6rRjNABjrynEkxHiLiysmM9q2mNHNseWXfZLtklQQqJsg5gUXwrxbY9rUWgs2o5eemX4Ge9HmQF0jrD8dLYOBhHXekI59jbVrmDsGZ75P8ycjt2jC6APDGTbqHwHp272l9H7lgrz-mkjYevGLiWgdnRXkaZL9ld7FH6uPvERmhhXV4phGuZKDV_QsvZ9xeGfzeiBEiD_xFm00)

**Диаграмма компонентов (Components)**

Добавьте диаграмму для каждого из выделенных микросервисов.

**Диаграмма кода (Code)**

Добавьте одну диаграмму или несколько.

# Задание 3. Разработка ER-диаграммы

Добавьте сюда ER-диаграмму. Она должна отражать ключевые сущности системы, их атрибуты и тип связей между ними.

Четвёртое задание — дополнительное. Его можно сделать по желанию. Чтобы ревьюер быстрее проверил ваше решение, укажите, сделали вы это задание или нет. Для этого оставьте нужный эмодзи около заголовка задания:

✅ — вы выполнили задание.

❌ — вы пропустили задание.

# ✅ ❌ Задание 4. Создание и документирование API

### 1. Тип API

Укажите, какой тип API вы будете использовать для взаимодействия микросервисов. Объясните своё решение.

### 2. Документация API

Здесь приложите ссылки на документацию API для микросервисов, которые вы спроектировали в первой части проектной работы. Для документирования используйте Swagger/OpenAPI или AsyncAPI.