# SPI Performance differences between ESP-IDF and Arduino

From the r/esp32 subreddit: https://www.reddit.com/r/esp32/comments/o3xmxd/spi_performance_differences_between_espidf_and/

I love the ESP32, and it pains me to have to write posts like this, but the goal here is not to bash the ESP32, but to implore those who work on it to make it better.

This is the first in a series of posts where I will endeavor to demonstrate to any reader that the ESP-IDF's SPI functionality has several problems, and will not work with certain devices.

Since it's several issues, albeit related I'm going to cover it in several posts.

The first is performance. In this post, I endeavor to demonstrate to you that the performance of the ESP-IDF SPI api is not commercial quality. I hate to say that, but there it is.

As I've mentioned I have ongoing performance issues with the ESP-IDF's SPI facilities.

I've tracked part of the problem to the overhead of transactions, including polling transactions. As the size of an individual transaction increases, the performance improves. It still has too much overhead to do 40MHz but it is what it is. spi_master.h and its related facilities are too heavy for the ESP32. I'm confident in that. Probably due to the use of queues where a ring buffer would have done, plus a ton of semaphore use. I think the threading could have been designed better.

I've produced a reproduction in C++ but it's as C like as possible. One project at the repo is ESP-IDF, and the other is Arduino. Both target the ESP-WROVER-KIT with an ILI9341 display.

https://github.com/codewitch-honey-crisis/spi_repro

The code is as identical as humanly possible.

Arduino successfully sends at 40MHz to the display. The best the ESP-IDF can manage is 26MHz\*\* and the proof is in the pudding:

\*\* I've gotten it to 32MHz without crashing but the performance doesn't improve past 26MHz

Arduino version:
```
Average frame write over 50 iterations is 35786 microseconds per frame
Average frame write over 50 iterations is 35785 microseconds per frame
Average frame write over 50 iterations is 35785 microseconds per frame
Average frame write over 50 iterations is 35785 microseconds per frame
```

ESP-IDF version:

```
Average frame write over 50 iterations is 49278 microseconds per frame
Average frame write over 50 iterations is 49278 microseconds per frame
Average frame write over 50 iterations is 49278 microseconds per frame
Average frame write over 50 iterations is 49278 microseconds per frame
```

By itself this is disappointing. But this is not even the beginning of the problems, as I said before, and I will demonstrate it all in a series of future posts.
