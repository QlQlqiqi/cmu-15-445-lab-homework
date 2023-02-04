#### 记录自己做 cmu 15-445fall2022 lab&homework 的过程

###### 背景&用法

因为新的代码框架和旧版的已经不同，所以我退回到旧版的代码进行后续的工作。

```
git reset --hard d830931a9b2aca66c0589de67b5d7a5fd2c87a79
```

替换相应的文件即可。

>使用 vscode 中 ssh 连接本地 wsl 或者远程服务器进行开发，具体环境配置过程见[博客](https://zhuanlan.zhihu.com/p/592802373)。

[cmu 15-445fall2022](https://15445.courses.cs.cmu.edu/fall2022/schedule.html)

[homework solutions](https://15445.courses.cs.cmu.edu/fall2022/assignments.html)

[autograder](https://www.gradescope.com/)

[bustub frame](https://github.com/cmu-db/bustub)



# lab1

###### 坑：

- task1 Insert 踩了大：
  - c++ 引用返回不能修改原值，这个只是拷贝；（浪费了很久时间）
  - local_depth 过低的时候（global_depth 为 3，local_depth 为 1），这时候对应的 bucket 有 4 个位置（如：1、3、5、7），起初我默认认为 bucket 对应的位置只有两个；同理，split 之后对应的位置也有多个（如：1、5 和 3、7），起初我认为 split 之后只有两个位置；（浪费了很久时间）

- task3 newPgImp 要返回 page id，因为忘记返回 page id，导致一直报内存泄漏错误和 0 地址访问错误，浪费了一整天。

- task3，flushAll 中获取全局读锁，然后调用 flush，flush 中也获得全局读锁，理论上不会出问题，但是在线检测死锁。（虽然全部测试都通过了）

###### 优化点：

- 3 个 task 锁优化基本一致，都是公共类一个读写锁、内部实现一个锁。（task1 是每个 bucket 一个锁，task2 是每个 frame 一个锁，task3 是每个 page 一个锁）



花了很久时间，踩了无数的坑，有的记住了，有的没记住。。。

最后

![image-20230204205940642](https://article.biliimg.com/bfs/article/416b8c7084262d22b1cc26114128fa42193f12f3.png)

我觉得瓶颈应该在 lru-k 的 evict 上，因为这个是 O(n) 的。

###### 这个优化可以用索引优先队列+循环数组来优化，可以将 evcit 算法优化到O(1)，其他操作从O(1)升为O(logn)，但是因为除了 evict 的每个操作都需要对索引优先队列进行操作，所以会导致每个操作之间不能并行处理。
