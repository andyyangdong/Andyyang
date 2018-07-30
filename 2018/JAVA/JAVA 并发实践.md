#  线上定位问题解决 [参照方案](http://techblog.youdao.com/?p=961)

## 简单步骤

1\. top: 记录cpu idle%。如果发现cpu占用过高，则c, shift+h, shift + p查看线程占用CPU情况，并记录

2\. free: 查看内存情况，如果剩余量较小，则top中shift+m查看内存占用情况，并记录

3\. 如果top中发现占用资源较多的进程名称（例如java这样的通用名称）不太能说明进程身份，则要用ps xuf | grep java等方式记录下具体进程的身份 

4\. 取jstack结果。假如取不到，尝试加/F

  jstack命令：**jstack PID > jstack.log**

5\. jstat查看OLD区占用率。如果占用率到达或接近100%，则jmap取结果。假如取不到，尝试加/F 

  jstat命令： **jstat -gcutil PID**

    S0  S1    E      O     P     YGC    YGCT    FGC  FGCT   GCT

   0.00 21.35 88.01 97.35 59.89 111461 1904.894 1458 291.369 2196.263

  jmap命令： ** jmap -dump:file=dump.map PID**

6\. 重启服务



#  性能测试

netstat 查看数据库连接数

ps 查看线程池线程数

查看mysql慢查询日志

查看网络流量 cat /proc/net/dev

查看系统平均负载 cat /proc/loadavg

查看系统内存使用情况 cat /proc/meminfo

查看cpu使用率 cat /proc/sta



#  异部任务池

``` java 
public class MyFutureTask {
	
	private static final ExecutorService EXECUTOR_SERVICE = Executors.newFixedThreadPool(4);
	
    private Future<List<Word>> submit(final String sentence){
        return EXECUTOR_SERVICE.submit(new Callable<List<Word>>(){
        	
            @Override
            public List<Word> call() {
            	
                return segSentence(sentence);
            }

			
        });
    }
    private List<Word> segSentence(String sentence) {
		
    	//http请求或者链接数据库
    	List<Word> sentences=new ArrayList<Word>();
    	sentences.add(new Word("ok")); 
    	sentences.add(new Word("异步线程池的使用"));
    	sentences.add(new Word("ok异步线程池的使用"));
    	sentences.add(new Word("test"));
    	
		return sentences;
	}
    
	public static void main(String[] args) throws Exception, ExecutionException {
		
		Future<List<Word>>  futures =new MyFutureTask().submit("");
		for(Word word:futures.get())
		{
			System.out.println(word);
			
		}
		MyFutureTask.EXECUTOR_SERVICE.shutdown();
	}

}
```
``` java 
/**
 * Java 异步线程池的使用
 * @author Administrator
 *
 */

public class MyFutureTask {
	
	private static final ExecutorService EXECUTOR_SERVICE = Executors.newFixedThreadPool(4);
	
	//异步任务的关键在Future类
    private Future<List<Result>> submit(final String sentence){
    	
    	//EXECUTOR_SERVICE.execute(command);
        return EXECUTOR_SERVICE.submit(new Callable<List<Result>>(){
        	
            @Override
            public List<Result> call() {
            	
                return segSentence(sentence);
            }

			
        });
    }
    private List<Result> segSentence(String sentence) {
		
    	System.out.println("异步线程开始执行");
    	//http请求或者链接数据库
    	List<Result> sentences=new ArrayList<Result>();
    	sentences.add(new Result("ok")); 
    	sentences.add(new Result("异步线程池的使用"));
    	sentences.add(new Result("ok异步线程池的使用"));
    	sentences.add(new Result("test"));
    	
    	System.out.println("异步线程执行完成");
    	try {
			Thread.sleep(1000);
			System.out.println("异步线程休息完成");
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
    	
    	
    	
		return sentences;
	}
    
	public static void main(String[] args) throws Exception, ExecutionException {
		
		Future<List<Result>>  futures =new MyFutureTask().submit("");//提交异步任务，任务会在异步线程执行。
		//主线程继续执行其他。http://blog.csdn.net/yangyan19870319/article/details/6093481
		int i=0;
		while (i<10) {
			
			System.out.println("主线程做其他事情去了");
			i++;
			Thread.sleep(500);
		}	
		//在调用futures.get()方法获取异步任务数据之前，我们可以做其他事情。
		for(Result word:futures.get(5000, TimeUnit.MILLISECONDS))//取得结果，同时设置超时执行时间为5秒。同样可以用future.get()，不设置执行超时时间取得结果 
		{
			System.out.println(word);
			
		}
		MyFutureTask.EXECUTOR_SERVICE.shutdown();
	}

}
``` 


