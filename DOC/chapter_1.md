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

โดยตัวของ database อย่าง postgresql นั้นมีตัว Extension อย่าง Anonymization & Data Masking และ Postgresql faker มาใช้ในการทำ Data Masking โดยแต่ละเครื่องมือมีการใช้งานที่แตกต่างกัน

โดยที่ความสามารถในการทำ Data masking นั้นใกล้เคียงกันกับ Percona

โดย Anonymization จะครอบคลุมทั้ง

1. สร้างข้อมูลแบบใหม่โดยการสุ่ม เช่น สุม อีเมล
2. ปกปิดข้อมูลด้วยการ ซ่อนข้อมูล เช่น mask ข้อมูล เบอร์มือถือ 08x-xxx-xxxx

แต่ตัวของ Faker นั้นสามารถทำการสุ่่่มได้เท่านั้น หรือทำได้เพียงแค่ข้อที่ 1 

## Docker 

ติดตั้ง Docker image

```bash
docker pull registry.gitlab.com/dalibo/postgresql_anonymizer:latest
```

ติดตั้ง Docker container ของ postgresql

```bash
docker run -d -e POSTGRES_PASSWORD=x -p 6543:5432 registry.gitlab.com/dalibo/postgresql_anonymizer
```

run container 
```bash
docker exec -it container-id bash
```

login เข้า database 
```bash 
    psql --host=localhost --port=5432 --user=postgres

```
or
```bash
psql -h localhost -p 5432 -U postgres
```
