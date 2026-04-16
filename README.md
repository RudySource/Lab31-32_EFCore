<div align="center">

  <h3>RUD3US</h3>

<img
    src="https://avatars.githubusercontent.com/u/80245370?v=4"
    alt="Rudy Icon"
    width="180"
    style="border-radius: 50%; box-shadow: 0 8px 24px rgba(0,0,0,0.18); border: 1px solid #e5e7eb;"
  />

  <p><sub>Used Services:</sub></p>

</div>

<p align="center">
  <a href="https://github.com/RudySource?tab=repositories">
    <img src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white"/>
  </a>
  <a href="https://dotnet.microsoft.com/">
    <img src="https://img.shields.io/badge/.NET-5C2D91.svg?style=for-the-badge&logo=.net&logoColor=white"/>
  </a>
  <a href="https://www.sqlite.org/">
    <img src="https://img.shields.io/badge/SQLite-003B57.svg?style=for-the-badge&logo=sqlite&logoColor=white"/>
  </a>
  <a href="https://learn.microsoft.com/dotnet/csharp/">
    <img src="https://img.shields.io/badge/C%23-239120.svg?style=for-the-badge&logo=c-sharp&logoColor=white"/>
  </a>
</p>

<div align="center" style="max-width: 600px; margin: 0 auto; padding: 18px; border-radius: 18px; background: rgba(255,255,255,0.06); box-shadow: 0 14px 30px rgba(0,0,0,0.08);">

## Основная информация

**ФИО**: Rudy Rudy Rudy

**Группа**: ИСП-233

**Дата**: 24.08.2077

</div>

# Лабораторная работа №31-32. Введение в SQLite и Entity Framework Core

## Краткое описание работы

Сделал - подключение базы данных `SQLite` - работа через ORM (EF
Core) - подход Code First - миграции базы данных - CRUD операции -
`LINQ`-запросы (поиск, статистика, пагинация)

---

