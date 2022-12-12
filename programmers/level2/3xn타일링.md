# 3 x n 타일링

## **📝문제 설명**

가로 길이가 2이고 세로의 길이가 1인 직사각형 모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 3이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다

- 타일을 가로로 배치 하는 경우
- 타일을 세로로 배치 하는 경우
- 
예를들어서 n이 8인 직사각형은 다음과 같이 채울 수 있습니다.

![Imgur](https://i.imgur.com/zBW7peI.png)

직사각형의 가로의 길이 n이 매개변수로 주어질 때, 이 직사각형을 채우는 방법의 수를 return 하는 solution 함수를 완성해주세요.

### **⚠제한사항**

- 가로의 길이 n은 5,000이하의 자연수 입니다.
- 경우의 수가 많아 질 수 있으므로, 경우의 수를 1,000,000,007으로 나눈 나머지를 return해주세요.

### **입출력 예**

| n   | result |
| --- | ------ |
| 4   | 11     |

**입출력 예 설명**

*입출력 예 #1*

다음과 같이 11가지 방법이 있다.

![img](https://i.imgur.com/nnoT9kL.png) ![img](https://i.imgur.com/QTZFrTH.png) ![img](https://i.imgur.com/YE1JfJn.png) ![img](https://i.imgur.com/QhYvRTr.png) ![img](https://i.imgur.com/NKgKTIR.png) ![img](https://i.imgur.com/3uobFxe.png) ![img](https://i.imgur.com/sEK9oor.png) ![img](https://i.imgur.com/u6dpiep.png) ![img](https://i.imgur.com/re3C19N.png) ![img](https://i.imgur.com/GerdAJB.png) ![img](https://i.imgur.com/ITcbWj0.png)

## **🧐CODE REVIEW**

### **😫나의 오답 풀이**

```js
function solution(n) {
    if(n === 2) return 3;
    if(n === 4) return 11;
    if(n % 2 !== 0) return 0;
    const dp = [0, 3, 11];
    n =  n/2;
    for(let i = 3; i <= n; i++) {
        dp.push(4*dp[i-1]-dp[i-2]);
    }
    return dp[n]%1000000007;
}
```

#### **📝해설**

n개의 타일링을 한다고 했을 때, 경우의 수를 f(n)이라고 한다면, 문제의 주어진 케이스로 보면 f(2) = 3, f(4) = 11이다.

다음 f(6)의 규칙을 살펴보자.

구역을 순차적으로 나누면서 생각해보자.

`4, 2` / `2, 4` / `0, 6`

크기 6의 타일을 두 크기의 부분으로 나눠서 보도록 하자.

먼저 `4, 2`부터 생각해보면,

왼쪽의 4 크기의 타일링은 f(4)이며
오른쪽의 남은 2크기를 채운다고 한다면, f(2)이며 이 값은 3이다.

`f(4) * f(2) = 11 * 3 = 33`

이제 남은 케이스는 `2,4`와 `0,6`. 케이스 두가지가 남았다.

그러면 남은 케이스에서는 위에서 포함하지 않는 새로운 타일링을 찾아야 한다.

새로운 타일은 각각 딱 두 가지 경우 씩 나온다.

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrCzOU%2FbtqDurNi5UJ%2FAnx3Cni2eLSfpIsYtvoii1%2Fimg.png)

출처 : https://s2choco.tistory.com/24


그러면 한눈에 정리해보자

```
f(4) * f(2) = 3f(4)
f(2) * 2
2

f(6) = 3f(4) + 2f(2) + 2
```

```
f(2) = 3
f(4) = 3f(2) + 2 = 11
f(6) = 3f(4) + 2f(2) + 2

...

f(n) = 3(n-2) + 2f(n-4) + 2f(n-6) + ... + 2
```

`f(n)`의 점화식을 조금 더 축약시켜 보자

```
f(n-2) = 3(n-4) + 2f(n-6) + 2f(n-8) + ... + 2
f(n-4) = 3(n-6) + 2f(n-8) + 2f(n-10) + ... + 2

f(n-2) - f(n-4) = 3f(n-4) - f(n-6)
 => n-2 = n으로 치환
f(n) = 3f(n) - f(n-2)
```

축약시킨 f(n)의 점화식의 결론은
`f(n) = 3f(n) - f(n-2)`이다.

근데... 외않됌..?

### **🧾나의 풀이**

```js
function solution(n) {
    if(n === 2) return 3;
    if(n === 4) return 11;
    if(n % 2 !== 0) return 0;
    const dp = [0, 3, 11];
    n =  n/2;
    for(let i = 3; i <= n; i++) {
        dp.push((4*dp[i-1]%1000000007-dp[i-2]%1000000007 + 1000000007)%1000000007)
    }
    return dp[n];
}
```

#### **📝해설**

마지막에만 `1000000007`로 나눈 나머지를 계산한다면

중간 중간에 음수가 되어버리는 경우가 나온다고 한다.

그래서 각각의 경우에서 먼저 `%1000000007`해주고 `1000000007`을 더한 다음 전체에 `%1000000007`연산을 해주면 음수가 나오는 경우를 없애줄 수 있다.

*진짜 진짜 너무 어려웠던 문제...*

### **🔖정리**

1. 점화식 너무 어려움...

## 📚참고 사이트

- **🔗문제 링크**<br/>
https://school.programmers.co.kr/learn/courses/30/lessons/12902

- **[프로그래머스 - 3xn 타일링 풀이 (python3) - ellie]**<br/>
https://s2choco.tistory.com/24

- **[간단하게 점화식 원리 설명]**<br/>
https://school.programmers.co.kr/questions/35882

- **[문제 해결 팁]**<br/>
https://school.programmers.co.kr/questions/32019
