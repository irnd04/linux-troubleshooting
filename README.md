# linux-troubleshooting

http://www.brendangregg.com

![image](https://user-images.githubusercontent.com/45504115/136681720-791b58b7-0eec-4642-a0a5-37486b2dc70b.png)


### uptime
```
13:18:46 up 1 min,  1 user,  load average: 2.99, 0.97, 0.34
```

* 서버가 시작한 지 얼마나 되는지를 확인하기 위한 명령어
* 뒷부분에 있는 load average는 앞에서부터 1분, 5분, 15분간의 평균값. 이값은 0에 가까우면 좋고, 멀수록 좋지않다.
* 1분 평균이 5분이나 15분 평균보다 높으면 아직도 문제가 지속되고 있다는 의미
* load : https://en.wikipedia.org/wiki/Load_(computing)

### dmesg | tail
* dmesg 명령은 시스템 메세지를 확인하는 명령어. tail을 사용했기 때문에 최근 시스템메시지를 확인할 수 있다.

### vmstat 1
```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  1      0 4607960 209244 1530872    0    0   910   513  248  381  3  6 81  9  0
 1  0      0 4599120 213024 1535740    0    0  4416     0 2301 4854  0  7 72 20  0
 0  1      0 4590260 217340 1540232    0    0  4832     0 2387 4024  0  8 72 19  0
 1  1      0 4582048 221932 1543864    0    0  5088   112 3034 5386  1  8 71 20  0
 1  0      0 4572304 227600 1548340    0    0  6308   648 3791 6257  0 14 67 19  0
 0  1      0 4555968 235316 1556696    0    0  8740     0 4888 8762  0 16 67 16  0
 1  1      0 4575656 242984 1559772    0    0  8052   484 3030 5137  6 13 62 19  0
 2  0      0 4552728 250900 1563540    0    0  8432     0 1929 4478 22  7 51 20  0
 1  1      0 4526024 258996 1569096    0    0  8700   428 2150 5785 23  9 49 19  0
 2  1      0 4514100 265484 1573944    0    0  7256     0 1769 4778 24  7 50 20  0
 1  1      0 4503760 271060 1578860    0    0  6088     0 1908 5645 24  6 50 19  0
 2  0      0 4490228 278824 1585752    0    0  8524     0 2305 4701 24 10 48 19  0
 2  0      0 4457480 281232 1615028    0    0  7111     0 1767 5851 23 24 49  4  0
 1  1      0 4439148 284220 1631456    0    0  7858 18800 2864 6564 22 18 46 14  0
 2  1      0 4431312 289268 1634856    0    0  5432     0 2568 4675 25 10 47 17  0
 1  1      0 4414316 294504 1641280    0    0  5876     0 3238 5686 22 14 46 18  0
```

구분 | 항목 | 내용
-- | -- | --
Procs | r | CPU의 Run Queue를 의미하며, CPU가 바빠서 자기 차례를 기다리는 프로세스의 개수를 뜻함. CPU코어 개수보다 이 숫자가 많으면 서버가 아주 바쁜 상황이라고 판단할 수 있음.
Procs | b | 중단 불가능한 슬립(uninterruptible sleep) 상태에 있는 프로세스의 수
Memory | swpd | 가상 메모리를 사용한 크기
Memory | free | 사용하지 않는 메모리의 크기
Memory | buff | 버퍼로 사용하는 메모리의 크기
Memory | cache | 캐시로 사용하는 메모리의 크기
Swap | si | 초당 디스크에서 읽은 스왑된 메모리의 크기
Swap | so | 초당 디스크로 스왑된 메모리의 크기
I/O | bi | 블록 디바이스에서 받은 블록 수(blocks/s)
I/O | bo | 블록 디바이스로 보낸 블록 수 
System | in | 클럭(clock)을 포함한 초당 인터럽트 수 
System | cs | 초당 컨텍스트 전환 개수
CPU | us | 사용자 애플리케이션(non-kernel code)코드에 의해 사용되는 CPU 시간
CPU | sy | 시스템(kernel code)에 의해 사용되는 CPU 시간
CPU | id | idle 상태에서 수행된 시간
CPU | wa | I/O를 대기하는 데 소요된 시간 

* vmstat 명령은 가상 메모리의 통계 정보를 제공. 프로세스, 메모리, 페이징, 블록, I/O, 트랩스, 디스크, CPU에 대한 정보를 제공한다.
* vmstsat을 수행하면 위와 같이 첫번째 결과는 다른 결과와 다르게 수치가 많이 차이나는 것을 볼 수 있다. 이는 vmstat의 명령어를 수행한 후 항상 첫번째 줄은 이전의 vmstat 명령어로 수집된 정도에 대한 평균치를 나타내기 때문이다. 즉 현재의 정보가 아닌 이전에 누적된 정보임을 기억해야 한다.

### mpstat -P ALL 1
```
ubuntu@ubuntu:~$ mpstat -P ALL 1
Linux 5.4.0-87-generic (ubuntu) 	10/10/2021 	_x86_64_	(4 CPU)

01:34:06 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
01:34:07 PM  all    0.50    0.00    1.76    0.00    0.00    0.00    0.00    0.00    0.00   97.73
01:34:07 PM    0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
01:34:07 PM    1    1.00    0.00    4.00    0.00    0.00    0.00    0.00    0.00    0.00   95.00
01:34:07 PM    2    0.99    0.00    3.96    0.00    0.00    0.00    0.00    0.00    0.00   95.05
01:34:07 PM    3    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00

01:34:07 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
01:34:08 PM  all    0.25    0.00    0.75    0.00    0.00    0.00    0.00    0.00    0.00   98.99
01:34:08 PM    0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
01:34:08 PM    1    1.02    0.00    1.02    0.00    0.00    0.00    0.00    0.00    0.00   97.96
01:34:08 PM    2    0.00    0.00    1.01    0.00    0.00    0.00    0.00    0.00    0.00   98.99
01:34:08 PM    3    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
``` 
항목 | 내용
---- | ---- 
%usr | 사용자 레벨(어플레이케이션)에서 수행되는 동안 사용한 CPU 시간의 비율
%nice | nice 우선순위로 사용자 레벨에서 수행되는 동안 사용한 CPU 시간의 비율
%sys | 시스템(커널) 레벨에서 수행되는 동안 사용한 CPU 시간의 비율. 참고로 이 값은 하드웨어와 소프트웨어의 인터럽트를 처리하는데 수행된 시간은 포함되지 않는다.
%iowait | 시스템 디스크의 I/O요청을 처리하는 동안 CPU가 유휴(idle) 상태인 시간의 비율
%irq | 하드웨어 인터럽트를 CPU에서 처리하는 데 사용한 CPU 시간의 비율
%soft | 소프트웨어 인터럽트를 처리하는 데 사용한 CPU 시간의 비율
%idle | CPU가 유휴(idle) 상태인 시간의 비율

- mpstat 명령어는 CPU별 사용량을 확인할 수 있다. 이를 통하여 단일 CPU만 열심히 일하고 있는지, 여러 CPU가 분산되어 일하고 있는지를 확인할 수 있다.
- 사용법: mpstat [ 옵션 ] [ <간격> [ <회수> ] ]

### ps -Lf -p {pid}
```
 ubuntu@ubuntu:~$ ps -Lf -p 5934
UID         PID   PPID    LWP  C NLWP STIME TTY          TIME CMD
ubuntu     5934   5891   5934  0   73 14:34 tty2     00:00:00 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5935  0   73 14:34 tty2     00:00:00 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5936  2   73 14:34 tty2     00:00:04 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5937  2   73 14:34 tty2     00:00:04 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5938  1   73 14:34 tty2     00:00:03 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5939  0   73 14:34 tty2     00:00:00 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5940  0   73 14:34 tty2     00:00:00 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5941  0   73 14:34 tty2     00:00:00 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5942 39   73 14:34 tty2     00:01:24 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
ubuntu     5934   5891   5943 16   73 14:34 tty2     00:00:34 /opt/idea-IU-201.7846.76/jbr/bin/java -classpath /opt/idea-IU-201.7846.76/lib/bootstrap.jar:/opt/idea-IU-201.7846.76/lib/extensions.jar:/opt/i
```
항목 | 내용
---- | ---- 
UID | 사용자 id
PID | 프로세스 id
PPID | 부모 프로세스 id
LWP | Light Weight Process 의 약자로 스레드 id
C | 프로세서 사용량을 나타내는 값으로, 해당 프로세스가 지속해서 사용한 CPU 사용량
NLWP | 해당 프로세스에서 사용하는 스레드의 수. 즉 LWP의 개수
STIME | 프로세스가 시작한 시간
TTY | 해당 프로세스를 통제하는 터미널
TIME | CPU 점유시간
CMD | 프로세스를 수행시킨 

* 해당 프로세스의 모든 스레드 정보를 상세히 확인할 수 있다.


### pidstat 1
```
ubuntu@ubuntu:~$ pidstat -p 5934 1
Linux 5.4.0-87-generic (ubuntu) 	10/10/2021 	_x86_64_	(4 CPU)

02:41:03 PM   UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
02:41:04 PM  1000      5934    1.00    1.00    0.00    0.00    2.00     0  java
02:41:05 PM  1000      5934    0.00    0.00    0.00    0.00    0.00     0  java
02:41:06 PM  1000      5934    0.00    1.00    0.00    0.00    1.00     0  java
02:41:07 PM  1000      5934    1.00    0.00    0.00    0.00    1.00     0  java
02:41:08 PM  1000      5934    1.00    0.00    0.00    0.00    1.00     0  java
02:41:09 PM  1000      5934    0.00    1.00    0.00    0.00    1.00     0  java
02:41:10 PM  1000      5934    1.00    1.00    0.00    0.00    2.00     0  java

```

* 프로세스별 CPU 사용량을 확인하는 명령. 
* %CPU값은 코어별 누적이다. 예를 들어 %CPU가 1600이라면 16개의 CPU를 100% 사용하고 있다는 뜻이다

### iostat -xz 1
```
ubuntu@ubuntu:~$ iostat -xz 1
Linux 5.4.0-87-generic (ubuntu) 	10/10/2021 	_x86_64_	(4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.53    0.06    0.71    0.83    0.00   97.87

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
loop0            0.00    0.00      0.03      0.00     0.00     0.00   0.00   0.00   12.73    0.00   0.00     6.73     0.00  12.86   0.01
loop1            0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    9.00    0.00   0.00     1.00     0.00  12.00   0.00
loop2            0.04    0.00      0.13      0.00     0.00     0.00   0.00   0.00    5.09    0.00   0.00     3.17     0.00   4.39   0.02
loop3            0.01    0.00      0.10      0.00     0.00     0.00   0.00   0.00   12.93    0.00   0.00    11.67     0.00  12.64   0.01
loop4            0.00    0.00      0.09      0.00     0.00     0.00   0.00   0.00   30.33    0.00   0.00    18.98     0.00  19.85   0.01
loop5            0.00    0.00      0.01      0.00     0.00     0.00   0.00   0.00   16.07    0.00   0.00     2.60     0.00  16.44   0.01
loop6            0.00    0.00      0.01      0.00     0.00     0.00   0.00   0.00   21.57    0.00   0.00     2.63     0.00  21.91   0.01
loop7            0.02    0.00      0.11      0.00     0.00     0.00   0.00   0.00    5.12    0.00   0.00     5.41     0.00   5.36   0.01
sda             10.24    2.16    203.51    121.28     4.02     3.29  28.17  60.40    6.61    1.11   0.06    19.87    56.26   2.49   3.09
loop8            1.17    0.00      1.26      0.00     0.00     0.00   0.00   0.00    8.01    0.00   0.01     1.08     0.00   0.52   0.06
loop9            0.00    0.00      0.01      0.00     0.00     0.00   0.00   0.00   10.78    0.00   0.00     2.83     0.00  11.41   0.00
loop10           0.01    0.00      0.10      0.00     0.00     0.00   0.00   0.00    5.74    0.00   0.00     8.78     0.00   5.80   0.01
loop11           0.02    0.00      0.05      0.00     0.00     0.00   0.00   0.00    4.66    0.00   0.00     2.40     0.00   4.93   0.01
loop12           0.01    0.00      0.10      0.00     0.00     0.00   0.00   0.00   12.20    0.00   0.00     9.58     0.00  11.38   0.01
loop13           0.02    0.00      0.05      0.00     0.00     0.00   0.00   0.00    2.82    0.00   0.00     2.27     0.00   3.06   0.01
loop14           0.05    0.00      0.14      0.00     0.00     0.00   0.00   0.00    1.90    0.00   0.00     2.94     0.00   1.96   0.01
loop15           0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00   15.05    0.00   0.00     2.42     0.00  15.79   0.00
loop16           0.02    0.00      0.05      0.00     0.00     0.00   0.00   0.00    5.21    0.00   0.00     2.39     0.00   5.51   0.01
loop17           0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    6.20    0.00   0.00     2.35     0.00   7.00   0.00
loop18           0.01    0.00      0.10      0.00     0.00     0.00   0.00   0.00   21.18    0.00   0.00    10.20     0.00  12.59   0.01
loop19           0.00    0.00      0.09      0.00     0.00     0.00   0.00   0.00   34.71    0.00   0.00    20.31     0.00  28.71   0.01
loop20           0.00    0.00      0.01      0.00     0.00     0.00   0.00   0.00   16.88    0.00   0.00     2.83     0.00  17.37   0.01
loop21           0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00   13.16    0.00   0.00     2.42     0.00  13.89   0.00
loop22           0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00   11.10    0.00   0.00     2.35     0.00  11.80   0.00
loop23           0.02    0.00      0.05      0.00     0.00     0.00   0.00   0.00    0.03    0.00   0.00     2.30     0.00   0.33   0.00

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.25    0.00    1.25    0.00    0.00   97.49

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.25    0.00    1.76    0.00    0.00   97.99

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.50    0.00    0.50    0.00    0.00   99.00

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.25    0.00    0.25    0.00    0.00   99.50

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.25    0.00    0.25    0.00    0.00   99.50

Device            r/s     w/s     rkB/s     wkB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
sda              0.00    7.00      0.00     68.00     0.00    10.00   0.00  58.82    0.00    0.29   0.00     0.00     9.71   1.14   0.80

```
항목 | 내용
-- | --
rrqm/s | 디바이스 큐에 쌓인 초당 읽기 요청(read request) 수
wrqm/s | 디바이스 큐에 쌓인 초당 쓰기 요청(write request) 수
r/s | 디바이스에 요청한 초당 읽기 요청의 수
w/s | 디바이스에 요청한 초당 쓰기 요청의 수
rsec/s | 디바이스에서 초당 읽은 섹터의 수
wsec/s | 디바이스에서 초당 쓴 섹터의 수
avgrq-sz/s | 디바이스에 요청한 초당 평균 데이터 크기
avgqu-sz/s | 디바이스에 요청한 초당 평균 큐의 길이. 즉 디바이스의 평균 요청 개수를 의미한다.
await | 디바이스에서 처리되기 위해서 요청된 I/O의 평균시간 ms. 이 시간은 큐에서 소요된 시간과 처리 시간을 합친 것이다.
svctm | 디바이스에서 처리한 I/O의 평균 시간 ms.
%util | 디바이스에서 요청한 I/O작업을 수행하기 위해서 사용한 CPU시간의 비율. 이 값이 60%를 넘어가면 I/O성능이 매우 안 좋다고 보면 되고, 이 값이 100%에 가까우면 디바이스의 한계에 다다랐다고 보면 된다.

* CPU와 I/O에 대한 통계를 제공하는 도구. 디바이스, 파티션, NFS의 입출력 통계를 제공
* CPU 정보가 필요없다면 -d 옵션을 추가.
* 처리한 데이터의 양이 큰것도 문제가 될 수 있지만, 큐와 관련된 수치들이 높다면 문제가 많다고 보면 될것. 그리고 그 대기 시간이 얼마나 긴지는 svctm - await을 계산해 보면 쉽게 알 수 있다.

### lsof
* lsof는 list open files의 약자로 실행 중인 프로세스에서 열려 있는 파일의 목록을 제공.

### free -h
```
ubuntu@ubuntu:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:           7.7G        1.6G        3.8G         15M        2.3G        5.8G
스왑:        1.9G          0B        1.9G
```
* free는 메모리 사용량을 확인하는 명령어

### sar -n DEV 1
```
ubuntu@ubuntu:~$ sar -n DEV 1
Linux 5.4.0-87-generic (ubuntu) 	10/10/2021 	_x86_64_	(4 CPU)

01:49:01 PM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
01:49:02 PM   docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
01:49:02 PM     ens33      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
01:49:02 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

```
* sar 명령은 여러 서버의 정보들을 취합하는데 사용되며, 이와 같이 -n 옵션을 사용하면 네트워크의 상태를 확인할 수 있다.
* DEV는 모든 네트워크 디바이스의 상태를 확인하겠다는 의미로 사용되고, rxkB/s는 초당 받은 킬로바이트 크기를 뜻하고, txkB/s는 초당 보낸 킬로바이트 크기를 뜻한다.
### sar -n TCP,ETCP 1
```
ubuntu@ubuntu:~$ sar -n TCP,ETCP 1
Linux 5.4.0-87-generic (ubuntu) 	10/10/2021 	_x86_64_	(4 CPU)

01:51:18 PM  active/s passive/s    iseg/s    oseg/s
01:51:19 PM      0.00      0.00      1.00      1.00

01:51:18 PM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
01:51:19 PM      0.00      0.00      0.00      0.00      0.00

01:51:19 PM  active/s passive/s    iseg/s    oseg/s
01:51:20 PM      0.00      0.00      0.00      0.00

01:51:19 PM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
01:51:20 PM      0.00      0.00      0.00      0.00      0.00

01:51:20 PM  active/s passive/s    iseg/s    oseg/s
01:51:21 PM      0.00      0.00      0.00      0.00

01:51:20 PM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
01:51:21 PM      0.00      0.00      0.00      0.00      0.00
^C

평균값:   active/s passive/s    iseg/s    oseg/s
평균값:       0.00      0.00      0.33      0.33

평균값:   atmptf/s  estres/s retrans/s isegerr/s   orsts/s
평균값:       0.00      0.00      0.00      0.00      0.00
```
* 위에서 살펴본 명령에서 TCP와 ETCP로 변경된 것 외에는 크게 다른 부분은 없는데, 제공되는 내용은 확연히 다르다. TCP의 경우 TCP를 통한 네트워크 처리 현황을 확인할 수 있으며, ETCP는 TCP 처리 시에 발생한 에러들의 개수를 확인하는데 유용하다.
* active/s는 장비에서 외부로 초기화된 초당 TCP 연결의 개수를 뜻함. 이 값이 많다면 해당 서버에 새롭게 접근한 클라이언트 수가 많다는 의미가 된다. 또한 자바 소켓 통신 시 connect() 메서드에 해당함.
* passive/s는 외부에서 장비로 초기화된 초당 TCP연결의 개수를 뜻하며, accept() 메서드에 해당함.
* retrans/s는 초당 재전송된 세그먼트의 총개수를 뜻하는데, 일반적인 경우에는 이와 같은 재전송이 발생하지 않기 때문에 이 값이 0보다 클 경우에는 네트워크나 서버에 문제가 발생했을 확률이 높다.

### top
* top은 시스템의 전반적인 현황을 한눈에 실시간으로 볼 수 있다는 장점이 있지만, CPU를 ㅁ낳이 사용하므로 부하가 심하다는 단점도 존재함. 서버의 프로세스가 많거나 부하가 심할 때에는 부하를 가중시키는 문제를 야기할 수 있기 때문에 조심해서 사용해야한다.