## Полезные команды dotnet ef

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
dotnet ef migrations list
dotnet ef migrations remove
dotnet ef migrations script
```

> [!TIP]
> Всегда проверяйте сборку проекта перед созданием миграции. `dotnet build` & `dotnet run` & `dotnet restore` - Ваши друзья

---

## Структура проекта

    TaskDb/
    ├── Controllers/
    │   └── TasksController.cs
    ├── Data/
    │   └── AppDbContext.cs
    ├── Models/
    │   ├── TaskItem.cs
    │   └── TaskDtos.cs
    ├── Migrations/
    │   ├── Куча файлов
    ├── Program.cs
    ├── appsettings.json
    └── taskdb.db

---

## Список реализованных маршрутов

- GET /api/tasks
- GET /api/tasks/{id}
- POST /api/tasks
- PUT /api/tasks/{id}
- PATCH /api/tasks/{id}/complete
- DELETE /api/tasks/{id}
- GET /api/tasks/search
- GET /api/tasks/stats
- GET /api/tasks/paged
- GET /api/tasks/overdue
- GET /api/tasks/complete_all
- GET /api/tasks/completed

---

## Таблица примененных миграций

| №   | Название миграции  | Описание изменений                                          |
| --- | ------------------ | ----------------------------------------------------------- |
| 1   | `InitialCreate`    | Создание таблицы Tasks с начальными данными (3 задачи)      |
| 2   | `AddDueDateToTask` | Добавление поля `DueDate` (срок выполнения) в таблицу Tasks |

---

## Сравнение LINQ и SQL

| LINQ <img src="https://github.com/devicons/devicon/blob/master/icons/csharp/csharp-original.svg" title="C#" alt="C#" width="24" height="24" /> | SQL <img src="https://github.com/devicons/devicon/blob/master/icons/sqlite/sqlite-original.svg" title="SQLite" alt="SQLite" width="24" height="24" /> |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `db.Tasks.ToList()`                                                                                                                            | `SELECT * FROM Tasks`                                                                                                                                 |
| `db.Tasks.Find(5)`                                                                                                                             | `SELECT * FROM Tasks WHERE Id = 5`                                                                                                                    |
| `.Where(t => t.IsCompleted == false)`                                                                                                          | `WHERE IsCompleted = 0`                                                                                                                               |
| `.Where(t => t.Title.Contains("SQL"))`                                                                                                         | `WHERE Title LIKE '%SQL%'`                                                                                                                            |
| `.OrderByDescending(t => t.CreatedAt)`                                                                                                         | `ORDER BY CreatedAt DESC`                                                                                                                             |
| `.Take(10)`                                                                                                                                    | `LIMIT 10`                                                                                                                                            |
| `.Skip(20).Take(10)`                                                                                                                           | `OFFSET 20 LIMIT 10`                                                                                                                                  |
| `.Count()`                                                                                                                                     | `SELECT COUNT(*)`                                                                                                                                     |
| `.Count(t => t.IsCompleted)`                                                                                                                   | `SELECT COUNT(*) WHERE IsCompleted = 1`                                                                                                               |
| `.GroupBy(t => t.Priority)`                                                                                                                    | `GROUP BY Priority`                                                                                                                                   |
| `.Select(t => t.Title)`                                                                                                                        | `SELECT Title`                                                                                                                                        |
| `.Any(t => t.Priority == "High")`                                                                                                              | `EXISTS (SELECT 1 WHERE Priority = 'High')`                                                                                                           |

---

## Итоговая сравнительная таблица

| Концепция              | Хранение в памяти (`static List<T>`) | EF Core + SQLite                             |
| ---------------------- | ------------------------------------ | -------------------------------------------- |
| **Хранение данных**    | `static List<T>` в RAM               | Файл `.db` на диске                          |
| **После перезапуска**  | Данные пропадают                     | Данные сохраняются                           |
| **Поиск по условию**   | `LINQ` to Objects                    | `LINQ` to Entities → SQL                     |
| **Создание структуры** | Не нужно                             | Миграции (`dotnet ef`)                       |
| **Начальные данные**   | Хардкод в коде                       | `HasData()` в миграции                       |
| **Получение данных**   | `list.FirstOrDefault(...)`           | `await db.Table.FindAsync(id)`               |
| **Добавление**         | `list.Add(item)`                     | `db.Table.Add(item) + SaveChangesAsync()`    |
| **Удаление**           | `list.Remove(item)`                  | `db.Table.Remove(item) + SaveChangesAsync()` |
| **Масштабируемость**   | Ограничена RAM                       | Гигабайты данных                             |
| **Транзакции**         | Нет                                  | Встроены в EF Core                           |

---

## Главные выводы

1. **EF Core — это переводчик между C# и SQL.** Вы пишете `LINQ`-запросы на C#, а EF Core автоматически преобразует их в `SQL`-запросы для базы данных. Это избавляет от необходимости писать `SQL` вручную и снижает риск ошибок.

2. **Миграции — это система контроля версий для структуры БД.** Так же, как `Git` отслеживает изменения в коде, миграции отслеживают изменения в структуре базы данных. Это позволяет команде разработчиков синхронно обновлять БД и легко откатывать изменения.

3. **Code First подход удобнее, чем ручной SQL.** Вместо того чтобы писать `CREATE TABLE` и `ALTER TABLE` вручную, вы просто изменяете C# классы и создаёте миграцию. EF Core сам сгенерирует необходимый `SQL`-код.

4. **`SaveChangesAsync()` — ключевой момент.** До вызова этого метода все изменения (добавление, обновление, удаление) существуют только в памяти. Только после `SaveChangesAsync()` EF Core отправляет `SQL`-запросы в базу данных и фиксирует изменения.

5. **`async/await` при работе с БД — это обязательный стандарт.** Запросы к базе данных занимают время, и блокировать поток сервера на время ожидания — плохая практика. Асинхронные методы позволяют серверу обрабатывать другие запросы, пока один ждёт ответа от БД.

6. **LINQ предоставляет мощные возможности для работы с данными.** Методы `Where`, `OrderBy`, `GroupBy`, `Skip/Take` позволяют гибко фильтровать, сортировать и пагинировать данные, при этом EF Core преобразует их в эффективные `SQL`-запросы.

7. **SQLite идеально подходит для обучения и небольших проектов.** Не требует установки сервера, вся база хранится в одном файле, использует стандартный `SQL`. Это делает её отличным выбором для разработки и тестирования.
