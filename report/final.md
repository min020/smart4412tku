# 22.06.09 최종 발표
### 발표자 :  <br><br>

## 게임설명
* 플레이 방식
  - 좌우로 이동 가능한 우주선을 조작해 미사일을 발사하여 장애물 파괴
* 운영 방식
  - 우주선과 장애물 충돌시 게임 오버
  - 장애물 파괴시 점수 증가
  - 각 스테이지마다 시간이 존재
  - 해당 시간동안 생존시 다음 스테이지로 변경
<br><br>

## 개발 과정
### 2주차
#### 1) 구현한 기능
* Dot Matrix
  - 우주선 - 좌우이동
  - 장애물 - 랜덤으로 하나씩 생성
* Tact Switch
  - 우주선 이동

#### 2) 문제상황
dot matrix와 tact switch에 동시접근이 안된다.
 
#### 3) 해결방안
gettimeofday 함수를 이용해서 시간제한을 두고 접근
<!--음.. 뭔가 이런 문제가 발생해서 이렇게 해결했다 라는걸 넣고싶어서 넣어봣는데 뭐라고 적어야할지 모르겟어서.. 하하.. 이부분 빼도될거같으면 빼주세용..-->

#### 3) 핵심 코드
``` C
    gettimeofday(&dotst, NULL);
    while(b)
    {
        if (dot_d == 0)    //dot matrix에 접근하지 않은 경우만 open
        {
            dot_d = open(dot, O_RDWR);
        }
        gettimeofday(&dotend, NULL);
        write(dot_d,&c,sizeof(c));     //우주선 출력
        /***dot matrix와 tack switch를 0.2초마다 번갈아가면서 접근***/
        if((dotend.tv_usec - dotst.tv_usec > 200000) || (dotend.tv_sec>dotst.tv_sec && (dotend.tv_usec+1000000-dotst.tv_usec > 200000)))
        {
            dot_d = close(dot_d);
            if (tact == 0)     //tact switch에 접근하지 않은 경우만 open
            {
                tact = open(tact_d,O_RDWR);
            }
            gettimeofday(&tactst, NULL);
            while(1)
            {
                gettimeofday(&tactend, NULL);
                read(tact,&t,sizeof(t));     //tact switch에 0.2초간 접근해있는 동안 입력받음
                switch(t)
                {
                   
                   ```
                   
                }
                /***0.2초 경과 or tact switch 입력이 있는 경우 tact switch에 접근 해제***/
                if((tactend.tv_usec - tactst.tv_usec > 200000) || (tactend.tv_sec>tactst.tv_sec && (tactend.tv_usec+1000000-tactst.tv_usec > 200000)) || t)
                {
                    tact = close(tact);
                    break;
                }
            }
            gettimeofday(&dotst, NULL);
        }
    }
```

#### 4) 3주차 진행 목표
- 장애물이 하나씩 생성되는 문제 해결
- 장애물을 tact switch 조작으로 부수는 동작 구현
- 우주선과 블록을 dot matrix에 동시 출력
<br><br>

### 3주차
#### 1) 구현한 기능 
* Dot Matrix
  - 장애물 - 랜덤으로 여러개 생성 
  - 미사일 - 장애물과 충돌시 삭제 
  - 우주선과 장애물 동시출력 
* Tact Switch
  - 미사일 발사 

$\Rightarrow$ 2주차에 계획했던 기능 모두 구현
<!-- 아 이부분.. 애매하네요.. 완성도얘기 뭐라고적을지.. 3주차진행목표부분에 a b c해서 3주차에 구현한 기능에 a b c 중에 뭘 구현한건지 쓸까여..?
* Dot Matrix
  - 장애물 - 랜덤으로 여러개 생성 -> a
  - 미사일 - 장애물과 충돌시 삭제  -> b
  - 우주선과 장애물 동시출력 -> c
* Tact Switch
  - 미사일 발사 -> b
움.. 잘모룰겟어여..
아니면 그냥 라인 84 삭제하고 발표할때 직접 언급하는걸루할까여? 지난주 목표중 이부분을 이렇게 구현했다~ 이런식으로..?-->

#### 2) 문제상황
기존에 따로 구현했던 장애물block.c 와 우주선spaceship.c를 Dot Matrix에 동시출력 해야한다. 

#### 3) 해결방안 
우주선, 장애물 그리고 미사일 각각의 배열을 생성후 하나의 배열로 합쳐서 한번에 출력

#### 4) 핵심 코드
``` C
unsigned char blocks[8] = { 0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00 };//장애물
unsigned char spaceship[8] = { 0x00,0x00,0x00,0x00,0x00,0x00,0x08,0x1C };//우주선
unsigned char missile[8] = { 0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00 };//미사일
unsigned char matrix[8] = { 0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00 };//최종 배열 <- 이걸 dotmatrix에 출력

