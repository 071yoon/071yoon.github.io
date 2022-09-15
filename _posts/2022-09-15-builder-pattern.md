---
layout: post
title: 빌더 패턴
subtitle: 자바로 빌더패턴 학습하기
categories: GoF
tags: [GoF, TIL]
---

## Java언어로 배우는 디자인 패턴 입문

### 생성 패턴 Creational Pattern

생성 패턴에서는 중요한 이슈가 두 가지가 존재한다.

- 생성 패턴은 시스템이 어떤 Concrete Class를 사용하는지에 대한 정보를 캡슐화한다.
- 생성 패턴은 이들 클래스의 인스턴스들이 어떻게 만들고 어떻게 결합하는지에 대한 부분을 완전히 가려준다.

> 생성 패턴을 이용하면 무엇이 생성되고, 누가 이것을 생성하며, 이것이 어떻게 생성되는지, 언제 생성할 것인지 결정하는 데 유연성을 확보할 수 있다.

추후에 어떤식으로 구현할 수 있는지 알아보자.

### 빌더 패턴의 등장인물

![Director 1](https://user-images.githubusercontent.com/66371206/190228103-b8b180e3-1fb3-4700-872c-5f263b0b6284.jpg)

- Builder
  - Instance 생성을 위한 API를 결정
  - 인스턴스의 각 부분을 만들기 위한 메소드 존재
  - Builder Class
- ConcreteBuilder
  - Build 역할의 API를 실제로 구현
  - 인스턴스 작성으로 호출되는 메소드가 정의
  - 최종적인 결과물을 얻기위한 메소드가 준비
  - TextBuilder와 HTMLBuilder
- Director
  - Builder 역할의 API를 사용하여 인스턴스 생성
  - ConcreteBuilder에 의존하지 않음
  - ConcreteBuilder에 상관하지 않고, Builder 역할의 메소드만 사용
  - Director Class
- Client
  - Builder패턴을 이용
  - Main Class

![Director 2](https://user-images.githubusercontent.com/66371206/190228118-4e62902f-cd49-40ad-b139-f66ac0eef2c0.jpg)

### 빌더 패턴 예시

`문서`를 작성하는 프로그램

#### 구조

- 타이틀을 한 개 포함
- 문자열을 몇 개 포함
- 개별항목을 몇 개 포함
- TextBuilder : Plain Text를 사용하여 문서를 생성
- HTMLBuilder : HTML을 사용하여 문서를 생성

![Director 3](https://user-images.githubusercontent.com/66371206/190228122-f6accbb8-f15b-4575-b69a-0330d2853373.jpg)

#### Builder Class

> `문서`를 만들 메소드들을 선언하고 있는 추상클래스

makeTitle, makeString, makeItems는 각각 타이틀, 문자열, 개별 항목을 문서 안에 구축하는 메소드

```java
public abstract class Builder {
	public abstract void makeTitle(String title);
	public abstract void makeString(String str);
	public abstract void makeItems(String[] items);
	public abstract void close();
}
```

#### Director Class

> Builder 클래스로 선언되어 있는 메소드를 사용하여 문서를 생성

Director 클래스의 생성자는 Builder형이지만, 실제로 Builder 클래스의 인스턴스가 주어지지는 않는다.

> Builder 클래스는 추상클래스이므로, 인스턴스 생성이 불가

Director 클래스에서 실제로 전달되는것은 Builder 클래스의 하위 클래스의 인스턴스이다. Builder 클래스의 하위 클래스 종류에 따라 Director 클래스가 만들 문서의 형식이 정해진다.

```java
public class Director {
	private Builder builder;
	public Director(Builder builder){ // Builder의 하위 클래스의 인스턴스이므로
		this.builder = builder; // builder필드에 저장
	}

	public void construct() {
		builder.makeTitle('Greeting');
		builder.makeString('아침과 낮에');
		builder.makeItems(new String[]{
			'좋은 아침입니다.',
			'안녕하세요.',
		});
		builder.makeString('밤에');
		builder.makeItems(new String[]{
			'안녕하세요.',
			'안녕히 주무세요.',
			'안녕히 계세요.',
		});
		builder.close(); //문서를 완성
	}
}
```

#### TextBuilder Class

> Builder Class의 하위 Class

일반 텍스트를 사용하여 문서를 구축하고, String으로 봔한

```java
public class TextBuilder extends Builder {
	private StringBuffer buffer = new StringBuffer();
	public void makeTitle(String title){
		buffer.append('======================\n');
		buffer.append('[' + 'title' + ']\n');
		buffer.append('\n');
	}

	public void makeString(String str){
		buffer.append('ㅁ' + str + 'ㅁ\n');
		buffer.append('\n');
	}

	public void makeItems(String[] items){
		for(int i = 0; i < items.length; i++){
			buffer.append(' -' + items[i] + '\n');
		}
		buffer.append('\n');
	}

	public void close(){
		buffer.append('======================\n');
	}

	public String getResult(){
		return buffer.toString();
	}
}
```

#### HTMLBuilder Class

> Builder Class의 하위 Class

HTML 파일로 문서를 구축

```java
public class HTMLBuilder extends Builder{
	private String filename;
	private PrintWriter writer;

	public void makeTitle(String title){
		filename = title + '.html';
		try {
			writer = new PrintWrite(new FileWriter(filename));
		} catch (IOException e) {
			e.printStackTrace();
		}
		writer.println("<html><head><title>" + title + "</title></head><body>");
		writer.println("<h1>" + title + "</h1>");
	}

	public void makeString(String str){
		writer.println("<p>" + str + "</p>");
	}

	public void makeItems(String[] items){
		writer.println("<ul>");
		for(int i = 0; i < items.length; i++){
			writer.println("<li>" + items[i] + "</li>");
		}
		writer.println("</ul>");
	}

	public void close(){
		writer.println("</body></html>");
		writer.close();
	}

	public String getResult(){
		return filename;
	}
}
```

#### Main Class

> 실제로 실행하는 Builder 패턴의 테스트 프로그램

TextBuilder와 HTMLBuilder는 Builder의 하위클래스이며, Director는 Builder의 메소드만을 사용하여 문서를 작성한다. Builder의 메소드만을 사용한다는 뜻은 Director는 실제로 동작하는것이 TextBuilder인지, HTMLBuilder인지 알 수 없다는 뜻. 즉 Builder는 문서를 구축하기 위해, 필요 충분한 메소드군을 선언할 필요가 있다.

```java
public class Main{
	public static void main(String[] args){
		if(args.length != 1){
			usage();
			System.exit(0);
		}
		if (args[0].equals('plain')){
			TextBuilder textbuilder = new TextBuilder();
			Director director = new Director(textbuilder);
			director.construct();
			String result = textbuilder.getResult();
			System.out.println(result);
		} else if (args[0].equals('html')) {
			HTMLBuilder htmlbuilder = new HTMLBuilder();
			Director director = new Director(htmlbuilder);
			director.construct();
			String filename = htmlbuilder.getResult();
			System.out.println(filename + '가 생성되었습니다.');
		} else {
			usage();
			System.exit(0);
		}
	}

	public static void usage() {
		System.out.println('Usage: java Main plain 일반 텍스트로 문서작성');
		System.out.println('Usage: java Main html HTML 파일로 문서작성');
	}
}
```

> 일반 텍스트 실행결과
> <img src="https://user-images.githubusercontent.com/66371206/190231034-16e7b3c7-9880-45b8-8b40-6d4914133567.png" width="400px">

> HTML 파일 실행결과
> <img src="https://user-images.githubusercontent.com/66371206/190230554-99d4ada5-afa7-4781-9fad-7d2b7b5362a7.png" width="400px">

> HTMLBuidler가 작성한 Greeting.html의 브라우저 결과
> <img src="https://user-images.githubusercontent.com/66371206/190230484-11188473-993c-415e-a887-f311cf88f5b8.png" width="400px">

### 누가 무엇을?

- Main Class는 Builder Class의 메소드를 모른다 (호출하지 않음)
- Main Class는 Director Class의 construct만 호출한다
- Director Class는 Builder Class를 알고있다
- Director Class는 실제로 이용하고 있는 클래스가 무엇인지 알 수 없다
  - HTMLBuilder인지 TextBuilder인지 알 수 없음
  - Director Class는 Builder Class의 메소드만 사용
  - Builder Class의 하위 클래스는 메소드를 구현
- Director Class가 Builder Class의 하위 클래스를 모르기 때문에, 교체할 수 있다
  - 모르기 때문에 교환이 가능하며, 교체가 가능하기 때문에 부품의 가치가 높다

## 더 알아보기

> 엥 이거 완전 팩토리 패턴이랑 비슷한거 아니야?

### 여행 예제로 알아보기

- 클라이언트 프로그램으로부터 팩토리 클래스로 많은 파라미터를 넘겨줄 때 타입, 순서 등에 대한 관리가 어려워져 에러가 발생할 확률이 높아진다.

  ```java
  new TourPlan("여행 계획", LocalDate.of(2021,12, 24), 3, 4, "호텔", Collections.singletonList(new DetailPlan(1, "체크인")));
  ```

- 경우에 따라 필요 없는 파라미터들에 대해서 팩토리 클래스에 일일이 null 값을 넘겨줘야 한다.

  ```java
  new TourPlan("여행 계획", LocalDate.of(2021,12, 24), null, null, null, Collections.singletonList(new DetailPlan(1, "놀고 돌아오기")));
  ```

- 생성해야 하는 sub class가 무거워지고 복잡해짐에 따라 팩토리 클래스 또한 복잡해진다.

#### Builder Class

```java
public interface TourPlanBuilder {
    TourPlanBuilder nightsAndDays(int nights, int days);
    TourPlanBuilder title(String title);
    TourPlanBuilder startDate(LocalDate localDate);
    TourPlanBuilder whereToStay(String whereToStay);
    TourPlanBuilder addPlan(int day, String plan);
    TourPlan getPlan();
}
```

#### Concrete Builder

```java
public class DefaultTourBuilder implements TourPlanBuilder {
    private String title;
    private int nights;
    private int days;
    private LocalDate startDate;
    private String whereToStay;
    private List<DetailPlan> plans;

    @Override
    public TourPlanBuilder nightsAndDays(int nights, int days) {
        this.nights = nights;
        this.days = days;
        return this;
	}
    @Override
    public TourPlanBuilder title(String title) {
        this.title = title;
        return this;
    }
    @Override
    public TourPlanBuilder startDate(LocalDate startDate) {
        this.startDate = startDate;
        return this;
    }
    @Override
    public TourPlanBuilder whereToStay(String whereToStay) {
        this.whereToStay = whereToStay;
        return this;
    }
    @Override
    public TourPlanBuilder addPlan(int day, String plan) {
        if (this.plans == null) {
            this.plans = new ArrayList<>();
        }

        this.plans.add(new DetailPlan(day, plan));
        return this;
    }
    @Override
    public TourPlan getPlan() {
        return new TourPlan(title, startDate, days, nights, whereToStay, plans);
    }
}
```

#### Director Class

```java
public class TourDirector {
    private TourPlanBuilder tourPlanBuilder;

    public TourDirector(TourPlanBuilder tourPlanBuilder) {
        this.tourPlanBuilder = tourPlanBuilder;
    }

    public TourPlan cancunTrip() {
        return tourPlanBuilder.title("칸쿤 여행")
                .nightsAndDays(2, 3)
                .startDate(LocalDate.of(2020, 12, 9))
                .whereToStay("리조트")
                .addPlan(0, "체크인하고 짐 풀기")
                .addPlan(0, "저녁 식사")
                .getPlan();
    }

    public TourPlan longBeachTrip() {
        return tourPlanBuilder.title("롱비치")
                .startDate(LocalDate.of(2021, 7, 15))
                .getPlan();
    }
}
```

#### Main Class

```java
public static void main(String[] args) {
	DefaultTourBuilder defaultbuilder = new DefaultTourBuilder();
    TourDirector director = new TourDirector(defaultbuilder);
    TourPlan tourPlan = director.cancunTrip();
}
```

> setter가 없으므로 객체 일관성을 유지하여 불변 객체로 생성할 수 있다.

### When not to use

> 객체를 생성하는 대부분의 경우에는 빌더 패턴을 적용하는 것이 좋다.

예외적으로 2가지 상황에서는 빌더를 구현해야될지 고려하면 좋다.

1. 객체의 생성을 라이브러리로 위임하는 경우

   - 엔티티(Entity) 객체나 도메인(Domain) 객체로부터 DTO를 생성하는 경우라면 직접 빌더를 만들고 하는 작업이 번거로우므로 MapStruct나 Model Mapper와 같은 라이브러리를 통해 생성을 위임할 수 있다.

2. 변수의 개수가 2개 이하이며, 변경 가능성이 없는 경우
   - 또한 변수가 늘어날 가능성이 거의 없으며, 변수의 개수가 2개 이하인 경우에는 정적 팩토리 메소드를 사용하는 것이 더 좋을 수도 있다.
   - 빌더의 남용은 오히려 코드를 비대하게 만들 수 있으므로  변수의 개수와 변경 가능성 등을 중점적으로 보고 빌더 패턴을 적용할지 판단하면 된다.

### 참고

[참고링크1](https://readystory.tistory.com/121)
[참고링크2](https://dev-youngjun.tistory.com/197)
[참고링크3](https://mangkyu.tistory.com/163)
