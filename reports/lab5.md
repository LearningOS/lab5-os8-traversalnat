1. 需要回收的资源包括 线程的 tid、内核页表, 进程页表，进程文件描述符表.
    其他线程的 TaskControlBlock 会在 等待队列、进程 tasks 中引用，无需回收，因为TaskControlBlock 被包裹在 Arc 中，线程退出并且线程对应的 process 退出, Arc 引用计数降至0, 对应 TaskControlBlock 自动销毁。

2. Mutex1 无论 是否有等待线程，都会设置 locked 为 false, Mutex2 只有在没有等待线程时，才设置  locked 为 false。Mutex1 可能会导致其他线程直接 lock 成功，导致锁同时被两个线程持有，导致锁失效，并且持有锁的线程在执行unlock 时 `assert!(mutex_inner.locked)` 失败.
