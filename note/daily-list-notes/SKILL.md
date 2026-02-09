---
name: daily-list-notes
description: Create a daily checklist page from the advanced to-do template, add tasks, and add notes in the daily list. Use when the user asks to create daily lists, add tasks, update task status/due dates, or add notes in Notion.
---

# Daily List + Tasks + Notes (Notion)

## Scope
- Use this skill for Notion workflows that create a daily list, add tasks, add notes, or archive daily lists.
- Default daily list naming format: `yyyyMMdd`.
- Default template: https://www.notion.so/2ff36d52ad2780d1b746e1865a4b792a

## Create a daily list (yyyyMMdd)
1. Get today's date for the title using `date "+%Y%m%d"` in the shell.
2. Duplicate the template page and rename it to the date string.
3. If the user specifies a parent page, move the new page there; otherwise keep it as-is.
4. Return the new page URL.

## Add a task to a daily list
1. Locate the target daily list page by its title (yyyyMMdd) or URL.
2. Fetch the page to find the inline "待办事项数据库" data source URL.
3. Create a new database row with:
   - `Name`: task title
   - `状态`: set if provided (e.g., "未开始", "进行中", "已完成")
   - `date:截止日期:start` + `date:截止日期:is_datetime`: set if due date provided
4. If the list view is filtered by "待办事项完成" relation, set it to the relation value referenced in the view filter (fetch the database view config to get the required page URL).
5. Return the created task URL.

## Add a note to a daily list
1. Locate the target daily list page by title or URL.
2. Fetch the page to find the inline "Notes" data source URL.
3. Create a new database row with:
   - `名称`: note title
   - `date:日期:start` + `date:日期:is_datetime`: default to current UTC time (use `TZ=UTC date "+%Y-%m-%dT%H:%M:%S"`)
4. If content is provided, write it into the page content; cite sources with `[^URL]`.
5. Return the created note URL.

## Archive daily lists
1. Use the archive List view URL to locate the database: https://www.notion.so/2ff36d52ad27808ba463d6ee7504ccff?v=2ff36d52ad2780b2be8e000c1044904f&source=copy_link
2. Fetch the database to confirm the data source and the `归档日期` (date) property exist.
3. Identify daily list pages to archive by title format `yyyyMMdd` and date rule:
   - If the user says "archive before YYYY-MM-DD", interpret as strictly earlier than that date.
   - Otherwise include today and earlier.
4. Move each matching daily list page into the "Notes & Tasks" database (List view).
5. For each archived page, set:
   - `date:归档日期:start`: current UTC datetime (use `TZ=UTC date "+%Y-%m-%dT%H:%M:%S"`)
   - `date:归档日期:is_datetime`: 1
6. Return a short summary (count + example URLs).

## Conventions
- Always use Notion MCP tools to fetch pages/databases and create/update items.
- Use Notion-flavored Markdown for note content.
- Keep responses concise and confirm created/updated URLs.
