
```bash
JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75 -XX:InitialRAMPercentage=50 -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/oom.prof -Xlog:gc*:file=/tmp/gc.log:time,uptime,level,tags"
```
