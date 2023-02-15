

​    在android中,Parcel是可以通过IBinder传递数据的容器, 以下数据类型可以直接通过Parcel传递到IPC的对端

1.  原始类型

   Byte / Double / Float / Int / Long / String
   

2. 原始类型的数组

   BooleanArray / ByteArray / CharArray / DoubleArray / FloatArray / IntArray / LongArray / StringArray / SparseBooleanArray
   

3. Parcelable

   writeParcelable  : 首先会写入Parcelable的具体类型,然后才会写入Parcelable的实现内容,适用于对端不知道具体的Parcelable类型(即本身就是动态不固定的),对端在反序列化时候先用readString读出类型,然后反射出其CREATOR,并将parcel剩余数据交给CREATOR的`createFromParcel` ,由该函数的实现自己反序列化对应的Parcelable具体实例
   

   writeTypedObject : 仅仅写入Parcelable的实现内容,数据量更小,适用于对端明确的知道此时反序列化是哪一种Parcelable类型,由于提前知道类型,对端直接使用对应类型的`CREATOR.createFromParcel` 创建具体实例

   

4. Bundle (WIP)

   一种特殊的Parcelable对象

   

5. 活跃对象(active object)

   序列化此类对象时候仅仅写入一个token,对端在反序列化时,通过此token可以得到的是一个可以操作原对象的引用
   

   Binder:  

   ​	使用writeStrongBinder写入,使用readStrongBinder读出,对读出的Binder发送消息可以直接回调到原Binder,更普遍的方法是使用AIDL文件定义一个具体的接口,写入时把对应的Proxy写入,读出时使用aidl生成的Stub类的asInterface方法把对应的IBinder转化为一个Stub对象,然后通过Stub对象调用方法到对端

   

   FileDescriptor:

   ​	使用writeFileDescriptor写入, 使用readFileDescriptor读出,并且为一个ParcelFileDescriptor对象,该对象操作的文件与位置均与原fd相同
   

6. Parcelable Containers

   对于Array / List / ArrayList / Map / SparseArray / 这些泛型容器,如果里面的值是上诉支持的类型的,可以通过对应的API把整个容器写入, 在序列化每一个entry的值的时候,会使用`instanceof` 来判断每一个值的类型,首先写入对应的类型所代表的数值(VAL_STRING / VAL_BUNDLE / VAL_BYTEARRAY etc), 然后判断类型是否





##  API







##  机制

Parcel除了











Parcel

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





