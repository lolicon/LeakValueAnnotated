

​	LeakValue是 [Michal Bednarski](https://github.com/michalbednarski/) 在2023年初, 组合利用AOSP中的LazyValue机制存在的两个漏洞,最终成功实现获取本应仅存在于SystemServer进程中的任意App的IApplicationThread对应的Binder, 通过对其发送aidl调用, 实现所有App的任意代码执行(ACE)的利用手法

​    Michal在公开所有技术细节的同时,给出了一个非常详尽的WriteUp和[poc](https://github.com/michalbednarski/LeakValue),非常值得一看,同时由于信息量过大,牵扯代码过多,以及Reparcel漏洞及其缓解机制引入过程的上下文读者不一定清楚导致阅读有一定门槛, 本文将分为一个系列,详尽的介绍LeakValue相关的背景知识以及利用原理

​     本系列将按照以下章节规划,目标是在每一个章节中引入的内容能够独立自洽,最终在理解LeakValue漏洞的同时,引入更多WriteUp中没有涉及或一笔带过的部分, 


### Parcel

- API
  - writeParcelable / readParcelable
    - CREATOR
  - writeStrongBinder
  - appendFrom
  - setClassCookie
  - marshal
  - 泛型处理

- 机制
  - ReadWriteHelper
  - Squashing
  - Defuse(Bundle)
  - Class Cookie
    

### Parcelable 以及一些特殊Parcelable实现

- BaseParceledListSlice(反向阻塞)

  - StringParceledListSlice
  - ParceledListSlice

- Notification & PendingIntent  (设置Class Cookie)

- Bundle (本身拷贝Parcel / Lazy)

- RemoteViews (Squash & ReadWriteHelper)

- ApplicationInfo 

- ReflectionAction

- ParsingPackageImpl (ReadWriteHelper)

  

### Reparcel 漏洞 以及 Android 13的缓解机制

- Reparcel 分类
  - 指令不对称
  - 控制流不对称
- 缓解机制
  - TypeSafe API
  - Parcel.enforceNoDataAvail
  - LazyValue
    

### ActivityThread 与  Activity(Task)?ManagerService 之间的交互

- ActivityThread
  - attach 
    - IActivityManager.attachApplication(mAppThread, startSeq)
- ApplicationThread
  - scheduleReceiver(Intent, ActivityInfo) 
    - 实例化Receiver的过程
- ActivityManagerService
  - appThead.scheduleReceiver(Intent, ActivityInfo, ...)
  - appThread.schedule...
  - mGlobalLock
  - mProcLock
    

### LeakValue

- negative position
- reference after recycle
