## 왜 중개자 패턴?

> 객체 간의 혼란스러운 의존 관계들을 줄일 수 있는 행동 디자인 패턴

**즉 중개자 패턴은 객체 간의 직접 통신을 제한하고 중재자 객체를 통해서만 협력할 수 있게 설정**

간단한 로그인 Form을 만든다고 구현을 해보자

<img width="279" alt="image" src="https://user-images.githubusercontent.com/66371206/198242069-ca392cd8-0124-4ab5-ad5a-b3162d6266e3.png">

<img width="278" alt="image" src="https://user-images.githubusercontent.com/66371206/198242099-dccf424c-c2c7-4d75-9821-1cd588e21031.png">

Guest를 누르면 Username과 Password가 막히고, Login을 누르면 Username이 활성화 되고, Username에 입력이 오면 Password가 활성화 되고, Password의 입력이 되면 OK가 활성화 된다.

생각나는대로 구현을 해보면 다음과 유사하다.

<img width="300" alt="image" src="https://user-images.githubusercontent.com/66371206/198241000-6f63ef32-41c3-4e77-9a77-b79fe75b1e2c.png">

하지만 이렇게 되면

- Guest 인지 Login인지에 따라 어떤 TextField를 활성화 비활성화
- Password는 Login이고 Username이 활성화 됐을 때 input이 되는 환경이어야 되고
- OK 버튼은 Guest 일 때는 바로 활성화며, Login 일 때는 Username 과 Password에 입력이 있어야 활성화

등 조건이 추가되고, 프로그램이 복잡해 질 수록 어떤 클래스가 어떤 역할을 하는지 정확하게 파악하기가 어려울 수 있다. 그럴 때 중개자를 두면 손쉽게 해결을 할 수 있다.

<img width="298" alt="image" src="https://user-images.githubusercontent.com/66371206/198241095-f9c99d16-7094-43ae-8534-42ab295f514a.png">

다음과 같이 Dialog란 중개자를 두어, 여러 TextField, CheckBox, Button 등을 확인하여 각각에 상황에 맞는 역할을 하라고 지시하게 만들 수 있다.

## 구현

<img width="500" alt="image" src="https://user-images.githubusercontent.com/66371206/198255317-c3e7600d-e7b1-4f6b-a2ae-2afb8fb129e2.png">

### Mediator

> 중개자 Interface로 colleague를 추가하고, colleague의 변화가 왔을 때 할 일을 지정할 수 있다

```java
// Mediator interface
public interface Mediator {
    public abstract void createColleagues();
    public abstract void colleagueChanged();
}
```

### Colleague

> Colleague 인터페이스, 공통적으로 제공하는 중개자를 세팅하는 부분과, 내가 활성화를 제어하는 부분이 포함

```java
// Colleague interface
public interface Colleague {
    public abstract void setMediator(Mediator mediator);
    public abstract void setColleagueEnabled(boolean enabled);
}
```

### ColleagueButton

> CheckBox에 따라 OK / Cancel을 할 수 있는 버튼

```java
// OK, cancel button
import java.awt.*;

public class ColleagueButton extends Button implements Colleague {
    private Mediator mediator;

    public ColleagueButton(String caption) {
        super(caption);
    }
    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    public void setColleagueEnabled(boolean enabled) {
        setEnabled(enabled);
    }
}
```

### ColleagueCheckbox

> Guest인지 Login인지 선택할 수 있는 체크박스

```java
// Guest, Login checkbox
import java.awt.*;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

public class ColleagueCheckbox extends Checkbox implements ItemListener, Colleague {
    private Mediator mediator;

    public ColleagueCheckbox(String label, CheckboxGroup group, boolean state) throws HeadlessException {
        super(label, group, state);
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    public void setColleagueEnabled(boolean enabled) {
        setEnabled(enabled);
    }

    public void itemStateChanged(ItemEvent event) {
        mediator.colleagueChanged();
    }
}
```

### ColleagueTextField

> Login이라면 Username를 enable, Username이 존재하면 Password를 enable 하는 input field

```java
// Username, Password input box
import java.awt.*;
import java.awt.event.TextEvent;
import java.awt.event.TextListener;

public class ColleagueTextField extends TextField implements TextListener, Colleague {
    private Mediator mediator;

    public ColleagueTextField(String text, int columns) throws HeadlessException {
        super(text, columns);
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    public void setColleagueEnabled(boolean enabled) {
        setEnabled(enabled);
        setBackground(enabled?Color.white : Color.lightGray);
    }

	// event listener
    public void textValueChanged(TextEvent event) {
        mediator.colleagueChanged();
    }
}
```

### LoginFrame

> mediator로 생성부터 change를 감지하면, 동작을 하며 변화에 대한 처리를 해준다

