---
layout: post
title: 몬티 홀 문제
subtitle: 몬티 홀 문제를 분석해서 시뮬레이션 해보자
categories: deep-thought
tags: [deep-thought, TIL]
---

## 몬티 홀 문제

---

### 문제 분석

몬티 홀 문제는 영화 `21`에서 처음 공개된 아이디어이다. 잠깐 문제를 보고 가자면

> Suppose you’re on a game show, and you’re given the choice of three doors. Behind one door is a car, behind the others, goats. You pick a door, say #1, and the host, who knows what’s behind the doors, opens another door, say #3, which has a goat. He says to you, "Do you want to pick door #2?" Is it to your advantage to switch your choice of doors?

> 당신이 한 게임 쇼에 참여하여 세 문들 중 하나를 고를 기회를 가졌다고 생각해봐라. 한 문 뒤에는 자동차가 있으며, 다른 두 문 뒤에는 염소가 있다. 당신은 1번 문을 고르고, 문 뒤에 무엇이 있는지 아는 사회자는 염소가 있는 3번 문을 연다. 그는 당신에게 "2번 문을 고르고 싶습니까?"라고 묻는다. 당신의 선택을 바꾸는 것은 이득이 되는가?

즉 간단히 말하면

1. 문 3개가 있는데 한 문 뒤에는 자동차가 있고 나머지 두 문 뒤에는 염소가 있다. 참가자는 이 상황에서 문을 하나 선택하여 그 뒤에 있는 상품을 얻는다.
2. 참가자가 어떤 문을 선택하면 사회자는 나머지 두 문 중에 염소가 있는 문 한 개를 열어 참가자에게 그 문에 염소가 있다고 확인시켜준다.
3. 그 후 사회자는 참가자에게 선택한 문을 닫혀있는 다른 문으로 선택을 바꿀 기회를 준다.

얼핏보기에는 정말 간단해 보이는 문제고 나도 처음 문제를 접했을 때는 엥?? 이게 왜 이렇게 핫한 주제일까 라는 생각까지 했다. 하지만 여기서 중요한 점은 `사회자는 어디에 스포츠카가 있는지 알고있다` 라는것이 조건부 확률의 전제로 들어간다는 것이다. 정말 단순해 보이는 문제인데, 어째서 이게 많은 수학자들에게 항의 메일이 오도록 했을까??

> IQ 200 인 사람도 처음에 틀리고, 노벨상 수상자 까지 문제가 잘못됐다고 메일을 보냈다고 한다...!!

### My Thoughts

---

우선 정답은 바꾸었을 때의 확률이 2/3 이고, 바꾸지 않았을 때의 확률은 1/3이므로, 당연히 바꾸는 것이 확률적으로 맞다!!

이제, 나의 처음 접근을 한번 살펴보자. 아마 많은 사람들일 나처럼 생각했겠지만, 당연히 50프로라고 생각을 했다. 그리고 이건 부끄러운 생각이 절대 아니다. 직관적으로 생각을 해보면 그렇기 때문이다. 내가 우선 선택을 하였고, 사회자가 둘중 하나를 오픈을 해 주었다. 그 시점을 독립사건으로 본다고 하면, 나는 둘중 하나를 개봉하는것이기 때문에, 확률은 마치 반반처럼 보이지만, 그렇게 단순한 문제가 아니다. 왜냐하면 확률적으로 접근을 해야하기 때문이다.

만약 처음에 내가 선택을 하지 않고, 사회자가 둘중 하나를 열어 준 다음 내가 선택을 하면 독립사건이 되어 50% 확률이 되는것이 수학적으롤도 맞다. 하지만, 내가 처음에 선택을 하였기 때문에, 바꾸지 않고 우승할 확률은 무조건 1/3 확률이고, 바꾸었을 때의 확률은 2/3이 된다. 여기서 중요한점은

1. 사회자는 어디에 자동차에 있는지 이미 알고있다
2. 내가 처음 선택을 하였다

이렇게 두가지가 전제조건으로 나와있어야 조건부 확률로 성립을 한다는 것이다. 만약 사회자가 둘중 하나에 에라이~! 하고 아무거나 열어버린다면 자동차가 나올 수가 있고, 내가 처음 선택을 하지않았다면, 사회자가 어떤 문을 열어야 할 지 선택 할 지 모르기 때문에 전제가 틀려버린다. 아마 많은 사람들이 헷갈린 부분이 이러한 전제조건의 여부가 정확하게 확립되지 않아 헷갈린 것 같다. 하지만 이런식으로 이야기를 하여도 직관적으로 보기엔 정말 어렵다. 그러때는 역시 묻지마 코딩이 정답이다. C++ 로 코딩을 하면서 차근차근 왜 이러하게 나오지는 살펴보자

### 몬티 홀 문제 시뮬레이션 via C++

