
- [Overview](#overview)
- [Tools](#tools)
  - [JMeter 5.x](#jmeter-5x)
  - [JMeter Selenium/WebDriver 3.x](#jmeter-seleniumwebdriver-3x)
  - [Taurus](#taurus)
- [How To Run Test?](#how-to-run-test)
- [Debugging](#debugging)
- [References](#references)

# Overview

 JMeter, a protocol-level load testing tool is now able to support browser-level load testing by integrating with Selenium WebDriver.

Traditionally, web application load testing is done through protocol level by sending a list of requests at predefined intervals directly to the web server. So, these requests are actually sent without simulating the web  interface,.

In browser-based load testing, real browser instances are opened on test client, and the tool is simulating a real user navigation through the web application using the GUI.

However, flaky tests is common in browser-based test and consume higher CPU and memory footprint than protocol-based test.

Despite the flakiness, it might still be faster to write browser-based test script over protocol-based test script. This would be helpful if you want to quickly run a load test for a web application, and not expecting a very high load.

This is beacuse protocol-level require in-depth knowledge about how an application works, what format it expects requests to be in, and how it responds). Meanwhile, on the browser-level, you don't need to know any of that.

To run browser-based load testing by using JMeter+Selenium Web Driver on remote machines (Selenium Grid).

# Tools

## JMeter 5.x
JMeter is one of the most recommended tool for performance test.

The user's manual can be found here: https://jmeter.apache.org/usermanual/index.html


## JMeter Selenium/WebDriver 3.x

To call selenium from JMeter and collect performance metrics
https://jmeter-plugins.org/wiki/WebDriverTutorial/

This makes our life easy in measuring the load time of dynamic pages, and to gather the performance of dynamic content in addition to their static content.

The source code can be found here:
https://github.com/undera/jmeter-plugins-webdriver

Recommend to install the [Selenium/WebDriver Plugin](https://jmeter-plugins.org/?search=jpgc-webdriver) using [Plugins Manager](https://jmeter-plugins.org/install/Install/) to ensure the compabitiliy with the version of JMeter installed on your local environment.

**NOTE**: To run the test agsinst Chrome browser, You may download the chrome driver from here: https://chromedriver.chromium.org/downloads. Then, specify the location path in Chrome Driver Config.

## Taurus

To make our development life easier, it's better to run the JMeter test using [BlazeMeter Taurus](https://gettaurus.org/).

Taurus is a free and open source framework for Continuous Testing which helps you by hiding the complexities of running performance tests. Think of it as an automation-friendly wrapper - it cloaks nicely around JMeter, neatly covering all of its complexities and imperfections.

For more information, go to [How to Run JMeter Scripts in Taurus](https://gettaurus.org/kb/Basic1/#How-to-Run-JMeter-Scripts-in-Taurus).

A comprehensive documentation can be found in [JMeter Executor](https://gettaurus.org/docs/JMeter/)


# How To Run Test?

Recommend to run test using taurus command-line tool. To know more, refer to [Taurus Command-Line Tool](https://gettaurus.org/docs/CommandLine/)

To run test with default settings:
```
# To run test with default setting:
bzt scripts/bzt/example.yml
```

To run test and generate jmeter html report:
```
bzt scripts/bzt/example.yml scripts/bzt/example_reporting.yml
  ```
Upon completion, the **JMeter HTML Dashboard Report** should be available in "<TAURUS_ARTIFACTS_DIR>/reports" folder.



# Debugging

For debugging purpose, you might want to run the test locally so that you could observe the user interaction in real browser.

1. First, modify the JMeter project to:
   * Disable 'jp@gc - Remote Driver Config'
   * Enable 'jp@gc - Chrome Driver Config'


2. Download the chrome driver from https://chromedriver.chromium.org/downloads

3. In Git Bash, run the test by specifying the location of downloaded chrome driver:
    ```
    bzt scripts/bzt/example.yml -o settings.env.CHROME_DRIVER_LOCATION='C:/webdrivers/chromedriver.exe'
    ```

# References

[Using Selenium with JMeter's WebDriver Sampler](https://www.blazemeter.com/blog/jmeter-webdriver-sampler)

[Web Driver Tutorial](https://jmeter-plugins.org/wiki/WebDriverTutorial/)

[Integration Of Selenium With JMeter](https://www.softwaretestinghelp.com/integrating-jmeter-selenium/)
