# 22.05.24
### 발표자 : 김민규
## 진행상황
tact switch를 이용한 우주선 이동과 블록의 랜덤 생성까지 구현하였다.



dot matrix와 tact switch에 동시접근이 안되는 부분에서 어려움을 겪었지만 gettimeofday 함수를 이용해서 시간제한을 두는 방식으로 해결하였다. 

``` C
gettimeofday(&dotend, NULL);
write(dot_d,&c,sizeof(c));     //우주선 출력

//dot matrix와 tack switch를 0.2초마다 번갈아가면서 접근
if((dotend.tv_usec - dotst.tv_usec > 200000) || (dotend.tv_sec>dotst.tv_sec && (dotend.tv_usec+1000000-dotst.tv_usec > 200000)))
{
  ···
  
    gettimeofday(&tactend, NULL);
    read(tact,&t,sizeof(t));     //tact switch에 0.2초간 접근해있는 동안 입력받음
  
  ···
    
    //0.2초 경과 or tact switch 입력이 있는 경우 tact switch에 접근 해제
    if((tactend.tv_usec - tactst.tv_usec > 200000) || (tactend.tv_sec>tactst.tv_sec && (tactend.tv_usec+1000000-tactst.tv_usec > 200000)) || t)
    {
        tact = close(tact);
        break;
     }
}
gettimeofday(&dotst, NULL);
}
```

## 다음 발표까지 진행목표 
 - 스레드를 이용한 병렬처리 - 블록 움직임을 시간제한 방식으로 동작시키다보니 블록이 하나씩 밖에 생성되지 않는 문제를 해결하기 위함

- 우주선과 블록을 dot matrix에 동시에 구현

## 참고문헌
[dot matrix 조작 참고문헌](https://comonyo.tistory.com/16)

[tact switch 조작 참고문헌](https://hongci.tistory.com/85)

[gettimeofday 함수 참고문헌](https://bywords.tistory.com/entry/CLinux-gettimeofday%EB%A1%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%B4%88-%EB%8B%A8%EC%9C%84-%EC%B8%A1%EC%A0%95%ED%95%98%EA%B8%B0)


