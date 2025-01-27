# Data Driven with CSV

1. [Start Sck Online Store](#start-sck-online-store)
2. [Create Test Plan](#create-test-plan)

---

## Start `sck-online-store`

1. Clone `sck-online-store` from github to `workspace`

   ```sh
   git clone https://github.com/SCK-SEAL-TEAM-One/sck-online-store.git
   ```

2. Change directory to `sck-online-store`

   ```sh
   cd sck-online-store
   ```

3. Start sck-online-store with docker compose

   ```sh
   docker compose up -d thirdparty point-service db store-service store-web nginx
   ```

---

## Create Test Plan

1. Create Test Plan call `Test_Plan`
   ![test plan](./images/csv/01-test-plan.png)
2. Create Test Group with `Thread Group`
   ![add thread group](./images/csv/02-thread-group.png)
3. Set `CSV Data Set Config`
   ![add csv data set config](./images/csv/03-add-csv-data-set-config.png)
   ![config csv data set config](./images/csv/04-csv-data-set-config.png)
4. Set `Http Request`
   ![add http request](./images/csv/05-add-http-request.png)
   ![http request](./images/csv/06-http-request.png)
5. Add `Response Assertion`
    ![add response assertion](./images/csv/07-add-response-assertion.png)
    ![response assertion](./images/csv/08-response-assertion.png)
6. Add `JSON Assertion`
    ![add json assertion](./images/csv/09-add-json-assertion.png)
    ![json assertion](./images/csv/10-json-assertion.png)

---

## References

1. [jmeter csv dataset config](https://www.blazemeter.com/blog/jmeter-csv-dataset-config)