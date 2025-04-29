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

   ```groovy
   import groovy.json.JsonSlurper

   def cart = vars.get("cart")
   def jsonSlurper = new JsonSlurper()
   def object = jsonSlurper.parseText(cart)

   def orderSize = object.cart.size
   //log.info("cart size: " + cartSize)

   vars.putObject("orderSize", orderSize)
   vars.putObject("orderList", object.cart)
   ```

   > Extract ค่า `cart` จาก `csv` โดยหาขนาดของ order และ orderList

5. Loop Control

   ![loop control](./images/jsr223-groovy/04-loop-control.png)

   > ตั้งค่า `Loop Count` เป็น `${orderSize}`

6. Extract Order Item

   ![extract order item](./images/jsr223-groovy/05-extract-item.png)

   ```groovy
   import groovy.json.JsonOutput

   def idx = vars.get("__jm__LC__idx")
   def cart = vars.getObject("orderList")
   def item = cart.get(idx.toInteger())
   def json = JsonOutput.toJson(item)

   vars.putObject("itemObject", json)
   ```

   > ตั้งค่า `itemObject` จากตำแหน่ง `idx` ของ `orderList`

7. Summit Order

   ![submit order](./images/jsr223-groovy/06-submit-order.png)

   ```groovy
   {
        "cart": [
            ${itemObject}
        ],
        "shipping_method": "Kerry",
        "shipping_address": "405/37 ถ.มหิดล",
        "shipping_sub_district": "ท่าศาลา",
        "shipping_district": "เมือง",
        "shipping_province": "เชียงใหม่",
        "shipping_zip_code": "50000",
        "recipient_name": "ณัฐญา ชุติบุตร",
        "recipient_phone_number": "0970809292"
   }
   ```

   > แทนที่ `"cart" : [ .... ]` ด้วย `${itemObject}`

8. Results Tree

   ![results tree](./images/jsr223-groovy/07-results-tree.png)

   > เกิดการทำ `Submit Order` จำนวน 3 ครั้ง

9. Overview

   ![overview](./images/jsr223-groovy/jsr223-and-csv.png)
