
```bash
JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75 -XX:InitialRAMPercentage=50 -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/oom.prof -Xlog:gc*:file=/tmp/gc.log:time,uptime,level,tags"
```

## dump_jstack_by_filter.sh
```bash
#!/bin/bash

FILTER=$1                 # e.g. my-app.jar or unique JVM arg
COUNT=${2:-10}            # number of dumps (default 10)
INTERVAL=${3:-5}          # seconds between dumps (default 5)

if [ -z "$FILTER" ]; then
  echo "Usage: $0 <filter> [count] [interval_seconds]"
  echo "Example: $0 my-app.jar 15 3"
  exit 1
fi

# Find PID (avoid matching grep itself)
PID=$(ps aux | grep "$FILTER" | grep -v grep | awk '{print $2}' | head -n 1)
#PID=$(ps aux | grep "$FILTER" | grep -v grep | awk '{print $2}')

if [ -z "$PID" ]; then
  echo "❌ No process found matching filter: $FILTER"
  exit 1
fi

OUT_DIR=jstack_${PID}_$(date +%Y%m%d_%H%M%S)
mkdir -p "$OUT_DIR"

echo "✅ Found PID: $PID"
echo "Filter   : $FILTER"
echo "Count    : $COUNT"
echo "Interval : ${INTERVAL}s"
echo "Output   : $OUT_DIR"
echo

for i in $(seq 1 $COUNT); do
  TS=$(date +%Y%m%d_%H%M%S)
  echo "[$i/$COUNT] Dumping jstack at $TS"
  jstack -l "$PID" > "$OUT_DIR/jstack_${PID}_${TS}.txt"
  sleep "$INTERVAL"
done

echo
echo "✅ jstack capture completed."
```
