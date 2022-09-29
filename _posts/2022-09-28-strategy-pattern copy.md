---
layout: post
title: 전략 패턴
subtitle: 자바로 전략패턴 학습하기
categories: GoF
tags: [GoF, TIL]
---

## 전략이란?

1. 적을 부수기 위한 작전
2. 군대를 움직일 때의 방책
3. 문제를 해결하기 위한 방법

등 이라 볼 수 있다.

> 즉 알고리즘을 빈틈없이 교체하여 같은 문제를 다른 방법으로 쉽게 해결할 수 있게 도와주는 패턴이다!

## 등장인물

![StrategyPatternTemplate drawio](https://user-images.githubusercontent.com/66371206/192945590-e33c0b30-da51-4759-a4b6-68b499b65783.png)

### Strategy

Strategy는 전략을 이용하기 위한 API를 결정한다.
-> Strategy Interface로 이용

### Concrete Strategy

구체적인 API를 구현한다.
-> Winning Strategy, ProbStrategy로 구현

### Context

Strategy를 이용한다.
-> Player로 구현

## 예제 프로그램

![StrategyPatternExample drawio](https://user-images.githubusercontent.com/66371206/192945589-86dea94f-74a8-42db-9295-06512f4500bd.png)

### 예제 설명

Strategy Pattern을 활용한 `가위바위보` 프로그램을 제작해보자!

현재 프로그램에서는 두가지 전략이 존재한다.

1. 이기면 다음에 같은 손을 내밀고, 졌다면 랜덤한 손을 내는 간단한 전략
2. 직전에 냈던 손과 다음에 낼 손을 확률적으로 계산하여 높은 확률의 손을 내는 전략

직접 구현을 하며, Strategy Pattern 이 어떤식으로 구현되어 있는지 알아보자

### Hand Class

Hand는 Player의 손을 표시하는 클래스로 존재한다. 내부적으로 [주먹, 가위, 보] 를 [0, 1, 2] 로 저장하고 있으며, Player는 필요할 때 언제든 같은 객체를 반복하여 사용할 수 있다.

> 싱글톤을 잘 활용한 예시

주요 함수로는, 가위바위보를 진행 후에 `isStrongerThan`으로 승패를 판단할 수 있다.

```java

public class Hand{
  public static final int ROCK = 0;
  public static final int SCIS = 1;
  public static final int PAPE = 2;
  public static final Hand[] hand = {
    new Hand(ROCK);
    new Hand(SICS);
    new Hand(PAPE);
  };

  private static final String[] name = {
    'Rock', 'Scissors', 'Paper'
  };

  private int handValue;
  private Hand(int handValue) { // constructor
    this.handValue = handValue;
  }

  public static Hand getHand(int HandValue) { //get instance from value
    return hand[handValue];
  }

  public boolean isStrongerThan(Hand h) { // return true when win
    return fight(h) == 1;
  }

  public boolean isWeakerThan(Hand h) { //return true when lose
    return fight(h) == -1;
  }

  private int fight(Hand h) { //private fight method
    if (this == h) { // draw
      return 0;
    } else if ((this.handValue + 1) % 3 == h.handValue) { // win
      return 1;
    } else { // lose
      return -1;
    }
  }

  public String toString() {
    return name[handValue];
  }
}

```

### Strategy Interface

전략을 위한 추상 메소드의 집합이다.
nextHand는 다음에 내는 손을 얻기 위한 메소드이며, 이 메소드가 호출되면, 다음에 낼 손을 리턴하게 된다.
Study는 직전에 내가 낸 손으로 이겼는지 졌는지, 그리고 그에 해당하는 학습을 위한 메소드로 사용된다. 이겼다면 `study(true)`, 졌다면 `study(false)`로 호출된다.

```java
public interface Strategy {
	public abstract Hand nextHand();
	public abstract void study(boolean win);
}
```

### WinningStrategy Class

단순한 Strategy 를 나타낸다.

간단하게, 직전 승부에서 이겼다면, 이긴 손을 계속내고, 만약 졌다면 새로운 랜덤한 손을 생성해서 제공한다.

```java
public class WinningStrategy implements Strategy {
	private Random random;
	private boolean won = false;
	private Hand prevHand;

	public WinningStrategy(int seed) { // init Random from seed
		random = new Random(seed);
	}

	@Override
	public Hand nextHand() {
		if(!won){ // lose -> next hand will be random
			prevHand = Hand.getHand(random.nextInt(3));
		}
		return prevHand; // win -> next hand is same
	}

	@Override
	public void study(boolean win) { // check if last game is won
		won = win;
	}
}
```

### ProbStrategy Class

가위바위보의 복잡한 전략
간단하게 설명을 하자면
`history[이전에 낸 손][이번에 낼 손]` 에 해당하는 데이터들을 계속 쌓아, 이번에 낼 손 중에 가장 이겼던 확률이 높았던 것을 내게 결정한다.

예를들어 내가 이때까지 가위를 내고 다음에 주먹을 내서 이겼던 상황이 10번 있었다면 `history[1][2] = 10`이 된다.

즉 내가 저번에 가위를 냈고 다음에 주먹, 가위, 보를 냈을때의 승률 즉 `history[1][0]` `history[1][1]` `history[1][2]`의 비율이 `1:3:5` 라면 주먹을 낼 확률이 1/9, 가위를 낼 확률이 3/9, 보를 낼 확률이 5/9. 이런식으로 정의를 하였다

```java
public class ProbStrategy implements Strategy {
	private Random random;
	private int prevHandValue = 0;
	private int currentHandValue = 0;
	private int[][] history = {
			{1,1,1,},
			{1,1,1,},
			{1,1,1,},
	};

	public ProbStrategy(int seed) { // init Random from seed
		random = new Random(seed);
	}

	@Override
	public Hand nextHand() { // next hand algorithm logic
		int bet = random.nextInt(getSum(currentHandValue));
		int handValue = 0;
		if(bet < history[currentHandValue][0]){
			handValue = 0;
		}else if(bet < history[currentHandValue][0]+history[currentHandValue][1]){
			handValue = 1;
		}else{
			handValue = 2;
		}
		prevHandValue = currentHandValue;
		currentHandValue = handValue;
		return Hand.getHand(handValue);
	}

	private int getSum(int hv) { // help next hand algorithm logic
		int sum = 0;
		for(int i = 0;i<3;i++){
			sum += history[hv][i];
		}
		return sum;
	}

	@Override
	public void study(boolean win) { // stack data for algorithm
		if(win){
			history[prevHandValue][currentHandValue]++;
		}else{
			history[prevHandValue][(currentHandValue+1)%3]++;
			history[prevHandValue][(currentHandValue+2)%3]++;
		}
	}
}

```

### Player Class

실제 가위바위보를 하는 사람을 표현할 클래스이다.
Player는 `이름` 과 `전략`이 할당되어 인스턴스를 만들게 된다.
`nextHand`를 호출하면, 전략에 알맞는 다음 손을 유추하여 리턴 받고 다음 손으로 지정하게 된다. 즉 다음에 자신이 해야 할 행동을 Strategy에 맡기고 `위임`을 하고 있다.

```java
public class Player {
	private String name;
	private Strategy strategy;
	private int wincount;
	private int losecount;
	private int gamecount;

	public Player(String name,Strategy strategy){
		this.name = name;
		this.strategy = strategy;
	}

	public Hand nextHand() { // get command from strategy
		return strategy.nextHand();
	}

	public void win(){
		strategy.study(true);
		wincount++;
		gamecount++;
	}

	public void lose(){
		strategy.study(false);
		losecount++;
		gamecount++;
	}

	public void even(){
		gamecount++;
	}

	public String toString(){
		return "["+name+":"+gamecount+"games,"+wincount+"win,"+losecount+"lose"+"]";
	}
}

```

### Main Class

실제로 Main 함수는 다음과 같이 흘러간다.

```java
public class Main {

	public static void main(String[] args) {
		if(args.length!=2){
			System.out.println("Usage:java Main randomseed1 randomseed2");
			System.out.println("Example:java Main 314 15");
			System.exit(0);
		}

		int seed1 = Integer.parseInt(args[0]); // init with seed
		int seed2 = Integer.parseInt(args[1]);

		Player player1 = new Player("HanRyang",new WinningStrategy(seed1));
		Player player2 = new Player("Halang",new WinningStrategy(seed2));

		for (int i = 0; i<1000;i++){
			Hand nextHand1 = player1.nextHand();
			Hand nextHand2 = player2.nextHand();

			if(nextHand1.isStrongThan(nextHand2)) {
				System.out.println("Winner:"+player1);
				player1.win();
				player2.lose();
			} else if(nextHand1.isWeakerThan(nextHand2)) {
				System.out.println("Winner:"+player2);
				player2.win();
				player1.lose();
			} else {
				System.out.println("Even...");
				player1.even();
				player2.even();
			}
		}

		System.out.println("Total result:");
		System.out.println(player1.toString());
		System.out.println(player2.toString());
	}
}
```

## 왜 사용할까?

일반적으로 메소드 내부에 동화된 형태로 알고리즘을 구현하지만 Strategy Pattern은 알고리즘을 다른 부분을 분리하여 API 즉 알고리즘의 인터페이스만 규정하고있다.

만약 알고리즘을 개량하고 싶은 상황이 온다면, Strategy Pattern의 인터페이스는 유지한 채로, Concrete Strategy만 변경하면 된다. 혹은 새로운 알고리즘을 사용하고 싶다면, 새로운 Concrete Strategy를 만든 후 바꾸기만 하면 된다. 즉 위임이라는 `느슨한 연결`을 제공함으로써 알고리즘을 용이하게 교환할 수 있게 된다.

혹은 원래 알고리즘과 속도 비교등도 간단하게 교체를 통하여 진행 할 수 있다.

## TIP

Strategy 패턴을 활용하여, 동작 중에도 Concrete Strategy 역할의 클래스를 교체 할 수도 있다.

> Ex. 메모리가 적은 환경에서는 속도가 느리지만 메모리를 절약하는 전략, 메모리가 많을 때에서는 속도가 빠르지만 메모리를 많이 사용하는 전략을 선택 할 수 있다.

Strategy 가 알고리즘의 확장성 즉 OCP를 만족시키며, Context가 알고리즘의 메소드를 모르고도 이용할 수 있게 하며 알고리즘의 복잡도를낮춰준다.
