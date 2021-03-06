# gym_order
## 场地预约
预约逻辑：运行后等待到每天7：00开始预约，预约0-2天(可设置，学校预约系统只开放2天内的场次）内多个指定时间段（**按照固定顺序**）的场地，每次最多同时预约1-3个时间段（系统上每人最多同时3个场）
### 在自己的服务器上运行：
需要一台能联网的服务器，安装好相应的包、修改脚本参数后，自己配置每天定时运行的时间（如通过windows的任务计划程序或linux的crontab），每天7：00前几分钟运行（如6：57）

### 使用Github Actions自动运行：
1. fork到自己的github账号
2. 在你fork的副本中，点击setting->secrets->New secret，配置三个变量，分别是MOBILE（手机号）, STD_ID（学号） 和 PASSWORD（密码），将会用来登录ehall。
  （如：MOBILE：12345678965， STD_ID：21210240289， PASSWORD：12345678）
  （参考[这里](https://github.com/ZiYang-xie/pafd-automated/tree/master/docs)）
3. 默认情况下，脚本会通过.github/workflows/main.yml的cron表达式中配置的时间每天自动运行（规则参考[这里](https://docs.github.com/cn/enterprise-server@2.22/actions/learn-github-actions/events-that-trigger-workflows#scheduled-events) ）例如学校场馆每日早7：00开放预约，需要设置脚本每天6:57运行一次。
   
   但是经过实测，Github Actions的schedule**并不是很准确**，可能会相差数分钟到数小时才会运行脚本，导致预约失败（已经被抢没了）。
   
   因此要想定时启动的话需要更换一下定时启动脚本的方式，通过workflow_dispatch触发器来触发workflow的执行。简单来说，就是通过外面的服务器每天定时post一条链接，Github Actions收到这条请求后就会立刻执行workflow。
   
   这里步骤有点繁琐，需要一台能联网的服务器或者其他第三方cron服务（如腾讯云函数等）设置每天定时post一条链接（形如 https://api.github.com/repos/nqx12348/Badminton/actions/workflows/main.yml/dispatches ）
   
   具体的github和云函数配置过程可以参考[这里](https://blog.csdn.net/l1937gzjlzy/article/details/117753465)
   
   腾讯云函数每天post一条链接是免费的，但是根据[这条通知](https://console.cloud.tencent.com/message/detail/1067391159)该功能三个月后就会收费，到时可以找类似产品替代。
   
   
点击Actions->Run workflow可以立即执行一次预约，通过输出查看脚本是否能够正常运行

修改email地址可以预约成功后发邮件，场地不去的话记得在系统里退掉，否则每次会封掉一个坑位，最多3次就没法约场了（除非去体育馆解除）
