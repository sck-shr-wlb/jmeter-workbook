# CSV, JSR 223, and Loop Control

1. CSV file

   ```csv
   id,cart
   1|{"cart":[{"product_id": 1,"quantity": 2}]}
   2|{"cart":[{"product_id": 2,"quantity": 1},{"product_id": 1,"quantity": 2}]}
   ```

   > แบ่งข้อมูลด้วย `|` เนื่องจากติดข้อจำกัดในการที่จะ `Parse` ข้อมูลไปเป็นแบบ `JSON`

2. Tread Group

   ![thread group](./images/jsr223-groovy/01-thread-group.png)

   > ตั้งค่าจำนวนการทดสอบจาก `Number of Threads` และ `Loop Count` ให้เท่ากับจำนวนข้อมูลจาก `CSV file`

3. CSV Config

   ![csv config](./images/jsr223-groovy/02-csv.png)

   > ตั้งค่า `Delimeter` เป็น `|`

4. Extract CSV

   ![extract csv](./images/jsr223-groovy/03-extract-csv.png)

   > Extract ค่า `cart` จาก `csv` โดยหาขนาดของ order และ orderList

5. Loop Control

   ![loop control](./images/jsr223-groovy/04-loop-control.png)

   > ตั้งค่า `Loop Count` เป็น `${orderSize}`

6. Extract Order Item

   ![extract order item](./images/jsr223-groovy/05-extract-item.png)

   > ตั้งค่า `itemObject` จากตำแหน่ง `idx` ของ `orderList`

7. Summit Order

   ![submit order](./images/jsr223-groovy/06-submit-order.png)

   > แทนที่ `"cart" : [ .... ]` ด้วย `${itemObject}`

8. Results Tree

   ![results tree](./images/jsr223-groovy/07-results-tree.png)

   > เกิดการทำ `Submit Order` จำนวน 3 ครั้ง

9. Overview

   ![overview](./images/jsr223-groovy/jsr223-and-csv.png)
