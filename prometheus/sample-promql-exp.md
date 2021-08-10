Sample PromQL expressions:

1. Check if node memory is less than 20% or not. 
node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 20

2. Graph the disk read rate.
rate(node_disk_read_time_seconds_total[1m]) / rate(node_disk_reads_completed_total[1m])

3. Based on the past 2 hours of data, find out how much disk will fill in next 6 hours. 
predict_linear(node_filesystem_free_bytes{fstype!~"tmpfs"}[2h], 6 * 3600)

4. Find out the CPU usage percentage.  
100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[10m])) * 100)
