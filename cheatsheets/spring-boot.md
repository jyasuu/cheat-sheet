


### **Common Spring Boot Actuator Endpoints**

| Endpoint                | Description                                                      |
|-------------------------|------------------------------------------------------------------|
| `/actuator/health`      | Shows application health information (UP/DOWN, details, etc.)    |
| `/actuator/info`        | Displays arbitrary application info (from `application.properties`) |
| `/actuator/metrics`     | Shows metrics like JVM memory, CPU, HTTP requests, etc.          |
| `/actuator/env`         | Exposes environment properties                                   |
| `/actuator/beans`       | Lists all Spring beans in the application context                |
| `/actuator/mappings`    | Shows all @RequestMapping paths                                 |
| `/actuator/threaddump`  | Returns a thread dump                                            |
| `/actuator/loggers`     | Shows and modifies log levels at runtime                         |
| `/actuator/httptrace`   | Displays HTTP request/response traces (last 100 by default)      |
| `/actuator/auditevents` | Shows audit events                                               |
| `/actuator/scheduledtasks` | Shows scheduled tasks                                         |
| `/actuator/heapdump`    | Downloads a JVM heap dump (if enabled)                           |
| `/actuator/shutdown`    | Shuts down the application (must be explicitly enabled)          |
