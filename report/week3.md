# 22.05.31 3차 발표
### 발표자 : 장지원
## 진행상황
* Dot Matrix
  - 우주선 - 좌우이동, 미사일 발사
  - 장애물 - 랜덤으로 여러개 생성
* Tact Switch
  - 우주선 이동, 미사일 발사

![Pic](./pic/dot_matrix.gif)

우주선과 장애물, 미사일을 각각의 배열로 만들었다. 그리고 3개의 배열을 하나의 배열로 합친 후 출력하는 함수를 작성하여 한번에 출력하도록 구현했다.

``` C
int setMatrix(char d1[], char d2[], char d3[], int d)   //d1:장애물 d2:우주선 d3:미사일
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
```

## 변경 사항
- 남은시간 표시 : 7-segment -> CLCD로 변경
  - 7-sement로 남은시간을 출력하면 깜빡임으로 인해 가독성이 매우 떨어지므로 CLCD로 변경할 예정



## 다음 발표까지 진행목표 
- CLCD
  - 남은시간, 점수 출력
- 게임진행
  - 난이도 조절

## 참고문헌