int setMatrix(char d1[], char d2[], char d3[], int d)   //d1:장애물 d2:우주선 d3:발사체
{
    //matrix배열에 인자로받은 두 배열 합치기
    int h;
    int b = 1;
    for (h = 0; h < 8; h++)
    {
        if((d3[h]&d1[h])>0)     //비트연산으로 충돌여부 판단
        {
            d3[h] = 0;
            d1[h] = 0;
        }
        if((d2[h]&d1[h])>0)
        {
            b = false;
            break;
        }
        matrix[h] = d1[h] + d2[h] + d3[h];
    }
    write(d, &matrix, sizeof(matrix));
    return b;   //장애물과 우주선이 충돌하면 false리턴으로 게임 종료
}
```

#### 5) 4주차 진행 목표 
- FND
  - 현재 스테이지 남은 시간 출력
- CLCD
  - 점수, 게임 진행 안내문구 출력
- 게임진행
  - 난이도 조절 : 장애물 생성주기, 장애물 이동 속도, 스테이지별 시간
<br><br>

### 4주차
#### 1) 구현한 기능
* FND
  - 현재 스테이지 남은시간 출력
* CLCD
  - 현재점수
  - 최고점수
  - 게임진행메시지 : 게임시작, 게임오버, 다시시작, 게임종료, 스테이지, 재시작
* 게임 진행
  - 난이도 조절 : 장애물 생성주기, 장애물 내려오는 속도, 스테이지별 시간

$\Rightarrow$ 3주차에 계획했던 기능 모두 구현

#### 2) 핵심 코드
- FND출력함수
``` C
void setFnd(int k) {
    int fnd_d, ten_num, one_num;
    unsigned char fnd_list[10] = {0xC0, 0xF9, 0xA4, 0xB0, 0x99, 0x92, 0x82, 0xF8, 0x80, 0x90};  //0~9표현 16진수
    unsigned char fnd_data[4] = {0xC0,0xC0,0xC0,0xC0};  //fnd에 출력할 배열
    if(k<10){  //남은시간 10의 자리와 1의 자리 구하기
        ten_num = 0;
    }
    else{
        ten_num = k / 10;
    }
    if(k<1){
        one_num = 0;
    }
    else{
        one_num = k % 10;
    }
    fnd_data[2] = fnd_list[ten_num];
    fnd_data[3] = fnd_list[one_num];
    fnd_d = open(fnd,O_RDWR);
    write(fnd_d,fnd_data,4);  //0.1초간 fnd에 남은시간 출력
    usleep(100000);
    close(fnd_d);
}
```

- CLCD출력함수
``` C
void setText(char data[]) {
    int clcd_d;
    clcd_d = open(clcd, O_RDWR);
    write(clcd_d, data, 32);  //입력받은 data 문자열 CLCD에 출력
    close(clcd_d);
}
```
  
- 게임진행을 위한 case문
``` C
case 1:
    {
        CURRENT_SCORE = 0;
        for(i=0;i<(sizeof(time_list)/sizeof(time_list[0]));i++)
        {
            init_matrix();
            result = Game(time_list[i], blocks_gen[i], blocks_sp[i]);
            if(!result){
                char text[MAX_LEN] = "  GAME OVER...  ";
                setText(text);
                usleep(5000000);
                break;
            }
            else if(result == 2){
                char text[MAX_LEN] = "   Game Quit    ";
                setText(text);
                return 0;
            }
            char text[MAX_LEN];
            sprintf(text, " STAGE %d CLEAR! ", i+1);
            setText(text);
            usleep(5000000);
        }
        if(result == 1)
        {
            char text[MAX_LEN] = "ALL STAGE CLEAR!";
            setText(text);
            return 0;
        }
        break;
    }
```
<br><br>

## 독창성
- 기존에 있던 임베디드 시스템 프로젝트중 우리가 만든 게임과 비슷한 프로젝트가 없다. 
- 장치 출력방법, gettimeofday와 같은 함수 사용법 외에는 다른 자료를 참고하지 않고 직접 구현하였다. 
<br><br>

## 실행 영상
<아마 링크를 넣을 자리>


