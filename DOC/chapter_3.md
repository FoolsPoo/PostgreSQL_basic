# Postgresql Faker
Faker เป็น extension ที่นำมาใช้เพื่อ Masking data แบบนึงซึ่งสามารถสุ่มข้อมูลต่างๆได้เกือบทุกอย่าง ตั้งแต่ชื่อไปจนถึงเลขประจำตัวบัตรประชาชน ซึ่งทำให้การนำไปใช้นั้นสะดวกยิ่งขึ้น 

แต่ในทางกลับกัน Faker นั้นไม่สามรถนำมาใช้เพื่อปกปิดข้อมูลด้วยการ sensor ได้


## prepare
ลง Extension ภายใน postgresql database โดยใช้คำสั่ง
```bash
CREATE SCHEMA faker;

CREATE EXTENSION faker SCHEMA faker CASCADE;
```
เราสามารถใช้คำสั่งด้านล่างเพื่อเช็คว่า เราลงเสร็จแล้วหรือยัง

```bash 
SELECT faker.name();
```

โดยคำสั่ง faker ที่ provide ได้จะสามารถเช็คได้ที่เว็บ [LINK](https://faker.readthedocs.io/en/master/index.html)

เพราะ extension นี้แทบจะครอบคลุมคำสั่งการสุ่มเกือบทั้งหมด รวมถึงสามารถสุ่ม Thai id ได้เลย