### 前言 
整理了Java日期处理的十个坑，希望对大家有帮助。

### 一、用Calendar设置时间的坑
**反例：**
```
Calendar c = Calendar.getInstance();
c.set(Calendar.HOUR, 10);
System.out.println(c.getTime());
```
**运行结果：**

```
Thu Mar 26 22:28:05 GMT+08:00 2020
```
**解析：**

我们设置了10小时，但运行结果是22点，而不是10点。因为Calendar.HOUR默认是按12小时制处理的，需要使用Calendar.HOUR_OF_DAY，因为它才是按24小时处理的。

**正例：**

```
Calendar c = Calendar.getInstance();
c.set(Calendar.HOUR_OF_DAY, 10);
```
### 二、Java日期格式化YYYY的坑

**反例：**

```
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.DECEMBER, 31);

Date testDate = calendar.getTime();

SimpleDateFormat dtf = new SimpleDateFormat("YYYY-MM-dd");
System.out.println("2019-12-31 转 YYYY-MM-dd 格式后 " + dtf.format(testDate));
```
**运行结果：**

```
2019-12-31 转 YYYY-MM-dd 格式后 2020-12-31
```
**解析：**

为什么明明是2019年12月31号，就转了一下格式，就变成了2020年12月31号了？因为YYYY是基于周来计算年的，它指向当天所在周属于的年份，一周从周日开始算起，周六结束，只要本周跨年，那么这一周就算下一年的了。正确姿势是使用yyyy格式。

