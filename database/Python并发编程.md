## 并发编程

### 1. 进程、线程

- 多线程：IO 密集型操作使用多线程 eg：IO 操作、输入输出、网络请求

```
import requests
# 使用threading包创建多线程
import threading

def task(video_url):
    pass

url_list = [
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
]

for url in url_list:
    t = threading.Thread(target=task,args=(url,))
    t.start()

    # res = requests.get(url)
    # print res
```

- 多进程： cpu（计算）密集型操作使用多进程 eg：大量复杂的算法计算任务
  Python 内部每个进程中设置了 GIL 锁，进程中同一时刻只能有一个线程可以被 cpu 调度

```
import multiprocessing
import requests

def task(parms):
    res = requests.get(url = parms)
    print(res)

def run():
    url_list = [
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
    ]

    for url in url_list:
    p = multiprocessing.Procress(target=task,args=(url,))
    p.start()


if __name__ == '__main__':
    run()
```

### 2. 池

防止无限制的开进程和线程。

- 线程池

```
from cuncurrent.futures import ThreadPoolExecutor,ProcessPoolExecutor

def tadk(parms):
    print('开始执行任务')
    time.sleep(4)

pool = ThreadPoolExecutor(5)

url_list = [
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
    'http://hello.com.hahah.png',
]

for url in url_list:
    pool.submit(task,url)
```

- 进程池

```
from cuncurrent.futures import ThreadPoolExecutor,ProcessPoolExecutor

def tadk(parms):
    print('开始执行任务')
    time.sleep(4)

def run():
    pool = ProcessPoolExecutor(5)

    url_list = [
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
        'http://hello.com.hahah.png',
    ]

    for url in url_list:
        pool.submit(task,url)

if __name__ == '__main__':
    run()
```

### 3. 数据共享

- 线程

一个进程中可以包含多个线程，多个线程共享同个进程中所有的资源

```
import time
import threading

data_list = []

def task(index):
    data_list.append(index);
    print(data_list)

def run():
    for i in range(0,6):
        t = threading.Thread(target=task,args=(i,))
        t.start()

if __name__ == '__main__':
    run()
```

- 进程

进程与进程之间，数据是隔离的

```
import time
import multiprocessing
from multiprocessing import Manage

def task(index,data_list):
    data_list.append(index);
    print(data_list)


if __name__ == '__main__':
    data = []
    for i in range(0,6):
        t = multiprocessing.Process(target=task,args=(i,data))
        t.start()
        t.join()
    print(data)
```

可以人为的让进程之间进行数据共享，通过 Manage，不过通常我们的做法是通过数据库进行共享

```
import time
import multiprocessing
from multiprocessing import Manage

def task(data_list):
    data_list.append(111);
    data_list.append(333);
    print(data_list)


if __name__ == '__main__':
    manager = Manager()
    with manager:
        data = manager.list()
        t = multiprocessing.Process(target=task,args=(data,))
        t.start()
        t.join()
        print(data)
```

GIL 锁
GIL 作用，保证同一时刻一个进程中只有一个线程可以被 cpu 调度

### 4.携程

应用场景：开发给过程中存在 IO 等待时,当使用携程时，所引用你的模块也必须配合对应的携程模块
https://www.bilibili.com/video/BV1cK4y1E77y?spm_id_from=333.999.0.0
