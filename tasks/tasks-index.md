---
title: Tasks Index
created: 2026-15-04
updated: 2026-15-04
tags: [tasks, index]
---

# Tasks

## Backlog

### Порядок выполнения

> [!info]
> Порядок определён зависимостями: channels до content (FK publications→channels), dispatcher после обоих, notifications последним (все events уже существуют).
> БД не в продакшене — используем `db:push` вместо миграций.