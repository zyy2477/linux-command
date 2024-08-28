# iostat

iostat 被用于监视系统输入输出设备和 CPU 的使用情况。它的特点是汇报磁盘活动统计情况，同时也会汇报出 CPU 使用情况。同 vmstat 一样，iostat 也有一个弱点，就是它不能对某个进程进行深入分析，仅对系统的整体情况进行分析。

iowait 指在一个采样周期内有百分之几的时间是属于以下情况：CPU 处于空闲状态并且至少有一个未完成的磁盘 IO 请求。
            以 Rocky linux 8.8 上的 iostat 做介绍，版本为：11.7.3。

## 安装 iostat

iostat包含在**sysstat**包内。如果你没有，你首先需要安装它。

在 RedHat / CentOS / Fedora

```bash
yum install sysstat 
```

在 Debian / Ubuntu / Linux Mint

```bash
sudo apt-get install sysstat
```

## 语法

```bash
iostat(选项)(参数)
```

### 选项

```bash
# iostat --help
Usage: iostat [ options ] [ <interval> [ <count> ] ]
Options are:
[ -c ] [ -d ] [ -h ] [ -k | -m ] [ -N ] [ -s ] [ -t ] [ -V ] [ -x ] [ -y ] [ -z ]
[ -j { ID | LABEL | PATH | UUID | ... } ] [ --human ] [ -o JSON ]
[ [ -H ] -g <group_name> ] [ -p [ <device> [,...] | ALL ] ]
```

参数说明：
-c         输出CPU统计信息。不能与-d参数同时使用。
-d         输出设备和分区的I/O统计信息。不能与-c参数同时使用。(默认的参数是-d)
-h         让人类更容易阅读设备使用情况报告。--human使用此选项隐式启用。
-k         用“kbytes/秒”代替“块/秒”显示统计信息。
-m         用“mbytes/秒”代替“块/秒”显示统计信息。
-t         打印显示的每份报告的时间。
-V         显示版本号并退出。
-x         显示扩展统计信息。不能与参数-p同时使用。
-y         如果在给定时间间隔内显示多条记录，则省略自系统启动以来的第一份统计报告。
-z         告诉 iostat 忽略在样本期间没有活动的设备的输出。
-j         显示持久的设备名称。选项ID、LABEL等指定持久名称的类型。
--human    以人类可读格式打印大小(例如1.0k, 1.2M等)。使用此选项显示的单位取代与度量相关的任何其他默认单位(例如千字节，扇区…)。
-o         以JSON (Javascript Object Notation)格式显示统计数据。JSON输出字段顺序未定义，未来可能会添加新字段。
-H         该选项必须与-g一起使用，表示只显示组的全局统计信息，而不显示组中单个设备的统计信息。
-p         选项显示系统使用的块设备及其所有分区的统计信息。如果在命令行中输入一个设备名，那么将显示该设备及其所有分区的统计信息。最后，ALL关键字表示必须显示系统定义的所有块设备和分区的统计信息，包括那些从未使用过的设备和分区。如果在此选项之前定义了选项-j，则可以使用所选的持久名称类型指定在命令行上输入的设备。

### 参数

interval 刷新时间间隔
count 刷新次数

## 示例一

```bash
# iostat
Linux 4.18.0-477.13.1.el8_8.x86_64 (node1) 	08/28/2024 	_x86_64_	(2 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.30    0.01    1.90    0.12    0.00   94.68

Device             tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
vda               7.85        84.22        36.59 1081853831  470049100

```

第一部分包含了CPU报告：

| 性能指标 | 含义 |
| --- | --- |
| %user | 显示在用户级(应用程序)执行时发生的CPU利用率百分比。 |
| %nice  | 显示在具有nice优先级的用户级别执行时发生的CPU利用率百分比。 |
| %system | 显示在系统级(内核)执行时发生的CPU利用率百分比。 |
| %iowait | 显示在系统有未完成的磁盘I/O请求期间CPU空闲的时间百分比。 |
| %steal | 显示虚拟机监控程序为另一个虚拟处理器提供服务时，一个或多个虚拟CPU在非自愿等待上花费的时间百分比。 |
| %idle | 显示CPU空闲且系统没有未完成的磁盘I/O请求的时间百分比。 |

第二部分包含了设备利用率报告：

