# android代码review

标签（空格分隔）： findbugs

---

###1.错误的命名
```java
    public class RequestException extends Throwable 
```
以为是一个Exception，其实这个是个Throwable对象


----------


###2.枚举中的存在public域
```java
    enum A{
        public int id;
        public String name;
    }
```
枚举中存在了可变的变量，这样用枚举还有什么意义吗？


----------
###3.文件对象的操作状态
```java
    //mkdir
    File file=new File("a.txt");
    if(!file.exists){
        file.mkdirs();
    }
```
```java
    //delete
    File file=new File("a.txt");
    if(file.exists){
        file.delete();
    }
```
涉及到文件的删除和创建，这种最好去对操作状态进行判断，有可能会有异常。


----------
###4.对象的赋值操作
错误的写法：
```java
    public class A{
        private String[] strs;
        public void setStrs(String[] strs){
            this.strs=strs;
        }
        
        public String[] getStrs(){
            return this.strs;
        }
    }
```
测试：
```java
    class TestA{
        static void main(String[] args){
            String[] strs=new String[]{"hello,world!"};
            A a = new A();
            a.setStrs(strs);
            System.out.print(a.getStrs());
            //这里改变原始数据
            strs={"change"};
            System.out.print(a.getStrs());
        }
    }
```
上面的setStrs(String[] strs)方法可以通过传入一个String类型的数组来改变A的strs成员值，然后通过getStrs()可以在外部获取；这时，如果修改原始的strs，发现第二次打印出来的值也跟着变了。
修改一个对象，可能会引起其它对象的修改，在java中，对象是引用传递的。所以这里的建议是：setStrs的时候，对象不要直接赋值，通过传入对象的拷贝。

正确的写法：
```java
    public class A{
        private String[] strs;
        public void setStrs(String[] strs){
            this.strs=(String[])strs.clone();
        }
        
        public String[] getStrs(){
            return this.strs;
        }
    }
```


----------
###5.输入输出流的关闭
```java
    File f=new File("a.txt");
    try{
        FileOutputStream out=new FileOutputStream(f);
    }catch(Exception e){
        
    }
    
```
上面创建完文件输出流之后，最后并没有关闭，应该在finally中关闭。
```java
    finally{
        out.close();
    }
```


----------
###6.“＋”的使用
```java
    while((line=bufReader.readLine()) != null){
        res+=line;
    }
```
上面的while循环中使用“＋”来拼接字符串，“＋”每次都会在内存中开辟一块新的内存区域，拼接完之后存在这块新的内存，如果循环次数越多，那么浪费的时间和内存空间就不可而知，性能上大大地浪费了。
正确的做法是：使用StringBuffer或者StringBuilder代替“＋”，每次在已有的内存空间追加字符串，如果空间不足的话，会开辟2(size+1)个字节长度的空间。这两个的区别在于：StringBuffer是线程安全的，StringBuilder不是线程安全的。


----------
###7.Handler的使用(内存泄漏)
```java
    public class MyActivity extends Activity{
        private Handler mHandler = new Handler{
            @Override
            public void handleMessage(Message msg){
                
            }
        }
    }
```
上述的写法会存在内存泄漏，原因是：
#### Handler的生命周期与Activity不一致
*  当android应用启动的时候，会先创建一个UI主线程的Looper对象，Looper实现了一个简单的消息队列，用来处理Message对象。主线程中的Looper对象一直在整个应用的生命周期中存在。
*  当在主线程中初始化Handler时，这个Handler对象和Looper的消息队列关联。发送到消息队列的Message对象会引用该Handler对象，系统可以通过Handler的handleMessage(Message msg)来分发处理Message。
####handler引用Activity阻止了GC对Activity的回收
* 首先，在java中，非静态匿名内部类隐性持有外部类的引用，而静态内部类不会引用外部类对象。
* 所以外部类是Activity时，会引起Activity泄漏
当Activity调用finish()后，消息暂时存在于主线程的消息队列中。但是，消息引用了Activity的Handler的引用，然后这个Handler对象又引用了外部的Activity。这些引用对象一直保持到消息被处理完，这样的话，导致Activity对象无法被及时回收，造成所谓的“内存泄漏”。
####解决内存泄漏
* 1.使用静态内部类
* 2.使用弱引用,WeakReference
修改代码：

```java
    public class MyActivity extends Activity{
        private Handler mHandler = new MyHandler(this);
        
        private static class MyHandler extends Handler{
            private WeakReference<Context> mReference;
            public MyHandler(Context context){
                mReference=new WeakReference<>(context);
            }
            @Override
            public void handleMessage(Message msg){
                if (mReference.get()!=null){
                    mReference.get().call();
                }
            }
        }
        
        @Override
        public void onDestroy(){
            mHandler.removeCallbacksAndMessages(null);
            super.onDestroy();
        }
    }
```
在Activity执行finish()后，Handler对象还是在Message中排队。所以，可以在onDestroy()中，取消Handler对象的消息对象。

