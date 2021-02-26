
# Selenium Grid

Selenium Grid is used to speed up the execution of a test pass by using multiple machines to run tests in parallel.

~ https://www.selenium.dev/documentation/en/grid/


## Install Selenium Grid

* [Install Docker Compose](https://docs.docker.com/compose/install/)

    *Note: Please install 1.28.4 or above in oprder to support compose file version 3.9*
* Start up [Selenium Grid](https://www.selenium.dev/documentation/en/grid/) by running the following command:
  ```
  cd selenium-grid
  docker-compose up -d
  ```
  *Note: Specifying a docker network's gateway (172.50.2.1) so that it is accessible by another docker container.*
* Then, verify the hub is up and running:
  ```
   curl -Is curl -Is  http://localhost:4444/grid/console
   ```
   Or
   ```
   curl -Is curl -Is  http://172.50.2.1:4444/grid/console
  ```
  For example,
    ```
    ubuntu@7MGQQV2:~/selenium-grid$ curl -Is  http://172.50.2.1:4444/grid/console
    HTTP/1.1 200 OK
    Date: Fri, 26 Feb 2021 02:26:54 GMT
    Content-Type: text/html;charset=utf-8
    Content-Length: 9010
    Server: Jetty(9.4.z-SNAPSHOT)
    ```
* In JMeter, [Remote Driver Config](https://jmeter-plugins.org/wiki/RemoteDriverConfig/) enable browser based performance testing using Remote Selenium Grid.
