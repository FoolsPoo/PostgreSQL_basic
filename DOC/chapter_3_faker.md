# Postgresql Faker
Faker เป็น extension ที่นำมาใช้เพื่อ Masking data แบบนึงซึ่งสามารถสุ่มข้อมูลต่างๆได้เกือบทุกอย่าง ตั้งแต่ชื่อไปจนถึงเลขประจำตัวบัตรประชาชน ซึ่งทำให้การนำไปใช้นั้นสะดวกยิ่งขึ้น 

แต่ในทางกลับกัน Faker นั้นไม่สามรถนำมาใช้เพื่อปกปิดข้อมูลด้วยการ sensor ได้

---

## prepare

ลง Extension ภายใน postgresql database โดยใช้คำสั่ง
```bash
CREATE SCHEMA faker;

CREATE EXTENSION faker SCHEMA faker CASCADE;
```
สามารถใช้คำสั่งด้านล่างเพื่อเช็คว่า ลงเสร็จแล้วหรือยัง

```bash 
SELECT faker.name();
```

---

## command

โดยคำสั่ง faker ที่ provide ได้จะสามารถเช็คได้ที่เว็บ [LINK](https://faker.readthedocs.io/en/master/index.html)

เพราะ extension นี้แทบจะครอบคลุมคำสั่งการสุ่มเกือบทั้งหมด รวมถึงสามารถสุ่ม Thai id ได้เลย

โดยสามารถ setup ภาษาที่ต้องการสุ่มได้โดยใช้คำสั่ง

```bash
select faker.faker('th_TH')
```

อย่างในตัวอย่าง th_TH คือข้อมูลไทยในภาษาไทย หากเราต้องการให้เป็นข้อมูลไทยในภาษาอังกฤษ สามารถพิมว่า en_TH เพื่อเปลี่ยนไปใช้ภาษาอังกฤษแทน

---

## thai id

สามารถใช้คำสั่ง

```bash
select faker.vat_id();
```

ในการสุ่มเลขประจำตัวบัตรประชาชน โดยถูกหลักการได้ 


