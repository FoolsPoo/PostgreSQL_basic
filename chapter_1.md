# Postgresql Data Masking Tutorial

จะประกอบไปด้วยเนื้อหาพื้นฐานตั้งแต่การ Setup ไปจนถึงการทำการ Masking data ของ Postgresql 

## Course Overview

- [Percona Data Masking Tutorial](#percona-data-masking-tutorial)
    - [Concept](#concept)
    - [Data Masking Technique](#data-masking-technique)
    - [Docker](#docker)
---

## Concept 

Data Masking คือกระบวนการปกปิดข้อมูลของ user ที่เป็นข้อมูลที่ละเอียดอ่อนที่ในบางครั้งผู้ใช้งานอาจไม่ต้องการที่จะเผยแพร่ออกมา

โดยตัวของ database อย่าง postgresql นั้นมีตัว Extension อย่าง Anonymization & Data Masking for PostgreSQL มาใช้ในการทำ Data Masking 