```java
// mediator LoginFrame
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class LoginFrame extends Frame implements ActionListener, Mediator {
    private ColleagueCheckbox checkGuest;
    private ColleagueCheckbox checkLogin;
    private ColleagueTextField textUser;
    private ColleagueTextField textPass;
    private ColleagueButton buttonOk;
    private ColleagueButton buttonCancel;

    public LoginFrame(String title) throws HeadlessException {
        super(title);
        setBackground(Color.lightGray);
        setLayout(new GridLayout(4,2));
        createColleagues();
        add(checkGuest);
        add(checkLogin);
        add(new Label("Username"));
        add(textUser);
        add(new Label("Password"));
        add(textPass);
        add(buttonOk);
        add(buttonCancel);
        colleagueChanged();
        pack();
        show();
    }

    public void createColleagues() {
        // init components and set mediator
        CheckboxGroup g = new CheckboxGroup();
        checkGuest = new ColleagueCheckbox("Guest", g, true);
        checkLogin = new ColleagueCheckbox("Login", g, false);
        textUser = new ColleagueTextField("", 10);
        textPass = new ColleagueTextField("", 10);
        textPass.setEchoChar('*');
        buttonOk = new ColleagueButton("OK");
        buttonCancel = new ColleagueButton("Cancel");

        checkGuest.setMediator(this);
        checkLogin.setMediator(this);
        textUser.setMediator(this);
        textPass.setMediator(this);
        buttonOk.setMediator(this);
        buttonCancel.setMediator(this);

        checkGuest.addItemListener(checkGuest);
        checkLogin.addItemListener(checkLogin);
        textUser.addTextListener(textUser);
        textPass.addTextListener(textPass);
        buttonOk.addActionListener(this);
        buttonCancel.addActionListener(this);
    }

    public void colleagueChanged() {
        if (checkGuest.getState()) {
            textUser.setColleagueEnabled(false);
            textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(true);
        } else {
            textUser.setColleagueEnabled(true);
            userpassChanged();
        }
    }

    private void userpassChanged() {
        if (textUser.getText().length() > 0) {
            textPass.setColleagueEnabled(true);
            if (textPass.getText().length() > 0) {
                buttonOk.setColleagueEnabled(true);
            } else {
                buttonOk.setColleagueEnabled(false);
            }
        } else {
            textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(false);
        }
    }

    public void actionPerformed(ActionEvent event) {
        System.out.println(event.toString());
        System.exit(0);
    }
}
```

### Main

```java
public class Main {
    public static void main(String[] args) {

        new LoginFrame("Mediator Sample");
    }
}
```

## 등장인물

<img width="680" alt="image" src="https://user-images.githubusercontent.com/66371206/198248610-bedf001f-b4d7-47be-a786-d9370317482b.png">

### Mediator

Colleague 역할과 통신을 하며 조정을 실행하기 위한 Interface(API)를 결정

### Concrete Mediator

Mediator 역할의 인터페이스(API)를 구현하여 실제의 조정을 실행

### Colleague

Mediator역할과 통신을 실행할 인터페이스(API) 결정

### Concrete Colleague

Colleague 역할의 인터페이스(API)를 구현

## POINT

### 분산처리?

유효/무효에 대한 모든 로직은 colleagueChanged 메소드에 포함이 되어 있기에 해당 메소드만 수정을 하면 된다. 객체지향 프로그래밍을 하다보면, 한 곳에 집중되는것을 피하기 위해 분산하여 처리를 하는 경우가 있지만, 무조건 분산하는것이 옳은 것이 아닌, 분산 할 것은 분산시키며, 집중시킬 것은 집중시켜야 된다.

### 통신 경로의 증가

OK button 이 text field에 따라 on/off 가 정해지는 상황에서, 중개자가 없는 경우에는 바로 text-field 가 있는지 확인 후 on/off 를 확인 할 수 있었다. 하지만, 중개자가 있기에 text field의 변화를 중개자에게 알리고, 중개자가 on/off를 결정하는 단계가 하나 늘었다. 이러한 인스턴스가 늘어날 수록 부담이 될 수 있다.

### 재사용

ConcreteColleague 역할은 concreteMediator가 손쉽게 재사용 할 수 있지만, concreteMediator의 재사용은 쉽지 않다. 예제에서의 LoginFrame이 생성과 제어를 모두 담당하기 때문에 의존도가 너무 높아 재사용하기 어려운 상황이 올 수 있다.

> React와 비교하면 LoginFrame이라는 전역 상태를 사용하는것과 유사하다 볼 수 있을듯..!!

> mediator가 원래 생성부터 중개 역할까지 부담을 하는걸까? Factory? 한번 고민해보자

참고 : https://refactoring.guru/ko/design-patterns/mediator
