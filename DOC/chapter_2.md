# Prepare

การใช้งาน Tools database ที่แตกต่างกันนั้น บางคำสั่งก็มีวิธีใช้งานที่แตกต่างเหมือนกัน postgresql มีคำสั่งพื้นฐานที่ใช้ในการทำงานที่แตกต่างกับ Tools 

## Course Overview

- [Percona Data Masking Tutorial](#percona-data-masking-tutorial)
    - [Concept](#concept)
    - [Data Masking Technique](#data-masking-technique)
    - [Docker](#docker)
---

## Basic Command

List All Databases: “\l”.

Access or Switch a Database: “\c db_name”.

List All Tables: “\dt”.

Describe All Tables: “\d”.

Describe a Specific Table: “\d tab_name”.

List All Schemas: “\dn”.

List All Views: “\dv”.

List All Functions: “\df”.

List All Users: “\du”.

Quit psql: “\q” or "Ctrl+Z".

หรือคุณสามารถดูคำสั่งเพิ่มเติมได้โดยใช้คำสั่ง "\?" ซึ่งคำสั่งนี้จะแสดงคำสั่งที่เราสามารถใช้ได้ใน psql 

โดยหลักๆที่ได้ใช้จะมีดังนี้

reference:https://www.commandprompt.com/education/postgresql-basic-psql-commands/

---

