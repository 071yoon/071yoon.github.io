---
layout: post
title: 상태 패턴
subtitle: 상태 패턴을 배워보자
categories: GoF
tags: [GoF]
---

## 개요

![image](https://user-images.githubusercontent.com/66371206/199532363-edfb9a5d-b93d-4833-8c2a-c82655b23369.png)

> Finite-state_machine 참고

모든 주어진 순간에 프로그램이 속해 있을 수 있는 상태들의 수는 유한하며, 어떤 고유한 상태 내에서든 프로그램은 다르게 행동하고 한 상태에서 다른 상태로 즉시 전환될 수 있다. 현재의 상태에 따라 프로그램은 특정 다른 상태로 전환되거나 전환되지 않을 수 있으며, 이러한 전환 규칙들을 전환들(transitions)​이라고도 하고, 이러한 규칙들 또한 유한하고 미리 결정되어 있다.

![image](https://user-images.githubusercontent.com/66371206/199532122-689635dd-d965-4eda-9650-ad9e25d698ef.png)

단순한 상태 머신의 단점은 코드를 보면 바로 이해할 수 있다.

```python
class Document is
    field state: string
    // …
    method publish() is
        switch (state)
            "draft":
                state = "moderation"
                break
            "moderation":
                if (currentUser.role == "admin")
                    state = "published"
                break
            "published":
                // Do nothing.
                break
    // …
```

일반적으로 객체의 상태에 따라 적절한 행동들을 선택하는 많은 조건문​(if 또는 switch)​으로 구현되며, `Document` 클래스에 상태들과 상태에 의존하는 행동들을 추가할수록 분명해지게 된다. 그리고 프로젝트의 부피가 커질수록, transition이 발생했을 때, 메소드의 상태 조건들이 변경되어 관리가 힘들어지게 된다.

## 예제

### 조건

- 프로그램상의 1초를 현실 세계의 1시간으로 가정
- 금고는 1개가 있음
- 금고는 경비센터와 접속되어 있음
- 금고에는 비상벨과 일반통화용 전화가 접속되어 있음
- 금고에는 시계가 설치되어 있어 현재의 시간을 감시하고 있음
- 주간은 09:00~16:59, 야간은 17:00~23:59 및 0:00~8:59
- 금고는 주간에만 사용할 수 있음
- 주간에 금고를 사용하면 경비센터에 사용기록이 남음
- 야간에 금고를 사용하면 경비센터에 비상사태로 통보
- 비상벨은 언제나 사용할 수 있음
- 비상벨을 사용하면 경비센터에 비상벨 통보
- 일반통화용의 전화는 언제나 사용할 수 있음(야간은 녹음만 가능)
- 주간에 전화를 사용하면 경비센터가 호출
- 야간에 전화를 사용하면 경비센터의 자동응답기가 호출

> 예제 실행화면
> ![image](https://user-images.githubusercontent.com/66371206/199536253-e2de98e3-b074-460f-b67f-01a008980ad8.png)

<img width="585" alt="image" src="https://user-images.githubusercontent.com/66371206/199536792-04b4b454-4c44-4187-9d79-02a2726e8ca2.png">

<img width="760" alt="image" src="https://user-images.githubusercontent.com/66371206/199537669-aef4aab4-d7c6-4da4-928a-9d43fb85f9fd.png">

### State

```java
// state.java
public interface State {
    public abstract void doClock(Context context, int hour);
    public abstract void doUse(Context context);
    public abstract void doAlarm(Context context);
    public abstract void doPhone(Context context);
}
```

### DayState

```java
// DayState.java
public class DayState implements State{
    private static DayState singletone = new DayState();

    public DayState() {
    }

    public static State getInstance() {
        if(singletone == null) singletone = new DayState();
        return singletone;
    }
    @Override
    public void doClock(Context context, int hour) {
        if (hour < 9 || 17 <= hour) {
            context.changeState(NightState.getInstance());
        }
    }

    @Override
    public void doUse(Context context) {
        context.recordLog("금고사용(주간)");
    }

    @Override
    public void doAlarm(Context context) {
        context.callSecurityCenter("비상벨(주간)");
    }

    @Override
    public void doPhone(Context context) {
        context.callSecurityCenter("일반통화(주간)");
    }
    public String toString() {
        return "[주간]";
    }
}
```

### NightState

```java
// NightState.java
public class NightState implements State{
    private static NightState singletone = new NightState();

    public NightState() {
    }
    public static State getInstance() {
        if(singletone == null)singletone = new NightState();
        return singletone;
    }

    @Override
    public void doClock(Context context, int hour) {
        if (9 <= hour && hour < 17) {
            context.changeState(DayState.getInstance());
        }
    }

    @Override
    public void doUse(Context context) {
        context.recordLog("비상: 야간금고 사용!");
    }

    @Override
    public void doAlarm(Context context) {
        context.callSecurityCenter("비상벨(야간)");
    }

    @Override
    public void doPhone(Context context) {
        context.callSecurityCenter("야간통화 녹음");
    }
    public String toString() {
        return "[야간]";
    }
}
```

### Context

```java
// Context.java
public interface Context {
    public abstract void setClock(int hour);
    public abstract void changeState(State state);
    public abstract void callSecurityCenter(String msg);
    public abstract void recordLog(String msg);
}
```

### SafeFrame

```java
// SafeFrame.java
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class SafeFrame extends Frame implements ActionListener, Context {
    private final TextField textClock = new TextField(60);
    private final TextArea textScreen = new TextArea(10, 60);
    private final Button buttonUse = new Button("금고사용");
    private final Button buttonAlarm = new Button("비상벨");
    private final Button buttonPhone = new Button("일반통화");
    private final Button buttonExit = new Button("종료");

    private State state = DayState.getInstance();

    public SafeFrame(String title) throws HeadlessException {
        super(title);
        setBackground(Color.lightGray);
        setLayout(new BorderLayout());
        add(textClock, BorderLayout.NORTH);
        textClock.setEditable(false);
        add(textScreen, BorderLayout.CENTER);
        textScreen.setEditable(false);

        Panel panel = new Panel();
        panel.add(buttonUse);
        panel.add(buttonAlarm);
        panel.add(buttonPhone);
        panel.add(buttonExit);
        add(panel, BorderLayout.SOUTH);

        pack();
        show();

        buttonUse.addActionListener(this);
        buttonAlarm.addActionListener(this);
        buttonPhone.addActionListener(this);
        buttonExit.addActionListener(this);
    }

    //Event Listener Handler
    public void actionPerformed(ActionEvent event) {
        System.out.println(event.toString());
        if (event.getSource() == buttonUse) {
            state.doUse(this);
        } else if (event.getSource() == buttonAlarm) {
            state.doAlarm(this);
        } else if (event.getSource() == buttonPhone) {
            state.doPhone(this);
        } else if (event.getSource() == buttonExit) {
            System.exit(0);
        } else {
            System.out.println("?");
        }
    }

    @Override
    public void setClock(int hour) {
        String clockstring = "현재 시간은";
        if (hour < 10) {
            clockstring += "0" + hour + ":00";
        } else {
            clockstring += hour + ":00";
        }
        System.out.println(clockstring);
        textClock.setText(clockstring);
        state.doClock(this,hour);
    }
    //상태전환
    @Override
    public void changeState(State state) {
        System.out.println(this.state + "에서" + state + "로 상태가 변화했습니다.");
        this.state = state;
    }
    //경비센터의 호출
    @Override
    public void callSecurityCenter(String msg) {
        textScreen.append("call! " + msg + "\n");
    }
    //경비센터의 기록
    @Override
    public void recordLog(String msg) {
        textScreen.append("record ... " + msg + "\n");
    }
}
```

### Main

```java
// Main.java
public class Main {
    public static void main(String[] args) {
        SafeFrame frame = new SafeFrame("State Sample");
        while (true) {
            for (int hour = 0; hour < 24; hour++) {
                frame.setClock(hour);
                try{
                    Thread.sleep(1000);
                }catch(InterruptedException e){
                }
            }
        }
    }
}
```

## 등장인물

![image](https://user-images.githubusercontent.com/66371206/199541328-2a641f2e-745a-4abf-b8ff-65473c7035e4.png)

## Point

### Strategy Pattern과의 차이

이 구조는 전략 패턴과 비슷해 보이지만 한 가지 중요한 차이점이 있다. 상태 패턴에서의 특정 상태들은 서로를 인식하고 한 상태에서 다른 상태로 전환을 시작할 수 있지만 전략들은 거의 대부분 서로에 대해 알지 못한다.

### 분할정복

복잡하고 규모가 큰 문제 혹은 프로그램은 그대로 해결하려하기보단, 우선 작은 문제로 나누고 또 나눠서 하나하나의 모듈들이 쉽게 해결가능한 수준까지 나눠 해결해야 한다.
State Pattern에서는 각각의 `상태`를 각각의 클래스로 표현해 문제를 분할했다. 조건과 규모가 커질수록 조건은 많아지고 분기문은 많아진다. 하지만 이번처럼 State Pattern을 사용하면 각각의 '상태'를 클래스고 표현해 복잡한 프로그램을 분할하여 효율적으로 관리할 수 있다.

### 상태의존

SafeFrame 클래스의 setClock메소드는 Main클래스로부터 시간이 변경될 때(반복문의 순회)마다 호출되고 있다. 그리고 이 메소드는 시간을 인자값으로받아 출력 후 state.doClock(this,hour)으로 책임을 위임하고 있다. 즉, 시간의 설정을 '현재의 상태에 의존한 처리'로 취급하고 있다는 말입니다. 즉 state는 항상 같은값이 아닌 시간이 변경되서 기준을 충족할 때마다 주간과 야간 상태클래스로 변경하기 때문.
이처럼 State Pattern에서는 '상태에 의존한 처리'를 프로그램에서 표현하는방법은 다음과 같다.

- 추상 메소드로 선언후 인터페이스 명세(API)
- 구현 메소드로 구현 후 각각의 클래스로 한다.

### 상태전환의 관리

State Pattern에서 상태전환을 어디서 관리하는지에 대해서는 고려해봐야 할 부분이다.
예제에서는 Context역할의 SafeFrame 클래스에서 상태전환 메소드(changeState)를 구현했다. 그리고 이 메소드를 호출하는 부분은 ConcreteState역할인 DayState나 NightState클래스이다. 다시말하면 예제에서는 '상태전환'을 '상태에 의존한 동작'으로 간주하고 있다. 이 방법은 장점과 단점이 있다.
장점은 상태전환의 시기를 하나의 클래스 내에서 가지고 있다는 점.

> DayState 클래스가 상태전환되는 시기는 DayState 클래스 코드를 보면 된다.

단점은 하나의 ConcreteState역할이 다른 ConcreteState역할을 알아야 한다는 점입니다.

> doClock메소드 context.changeState(NightState.getInstance());

이것의 문제는 클래스 사이의 의존관계를 깊게한다는 점이다.  
그럼, 모든 상태전환을 Context 역할의 SafeFrame클래스에 맡길수도 있는데 이 경우 ConcreteState역할의 독립성이 높아지지만 Context가 모든 ConcreteState역할을 알아야하는 문제가 발생한다. 해결책으로는 Mediator Pattern을 쓰거나 혹은 State Pattern대신 상태의 테이블(표)를 사용해 설계하는 방식이 있다.

### 상태추가 is easy

State 인터페이스를 구현하는 ConcreteState 클래스를 만들어 구현하면되기에 상태추가는 어렵지 않다.
다만, 상태전환은 다른 ConcreteState 역할과 접점이 되기에 주의해야 할 필요는 있다.
그리고 완성된 State Pattern에 새로운 '상태의존의 처리'를 추가하는 것은 곤란하다. 이 말은 State 역할이 인터페이스에 메소드를 추가한다는 것을 의미하며 이를 구현하는 모든 ConcreteState역할에 구현이 필요하기 때문이다.

출처 : Java언어로 배우는 디자인 패턴 입문
