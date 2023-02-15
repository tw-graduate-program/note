# IdWorker

#### 分布式数据库 ID 生成

分布式系统中，有一些需要使用全局唯一ID的场景，这种时候为了防止ID冲突可以使用36位的UUID，但是UUID有一些缺点，首先他相对比较长，另外UUID一般是无序的。希望能使用一种简单一些的ID，并且希望ID能够按照时间有序生成。而twitter的snowflake解决了这种需求。

Snowflake算法生成主键 ID 的结果是一个64bit大小的整数，结构如下：

![image-20230215205346779](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20230215205346779.png)

### snowflake的结构(每部分用-分开):

0 - 0000000000 0000000000 0000000000 0000000000 0 - 00000 - 00000 - 000000000000



1位，不用。二进制中最高位为1的都是负数，但是生成的id一般都使用整数，所以这个最高位固定是0；

41位，用来记录时间戳（毫秒）。41位可以表示241−1241−1个数字，如果只用来表示正整数（计算机中正数包含0），可以表示的数值范围是：0 至 241−1241−1，减1是因为可表示的数值范围是从0开始算的，而不是1。也就是说41位可以表示241−1241−1个毫秒的值，转化成单位年则是  **(241−1)/(1000∗60∗60∗24∗365)=69(241−1)/(1000∗60∗60∗24∗365)=69年**

10位，用来记录工作机器id。可以部署在 **210=1024210=1024个节点**，包括5位datacenterId和5位workerId 5位（bit）可以表示的最大正整数是25−1=3125−1=31，即可以用0、1、2、3、....31这32个数字，来表示不同的datecenterId或workerId 12位，序列号，用来 **记录同毫秒内产生的不同id**。

12位（bit）可以表示的最大正整数是212−1=4095212−1=4095，即可以用0、1、2、3、....4094这4095个数字，来表示 **同一机器同一时间截（毫秒)内产生的4095个ID序号**。



由于在Java中64bit的整数是long类型，所以在Java中SnowFlake算法生成的id就是long来存储的.

1. 所有生成的id按时间递增
2. 分布式系统内不会产生重复id-由datacenterId和workerId进行区分



## IdWorker 使用

Reference：https://github.com/imadcn/idworker

https://github.com/twitter-archive/snowflake/tree/scala_28

#### Maven

```yaml
<dependency>
  <groupId>com.imadcn.framework</groupId>
  <artifactId>idworker</artifactId>
  <version>${latest.version}</version>
</dependency>
```

#### Bean Configuration

```java
@Configuration
public class IdAutoConfig {
    @Value("127.0.0.1:2181")
    private String zkServerLists;
  
    @Bean
    public IdentifierGenerator idGenerator() {
        return new ImadcnIdentifierGenerator(zkServerLists);
    }
}
```

#### IdWorker

```java
public class IdWorker {
	private long workerId;
    private long datacenterId;
    private long sequence;

    public IdWorker(long workerId, long datacenterId, long sequence){
        // sanity check for workerId
        if (workerId > maxWorkerId || workerId < 0) {
            throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0",maxWorkerId));
        }
        if (datacenterId > maxDatacenterId || datacenterId < 0) {
            throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0",maxDatacenterId));
        }
        System.out.printf("worker starting. timestamp left shift %d, datacenter id bits %d, worker id bits %d, sequence bits %d, workerid %d",
                timestampLeftShift, datacenterIdBits, workerIdBits, sequenceBits, workerId);

        this.workerId = workerId;
        this.datacenterId = datacenterId;
        this.sequence = sequence;
    }

    private long twepoch = 1288834974657L;

    private long workerIdBits = 5L;
    private long datacenterIdBits = 5L;
    private long maxWorkerId = -1L ^ (-1L << workerIdBits);
    private long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);
    private long sequenceBits = 12L;

    private long workerIdShift = sequenceBits;
    private long datacenterIdShift = sequenceBits + workerIdBits;
    private long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;
    private long sequenceMask = -1L ^ (-1L << sequenceBits);

    private long lastTimestamp = -1L;

    public long getWorkerId(){
        return workerId;
    }

    public long getDatacenterId(){
        return datacenterId;
    }

    public long getTimestamp(){
        return System.currentTimeMillis();
    }

    public synchronized long nextId() {
        long timestamp = timeGen();

        if (timestamp < lastTimestamp) {
            System.err.printf("clock is moving backwards.  Rejecting requests until %d.", lastTimestamp);
            throw new RuntimeException(String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds",
                    lastTimestamp - timestamp));
        }

        if (lastTimestamp == timestamp) {
            sequence = (sequence + 1) & sequenceMask;
            if (sequence == 0) {
                timestamp = tilNextMillis(lastTimestamp);
            }
        } else {
            sequence = 0;
        }

        lastTimestamp = timestamp;
        return ((timestamp - twepoch) << timestampLeftShift) |
                (datacenterId << datacenterIdShift) |
                (workerId << workerIdShift) |
                sequence;
    }

    private long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    private long timeGen(){
        return System.currentTimeMillis();
    }
}
```