![](https://user-gold-cdn.xitu.io/2020/3/26/171176d2ef2535d0?w=553&h=553&f=png&s=48060)

**正例：**

```
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.DECEMBER, 31);

Date testDate = calendar.getTime();

SimpleDateFormat dtf = new SimpleDateFormat("yyyy-MM-dd");
System.out.println("2019-12-31 转 yyyy-MM-dd 格式后 " + dtf.format(testDate));
```

### 三、Java日期格式化hh的坑。

**反例：**

```
String str = "2020-03-18 12:00";
SimpleDateFormat dtf = new SimpleDateFormat("yyyy-MM-dd hh:mm");
Date newDate = dtf.parse(str);
System.out.println(newDate);
```
**运行结果：**

```
Wed Mar 18 00:00:00 GMT+08:00 2020
```
**解析：**

设置的时间是12点，为什么运行结果是0点呢？因为hh是12制的日期格式，当时间为12点，会处理为0点。正确姿势是使用HH，它才是24小时制。

**正例：**

```
String str = "2020-03-18 12:00";
SimpleDateFormat dtf = new SimpleDateFormat("yyyy-MM-dd HH:mm");
Date newDate = dtf.parse(str);
System.out.println(newDate);
```

### 四、Calendar获取的月份比实际数字少1即(0-11)
**反例：**

```
//获取当前月，当前是3月
Calendar calendar = Calendar.getInstance();
System.out.println("当前"+calendar.get(Calendar.MONTH)+"月份");
```
**运行结果：**

```
当前2月份
```
**解析：**

```
The first month of the year in the Gregorian and Julian calendars
is <code>JANUARY</code> which is 0; 
也就是1月对应的是下标 0，依次类推。因此获取正确月份需要加 1.
```

**正例：**

```
//获取当前月，当前是3月
Calendar calendar = Calendar.getInstance();
System.out.println("当前"+(calendar.get(Calendar.MONTH)+1)+"月份");
```

### 五、Java日期格式化DD的坑

**反例：**

```
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.DECEMBER, 31);

Date testDate = calendar.getTime();

SimpleDateFormat dtf = new SimpleDateFormat("yyyy-MM-DD");
System.out.println("2019-12-31 转 yyyy-MM-DD 格式后 " + dtf.format(testDate));
```
**运行结果：**

```
2019-12-31 转 yyyy-MM-DD 格式后 2019-12-365
```
**解析：**

DD和dd表示的不一样，DD表示的是一年中的第几天，而dd表示的是一月中的第几天，所以应该用的是dd。

**正例：**

```
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.DECEMBER, 31);

Date testDate = calendar.getTime();

SimpleDateFormat dtf = new SimpleDateFormat("yyyy-MM-dd");
System.out.println("2019-12-31 转 yyyy-MM-dd 格式后 " + dtf.format(testDate));
```

### 六、SimleDateFormat的format初始化问题
**反例：**

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
System.out.println(sdf.format(20200323));
```
**运行结果：**

```
1970-01-01
```
**解析：**

用format格式化日期是，要输入的是一个Date类型的日期，而不是一个整型或者字符串。

**正例：**

```
Calendar calendar = Calendar.getInstance();
calendar.set(2020, Calendar.MARCH, 23);
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
System.out.println(sdf.format(calendar.getTime()));
```

### 七、日期本地化问题

**反例：**
```
String dateStr = "Wed Mar 18 10:00:00 2020";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("EEE MMM dd HH:mm:ss yyyy");
LocalDateTime dateTime = LocalDateTime.parse(dateStr, formatter);
System.out.println(dateTime);
```
**运行结果：**

```
Exception in thread "main" java.time.format.DateTimeParseException: Text 'Wed Mar 18 10:00:00 2020' could not be parsed at index 0
	at java.time.format.DateTimeFormatter.parseResolved0(DateTimeFormatter.java:1949)
	at java.time.format.DateTimeFormatter.parse(DateTimeFormatter.java:1851)
	at java.time.LocalDateTime.parse(LocalDateTime.java:492)
	at com.example.demo.SynchronizedTest.main(SynchronizedTest.java:19)
```
**解析：**

DateTimeFormatter 这个类默认进行本地化设置，如果默认是中文，解析英文字符串就会报异常。可以传入一个本地化参数（Locale.US）解决这个问题

**正例：**

```
String dateStr = "Wed Mar 18 10:00:00 2020";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("EEE MMM dd HH:mm:ss yyyy",Locale.US);
LocalDateTime dateTime = LocalDateTime.parse(dateStr, formatter);
System.out.println(dateTime);
```

### 八、SimpleDateFormat 解析的时间精度问题

**反例：**
```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
String time = "2020-03";
System.out.println(sdf.parse(time));
```
**运行结果：** 

```
Exception in thread "main" java.text.ParseException: Unparseable date: "2020-03"
	at java.text.DateFormat.parse(DateFormat.java:366)
	at com.example.demo.SynchronizedTest.main(SynchronizedTest.java:19)
```
**解析：**

SimpleDateFormat 可以解析长于/等于它定义的时间精度，但是不能解析小于它定义的时间精度。

**正例：**

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM");
String time = "2020-03";
System.out.println(sdf.parse(time));
```

### 九、SimpleDateFormat 的线性安全问题

**反例：**

```
public class SimpleDateFormatTest {

    private static final SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    public static void main(String[] args) {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10, 100, 1, TimeUnit.MINUTES, new LinkedBlockingQueue<>(1000));

        while (true) {
            threadPoolExecutor.execute(() -> {
                String dateString = sdf.format(new Date());
                try {
                    Date parseDate = sdf.parse(dateString);
                    String dateString2 = sdf.format(parseDate);
                    System.out.println(dateString.equals(dateString2));
                } catch (ParseException e) {
                    e.printStackTrace();
                }
            });
        }
    }
```
**运行结果：**

```
Exception in thread "pool-1-thread-49" java.lang.NumberFormatException: For input string: "5151."
	at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
	at java.lang.Long.parseLong(Long.java:589)
	at java.lang.Long.parseLong(Long.java:631)
	at java.text.DigitList.getLong(DigitList.java:195)
	at java.text.DecimalFormat.parse(DecimalFormat.java:2051)
	at java.text.SimpleDateFormat.subParse(SimpleDateFormat.java:2162)
	at java.text.SimpleDateFormat.parse(SimpleDateFormat.java:1514)
	at java.text.DateFormat.parse(DateFormat.java:364)
	at com.example.demo.SimpleDateFormatTest.lambda$main$0(SimpleDateFormatTest.java:19)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Exception in thread "pool-1-thread-47" java.lang.NumberFormatException: For input string: "5151."
	at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
	at java.lang.Long.parseLong(Long.java:589)
	at java.lang.Long.parseLong(Long.java:631)
	at java.text.DigitList.getLong(DigitList.java:195)
	at java.text.DecimalFormat.parse(DecimalFormat.java:2051)
	at java.text.SimpleDateFormat.subParse(SimpleDateFormat.java:2162)
	at java.text.SimpleDateFormat.parse(SimpleDateFormat.java:1514)
	at java.text.DateFormat.parse(DateFormat.java:364)
	at com.example.demo.SimpleDateFormatTest.lambda$main$0(SimpleDateFormatTest.java:19)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
```
**解析：**

全局变量的SimpleDateFormat，在并发情况下，存在安全性问题。
- SimpleDateFormat继承了 DateFormat
- DateFormat类中维护了一个全局的Calendar变量
- sdf.parse(dateStr)和sdf.format(date)，都是由Calendar引用来储存的。
- 如果SimpleDateFormat是static全局共享的，Calendar引用也会被共享。
- 又因为Calendar内部并没有线程安全机制，所以全局共享的SimpleDateFormat不是线性安全的。

**解决SimpleDateFormat线性不安全问题，有三种方式：**
- 将SimpleDateFormat定义为局部变量
- 使用ThreadLocal。
- 方法加同步锁synchronized。

**正例：**

```
public class SimpleDateFormatTest {

    private static final String DATE_FORMAT = "yyyy-MM-dd HH:mm:ss";
    private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>();

    public static DateFormat getDateFormat() {
        DateFormat df = threadLocal.get();
        if(df == null){
            df = new SimpleDateFormat(DATE_FORMAT);
            threadLocal.set(df);
        }
        return df;
    }

    public static String formatDate(Date date) throws ParseException {
        return getDateFormat().format(date);
    }

    public static Date parse(String strDate) throws ParseException {
        return getDateFormat().parse(strDate);
    }

    public static void main(String[] args) {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10, 100, 1, TimeUnit.MINUTES, new LinkedBlockingQueue<>(1000));

        while (true) {
            threadPoolExecutor.execute(() -> {
                try {
                    String dateString = formatDate(new Date());
                    Date parseDate = parse(dateString);
                    String dateString2 = formatDate(parseDate);
                    System.out.println(dateString.equals(dateString2));
                } catch (ParseException e) {
                    e.printStackTrace();
                }
            });
        }
    }
}
```

### 十、Java日期的夏令时问题

**反例：**

```
TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai"));

SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(sdf.parse("1986-05-04 00:30:00"));
```
**运行结果：**

```
Sun May 04 01:30:00 CDT 1986
```
**解析：**

先了解一下夏令时
> - 夏令时，表示为了节约能源，人为规定时间的意思。
> - 一般在天亮早的夏季人为将时间调快一小时，可以使人早起早睡，减少照明量，以充分利用光照资源，从而节约照明用电。
> - 各个采纳夏时制的国家具体规定不同。目前全世界有近110个国家每年要实行夏令时。
> - 1986年4月，中国中央有关部门发出“在全国范围内实行夏时制的通知”，具体作法是：每年从四月中旬第一个星期日的凌晨2时整（北京时间），将时钟拨快一小时。(1992年起，夏令时暂停实行。)
> - 夏时令这几个时间可以注意一下哈，1986-05-04, 1987-04-12, 1988-04-10, 1989-04-16, 1990-04-15, 1991-04-14.

结合demo代码，中国在1986-05-04当天还在使用夏令时，时间被拨快了1个小时。所以0点30分打印成了1点30分。如果要打印正确的时间，可以考虑修改时区为东8区。

**正例：**

```
TimeZone.setDefault(TimeZone.getTimeZone("GMT+8"));

SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(sdf.parse("1986-05-04 00:30:00"));
```

### 个人公众号

![](https://user-gold-cdn.xitu.io/2019/7/28/16c381c89b127bbb?w=344&h=344&f=jpeg&s=8943)

- 觉得写得好的小伙伴给个点赞+关注啦，谢谢~
- 如果有写得不正确的地方，麻烦指出，感激不尽。
- 同时非常期待小伙伴们能够关注我公众号，后面慢慢推出更好的干货~嘻嘻