```C++
#include <iostream>
#include <cstdlib>
#include <ctime>
using namespace std;

int main(){
	srand(time(NULL));

	int monty_sum = 0;
	int dont_change_sum = 0;
	int test_case = 0;
	char simul_on;
	cout << "simulation counts : ";
	cin >> test_case;
	cout << "check every simulations? (y/n) ";
	cin >> simul_on;
	for(int i = 0; i < test_case; i++){
		int chosen_door = rand() % 3;
		int correct_door = rand() % 3;
		if (simul_on == 'y')
			cout << "TEST CASE " << i + 1 << " Car is in " << (char)(correct_door + 'A') << " and my choice is " << (char)(chosen_door + 'A') << "\n";
		if (correct_door == chosen_door){
			if (simul_on == 'y')
				cout << "Monty-Hall X and got correct\n";
			dont_change_sum += 1;
		}
		else{
			if (simul_on == 'y')
				cout << "Monty-Hall X and got wrong\n";
		}
		if (correct_door == chosen_door){ // When you choose the coorect one at the first time -> you will never be able to win
			if (simul_on == 'y')
				cout << "Monty-Hall and got wrong\n";
			continue;
		}
		else if (correct_door != chosen_door){ // When you choose the wrong one at the first time -> you will get it for sure when you change
			if (simul_on == 'y')
				cout << "Monty-Hall and got right\n";
			monty_sum += 1;
		}
		// Since there is 1/3 possibility to win at the first time, possibility to get the prize is 2/3
	}
	cout << "\nmonty possibility is : " << ((double)monty_sum / (double)test_case) * (double)100 << "%" << "\ndon't change possibility is : " << ((double)dont_change_sum / (double)test_case) * (double)100 << "%" << "\n";
}
```

최대한 간략하면서, 직관적으로 보이게 짜보려고 했는데, 잘 됐을 지 모르겠다.
테스트를 해본 결과 대략 10만번을 돌려보면, 66.95% 와 33.05% 가 나와 거의 2/3 과 1/3에 수렴하는것을 볼 수 있다.

```shell
simulation counts : 100000
check every simulations? (y/n) n

monty possibility is : 66.95%
don't change possibility is : 33.05%
```

여기서 시뮬레이션에 `y` 를 주면 실제 테스트케이스별로 확인도 할 수 있다.

코드를 한번 설계를 해보면 왜 그런지 확실하게 알 수 있는데 확실하게 정의를 할 수 있다. 내가 몬티 홀을 믿고 무조건 바꾼다고 가정을 해보자. 그럼 당연하게 두가지 상황이 나온다.

1. 내가 맞음
2. 내가 틀림

자동차를 개봉하기 전 확률상으로 보면 내가 맞을 확률은 1/3, 그리고 내가 틀릴 확률은 2/3 이다.

여기서 만약 바꾸지 않고 똥고집을 부려 유지를 한다면, 그대로 1/3 확률로 나올 것이다. 하지만 바꾼다면 내가 처음에 틀린 경우 즉 2번으로 시작해서 무조건 맞으므로 맞을 확률이 2/3이 된다.

이제 그럼 매우 간단한 로직으로 설명을 들 수 있다. 내가 몬티홀을 무조건 믿고 바꾼다고 생각을 해보자

1. 내가 처음에 맞으면 무조건 틀림
2. 내가 처음에 틀리면 무조건 맞음

이렇게 설명 할 수 있다. 왜냐면 내가 바꿈으로써 나는 2개의 문을 동시에 확인하는 셈이 되어 버리기 때문이다. 그래서 처음에 맞을 확률 1/3, 처음에 틀릴 확률이 2/3 이므로 시뮬레이션을 돌려도 동일하게 나오는 것이다.

사람의 말로만 들으면 충분히 헷갈릴 수 있다. 컴퓨터 공학도라면 직접 시뮬레이션을 돌려보며 생각을 해보는 것이 이해에 더 도움이 될거라 생각한다.

### 후기

나무위키나 블로그들을 보면, 정말 많은 사람들이 다양한 이유로 다양한 설명들을 해놓은 것을 볼 수 있다. 그 중에, 수학적으로 접근을 하여 베이즈의 확률론으로 정리를 해놓은 사람, 혹은 경우의 수로 정리 한 사람, 문이 100개라고 가정을 하고 생각을 해보라는 사람.

> 잘 생각해보면 확률론으로 정리해놓은 것 빼고는 다 증명을 할 수 없는 말들이다

하지만 이렇게 똑똑이들이 해놓은 증명을 보고도 이해가 잘 안된다면, 무작정 시뮬레이션을 해보고 코딩을 하며 문제를 더 완벽히 파악하고 큰 문제를 짧게 쪼개며 단순화 하는것이 더 도움이 될 수 있다는 생각이 든다.