```java 
结果：
主线程做其他事情去了
异步线程开始执行
异步线程执行完成
主线程做其他事情去了
异步线程休息完成
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
主线程做其他事情去了
```
#   [生产者-消费者模式的三种实现方式](http://www.cnblogs.com/fankongkong/p/7339848.html)
并发编程中使用消费者和生产者能够解决绝大多数并发问题。
##  1.背景

生产者生产数据到**缓冲区**中，消费者从**缓冲区**中取数据。

如果**缓冲区**已经满了，则生产者线程阻塞；

如果**缓冲区**为空，那么消费者线程阻塞。
##   2、方式一：synchronized、wait和notify **


``` java 
//wait 和 notify
public class ProducerConsumerWithWaitNofity {
    public static void main(String[] args) {
        Resource resource = new Resource();
        //生产者线程
        ProducerThread p1 = new ProducerThread(resource);
        ProducerThread p2 = new ProducerThread(resource);
        ProducerThread p3 = new ProducerThread(resource);
        //消费者线程
        ConsumerThread c1 = new ConsumerThread(resource);
        //ConsumerThread c2 = new ConsumerThread(resource);
        //ConsumerThread c3 = new ConsumerThread(resource);
    
        p1.start();
        p2.start();
        p3.start();
        c1.start();
        //c2.start();
        //c3.start();
    }
    
    
    
}
/**
 * 公共资源类
 * @author 
 *
 */
class Resource{//重要
    //当前资源数量
    private int num = 0;
    //资源池中允许存放的资源数目
    private int size = 10;

    /**
     * 从资源池中取走资源
     */
    public synchronized void remove(){
        if(num > 0){
            num--;
            System.out.println("消费者" + Thread.currentThread().getName() +
                    "消耗一件资源，" + "当前线程池有" + num + "个");
            notifyAll();//通知生产者生产资源
        }else{
            try {
                //如果没有资源，则消费者进入等待状态
                wait();
                System.out.println("消费者" + Thread.currentThread().getName() + "线程进入等待状态");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    /**
     * 向资源池中添加资源
     */
    public synchronized void add(){
        if(num < size){
            num++;
            System.out.println(Thread.currentThread().getName() + "生产一件资源，当前资源池有" 
            + num + "个");
            //通知等待的消费者
            notifyAll();
        }else{
            //如果当前资源池中有10件资源
            try{
                wait();//生产者进入等待状态，并释放锁
                System.out.println(Thread.currentThread().getName()+"线程进入等待");
            }catch(InterruptedException e){
                e.printStackTrace();
            }
        }
    }
}
/**
 * 消费者线程
 */
class ConsumerThread extends Thread{
    private Resource resource;
    public ConsumerThread(Resource resource){
        this.resource = resource;
    }
    @Override
    public void run() {
        while(true){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            resource.remove();
        }
    }
}
/**
 * 生产者线程
 */
class ProducerThread extends Thread{
    private Resource resource;
    public ProducerThread(Resource resource){
        this.resource = resource;
    }
    @Override
    public void run() {
        //不断地生产资源
        while(true){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            resource.add();
        }
    }
    
}

