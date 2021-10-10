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