| 性能指标 | 含义 |
| --- | --- |
| Device | 此列给出了 /dev 目录中列出的设备（或分区）名称。 |
| tps | 表示每秒向设备发出的传输次数。传输是对设备的 I/O 请求。多个逻辑请求可合并为对设备的一个 I/O 请求。传输大小不确定。 |
| kB_read/s | 表示每秒从设备读取的数据量，以块数（千字节、兆字节）表示。块相当于扇区，因此大小为 512 字节。 |
| kB_wrtn/s | 表示写入设备的数据量，以每秒块数（千字节、兆字节）表示。 |
| kB_read | 读取的数据块总数（千字节、兆字节）。 |
| kB_wrtn | 写入的数据块总数（千字节、兆字节）。 |

## 示例二

通过`d`参数显示磁盘信息，`-h`参数更容易阅读，每个 1 秒刷新一次数据，共 5 次。

```bash
# iostat -hd 1 5
Linux 4.18.0-477.13.1.el8_8.x86_64 (node1) 	08/28/2024 	_x86_64_	(2 CPU)

      tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn Device
     7.85        84.2k        36.6k       1.0T     448.3G vda

      tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn Device
     0.00         0.0k         0.0k       0.0k       0.0k vda

      tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn Device
     1.00         4.0k         0.0k       4.0k       0.0k vda

      tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn Device
    30.00         4.0k       330.5k       4.0k     330.5k vda

      tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn Device
     0.00         0.0k         0.0k       0.0k       0.0k vda
```

## 示例三

通过`-d`显示磁盘信息，加上`-x`参数可获取更多 IO 信息，每 1 秒刷新一次数据：

```bash
# iostat -xd 1
Linux 4.18.0-477.13.1.el8_8.x86_64 (node1) 	08/28/2024 	_x86_64_	(2 CPU)

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              4.64    3.21     84.14     36.59     0.28     1.07   5.64  25.01    5.86    8.23   0.05    18.12    11.41   0.44   0.34

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              1.00    0.00      4.00      0.00     0.00     0.00   0.00   0.00    1.00    0.00   0.00     4.00     0.00   2.00   0.20

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              0.00   37.00      0.00    395.50     0.00    10.00   0.00  21.28    0.00    0.92   0.03     0.00    10.69   0.11   0.40

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    0.00    0.00   0.00     0.00     0.00   0.00   0.00

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    0.00    0.00   0.00     0.00     0.00   0.00   0.00

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
vda              1.00    0.00      4.00      0.00     0.00     0.00   0.00   0.00    1.00    0.00   0.00     4.00     0.00   1.00   0.10

```

从这里你可以看到，iostat 提供了非常丰富的性能指标。第一列的 Device 表示磁盘设备的名字，其他各列指标，虽然数量较多，但是每个指标的含义都很重要。

| 性能指标 | 含义 |
| --- | --- |
| r/s | 设备每秒完成的读取请求数（合并后）。 |
| w/s | 设备每秒完成的写入请求数（合并后）。 |
| rkB/s | 每秒从设备读取的扇区数（千字节、兆字节）。 |
| wkB/s | 每秒写入设备的扇区数（千字节、兆字节）。 |
| rrqm/s | 每秒合并到设备的排队 I/O 请求数。 |
| wrqm/s | 每秒合并到设备的写入请求数。 |
| %rrqm | 在发送到设备之前合并在一起的读取请求的百分比。 |
| %wrqm | 发送到设备前合并在一起的写入请求的百分比。 |
| r_await | 向设备发出的读取请求获得服务的平均时间（毫秒）。这包括请求在队列中花费的时间和为请求提供服务的时间。 |
| w_await | 向待服务设备发出写入请求的平均时间（毫秒）。这包括请求在队列中花费的时间和为请求提供服务的时间。 |
| aqu-sz | 向设备发出的请求的平均队列长度。注意：在以前的版本中，该字段被称为 avgqu-sz。 |
| rareq-sz | 向设备发出的读取请求的平均大小（千字节）。 |
| wareq-sz | 向设备发出的写入请求的平均大小（千字节）。 |
|  svctm | 向设备发出的 I/O 请求的平均服务时间（毫秒）。警告！不要再相信这个字段了。 未来的 sysstat 版本将删除此字段。 |
| %util | 向设备发出 I/O 请求所用时间的百分比（设备带宽利用率）。 对于串行服务请求的设备，当该值接近 100%，设备就会饱和。 但对于以并行方式提供请求的设备，如 RAID 阵列和现代固态硬盘，该数值并不能反映其性能极限。 |
