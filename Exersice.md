# PromQL

```json
# HELP python_gc_objects_collected_total Objects collected during gc
# TYPE python_gc_objects_collected_total counter
python_gc_objects_collected_total{generation="0"} 397.0
python_gc_objects_collected_total{generation="1"} 43.0
python_gc_objects_collected_total{generation="2"} 0.0
# HELP python_gc_objects_uncollectable_total Uncollectable object found during GC
# TYPE python_gc_objects_uncollectable_total counter
python_gc_objects_uncollectable_total{generation="0"} 0.0
python_gc_objects_uncollectable_total{generation="1"} 0.0
python_gc_objects_uncollectable_total{generation="2"} 0.0
# HELP python_gc_collections_total Number of times this generation was collected
# TYPE python_gc_collections_total counter
python_gc_collections_total{generation="0"} 170556.0
python_gc_collections_total{generation="1"} 15505.0
python_gc_collections_total{generation="2"} 1409.0
# HELP python_info Python platform information
# TYPE python_info gauge
python_info{implementation="CPython",major="3",minor="9",patchlevel="16",version="3.9.16"} 1.0
# HELP process_virtual_memory_bytes Virtual memory size in bytes.
# TYPE process_virtual_memory_bytes gauge
process_virtual_memory_bytes 2.16006656e+08
# HELP process_resident_memory_bytes Resident memory size in bytes.
# TYPE process_resident_memory_bytes gauge
process_resident_memory_bytes 5.9359232e+07
# HELP process_start_time_seconds Start time of the process since unix epoch in seconds.
# TYPE process_start_time_seconds gauge
process_start_time_seconds 1.68494724919e+09
# HELP process_cpu_seconds_total Total user and system CPU time spent in seconds.
# TYPE process_cpu_seconds_total counter
process_cpu_seconds_total 1348.04
# HELP process_open_fds Number of open file descriptors.
# TYPE process_open_fds gauge
process_open_fds 7.0
# HELP process_max_fds Maximum number of open file descriptors.
# TYPE process_max_fds gauge
process_max_fds 1.048576e+06
# HELP request_processing_seconds Time spent processing request
# TYPE request_processing_seconds summary
request_processing_seconds_count 140.0
request_processing_seconds_sum 35.750807531001556
# HELP request_processing_seconds_created Time spent processing request
# TYPE request_processing_seconds_created gauge
request_processing_seconds_created 1.6849472507491512e+09
# HELP events_sent_total Total number of events sent
# TYPE events_sent_total counter
events_sent_total 78.0
# HELP events_sent_created Total number of events sent
# TYPE events_sent_created gauge
events_sent_created 1.6849472507492208e+09


```

## Description
  1. Connect to Prometheus dashboard: URL
  2. connect to the service /metrics
  3. find what metrics the service is exporting
  4. describe each metric based on type and help
  5. write queries for:
      ## 1:
      1. Total Number of Events Sent
      2. Request Processing Time
      3. CPU Usage
      4. Memory Usage virtual and resident
      5. Python Garbage Collection

      ## 2:

      6. Percentage of CPU Usage
      7. Request Processing Time rate each 5m
      8. CPU Usage rate each 5m
      9.  Rate of Events Sent rate each 5m
      10. Average Number of Objects Collected by Python
      11. Ratio of Uncollectable Objects
      12. Memory Usage Ratio
      13. Rate of File Descriptor Usage

## answers

# 1-2:
the service name: "event-scraper-py", pod: "event-scraper-py-f68cccc4d-x8kjl", namespace: "scraper", instance="10.244.0.12:8000", endpoint: "event-scraper-py", job: "event-scraper-py", container: "event-scraper-py".
# 3:
# python_gc_objects_collected_total:
this metric quantifies the count of objects that are collected during the process of garbage collection, his type is count.
generational garbage collection in Python involves categorizing objects into three generations: "young" (generation 0), "intermediate" (generation 1), and "old" (generation 2).

# python_gc_objects_uncollectable_total:
this metric quantifies the number of uncollectable objects encountered during the process of garbage collection, his type is count.

# python_gc_collections_total:
this metric represents the count of how many times each generation was collected during the garbage collection process, his type is count.

# python_info:
the python_info metric offers details about the Python platform, including specific aspects and corresponding values, his type is gauge.

# process_virtual_memory_bytes:
this metric quantifies the size of virtual memory in bytes, which is : 291,508,224 bytes, his type is gauge.

# process_resident_memory_bytes:
this metric quantifies the size of resident memory in bytes, representing the portion of the process memory currently held in RAM, his type is gauge. 

# process_start_time_seconds:
this metric represents the start time of the process since the Unix epoch, measured in seconds, his type is gauge.

# process_cpu_seconds_total:
this metric represents the total CPU time spent by the process, including both user and system CPU time. It is measured in seconds, his type is counter.

# process_open_fds:
this metric indicates the count of open file descriptors associated with the process, providing insights into resource usage and limitations, his type is gauge.

# process_max_fds:
this metric represents the maximum limit for the number of open file descriptors that can be associated with the process, his type is gauge.

# request_processing_seconds:
this metric captures the processing time of requests. The count of processed requests is provided by request_processing_seconds_count, while request_processing_seconds_sum gives the sum of processing times for all requests. These metrics are valuable for analyzing the performance and efficiency of the request processing system.
his type is summary, and when you use this type you can use the tools: sum and count.

# request_processing_seconds_created:
this metric represents the time it take to process request in seconds, his type is gauge.

# events_sent_total:
this metric is designed to track the total number of events that have been sent from the python code, his type is counter.

# events_sent_created
this metric is designed to track the number of the total events that create and been sent the from the python code, his type is gauge.

## 1 

# Total Number of Events Sent:
events_sent_total

# Request Processing Time:
rate(request_processing_seconds_sum) / rate(request_processing_seconds_count)

# CPU Usage
rate(process_cpu_seconds_total[5m])

# Memory Usage virtual and resident
process_virtual_memory_bytes
process_resident_memory_bytes 

# Python Garbage Collection
python_gc_objects_collected_total{generation="depends on how old you want it to be"}

## 2  

# Percentage of CPU Usage(for 5m)
rate(process_resident_memory_bytes[5m])*100 

# Request Processing Time rate each 5m
rate(request_processing_seconds_sum[5m]) / rate(request_processing_seconds_count[5m])

# CPU Usage rate each 5m
rate(process_cpu_seconds_total[5m])

# Rate of Events Sent rate each 5m
rate(events_sent_total[5m])

# Average Number of Objects Collected by Python
rate(python_gc_objects_collected_total[5m]) / rate(python_gc_collections_total[5m])

# Ratio of Uncollectable Objects
rate(python_gc_objects_uncollectable_total[5m]) / rate(python_gc_objects_collected_total[5m])

# Memory Usage Ratio
process_resident_memory_bytes /  process_virtual_memory_bytes

# Rate of File Descriptor Usage
rate(process_open_fds[5m])
