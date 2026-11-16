# gt-demo

In fact, much of the data is unbounded, because it arrives gradually over time: users produced data yesterday and today, and will continue to produce more tomorrow. As long as your business is still operating, this process will never end, so from any meaningful point of view, the dataset will never be "complete"1. Therefore, batch processing programs have to artificially split the data into fixed time intervals, e.g., processing the day's data at the end of each day, or processing the hour's data at the end of each hour. [guide.md](../../docs/guide.md)

## 🌐 Translations

[English](../../translations/en/README.md)

***

The problem with daily batch processing is that changes in the input are not reflected in the output until a day later, which is far too slow for many impatient users. To reduce latency, you can run the processing more frequently — e.g., processing the data of each second at the end of that second — or abandon fixed time slices altogether and switch to continuous processing, where each event is processed immediately as it occurs. This is the basic idea of stream processing.

In general, a "stream" refers to data that becomes available gradually over time. This concept appears in many places: Unix's stdin and stdout, lazy lists in programming languages2, file system APIs (e.g., Java's FileInputStream), TCP connections, audio and video transmitted over the internet, and so on.