---
layout: post
title: 방문자 패턴
subtitle: 방문자 패턴 박살내기
categories: GoF
tags: [TIL, GoF]
---

## 방문자 패턴

데이터 구조는 많은 요소가 저장되어 있고, 각 요소에 대해 어떻게 `처리`가 필요한 경우를 생각해보자.

`처리`에 대한 코드는 어디에 있어야 될까? 일반적으로 데이터 구조를 표지하는 클래스에 기술하겠지만, `처리`가 늘어날 때 마다 데이터 구조의 클래스를 수정해야 되는 경우가 생긴다.

방문자 패턴은 해당 문제를 **데이터 구조와 처리를 분리**하며 해결한다. 데이터 구조 안을 돌아다닐 수 있는 `방문자` 클래스를 준비하여, 그 클래스에게 처리를 위임한다. 즉 새로운 처리를 추가하고 싶을 때 새로운 `방문자`를 만들게 되면 된다.

## 방문자 패턴의 등장인물

![image-1](https://user-images.githubusercontent.com/66371206/194114652-7aed7b17-134c-4cf4-bf18-e95806874317.png)

### Visitor (방문자)

Visitor는 데이터 구조의 구체적인 요소(ConcreteElement 역할)마다 visit(xxxx) 메소드를 선언합니다. 메소드는 xxxx를 처리하기 위한 메소드이고, 구현은 ConcreteVisitor가 합니다.

### Concrete Visitor

Visitor 의 인터페이스(API)를 구현한다.

> 예제의 ListVisitor에서 currentdir 값이 변화하듯 visit을 수행하는 도중 ConcreteVisitor 역할의 내부 상태가 변화하는 일도 있다.

### Element

Visitor역할의 방문할 곳을 나타내는 역할로, 방문자를 받아들이는 accept 메소드를 선언한다. 메소드의 인자값으로는 Visitor역할이 전달된다.

### Concrete Element

Element역할의 인터페이스(API)를 구현하는 역할이다.

> 예제에서 File이나 Directory에서 accept 메소드를 오버라이딩해서 구현한것과 동일.

### Object Structure

Element역할의 집합을 취급하는 역할이다. ConcreteVisitor 역할이 각각의 Element역할을 취급할 수 있는 메소드를 구비하고 있다.

> 예제에서는 Directory가 ConcreteElementB와 ObjectStructure 두 역할을 동시에 수행한다

---

## 예제

### Composite 패턴 복습

Composite 패턴에서의 상황과 유사한 상황을 가정하고 사용하기 위해, Composite 패턴의 예제를 복습해보자.

> 전체와 부분을 동일시해서 재귀적인 구조를 만들기 위한 디자인 패턴

![image-3](https://user-images.githubusercontent.com/66371206/194115416-8410628d-67da-455d-8acc-8dedd1f2ce02.png)

File 과 Directory의 `printList`를 `accept`로 바꾸고, Directory에 `iterator` 메소드가 추가된 것을 제외하고는 동일한 구현이다.

![image](https://user-images.githubusercontent.com/66371206/194115155-65810fd3-d2e3-4140-98e9-c53335b6783a.png)

| 이름                   | 해설                                                                          |
| ---------------------- | ----------------------------------------------------------------------------- |
| Visitor                | 파일과 디렉토리를 방문하는 방문자를 나타내는 추상클래스                       |
| Element                | VIsitor 클래스의 생성자를 받아들이는 데이터 구조를 나타내는 인터페이스        |
| ListVisitor            | Visitor 클래스의 하위 클래스로 파일과 디렉터리의 종류를 나타내는 클래스       |
| Entry                  | File과 Directory의 상위 클래스가 되는 추상 클래스(Acceptor 인터페이스를 구현) |
| File                   | 파일을 나타내는 클래스                                                        |
| Directory              | 디렉토리를 나타내는 클래스                                                    |
| FIleTreatmentException | File에 대해서 add한 경우 발생하는 예외 클래스                                 |
| Main                   | 동작 테스트용 클래스                                                          |

### Visitor Class

- Visitor는 '방문자'를 나타내는 추상클래스이고 자기가 방문할 곳(FIle, Directory)에 의존한다.

- Visitor클래스는 File과 Directory를 가지는 두개의 메소드를 오버로드해서 가지며 위임의 역할을 할 필드가 된다.

```java
public abstract class Visitor {
  public abstract void visit(File file);
  public abstract void visit(Directory directory);
}
```

### Element Interface

- Visitor를 `accept`로받아들이는 인터페이스

```java
public interface Element {
  public abstract void accept (Visitor v);
}
```

### Entry Class

- Element 인터페이스를 구현
- Entry Class를 Visitor Pattern에 적용시키기 위해 사용
- accept를 실제 구현하는 것은 Entry 의 하위 클래스인 File 과 Directory Class

```java
import java.util.Iterator;

public abstract class Entry implements Element{
  public abstract String getName();
  public abstract int getSize();
  public Entry add(Entry entry) throws FileTreatmentException {
    throw new FileTreatmentException();
  }
  public Iterator iterator() throws FileTreatmentException {
    throw new FileTreatmentException();
  }
  public String toString() {
    return getName() + " ("+getSize()+")";
  }
}
```

> add와 iterator는 Directory Class에만 유효하므로 Entry Class 에서는 에러로 처리

### File Class

- Composite 에서의 예제와 유사
- accept 메소드 안에서 Visitor의 visit 메소드를 호출
- File로 오버로드 된 메소드가 호출된다
- visit 메소드를 호출하며, File의 인스턴스(this)를 Visitor에세 알려준다

```java
public class File extends Entry {
  private String name;
  private int size;

  public File(String name, int size) {
    this.name = name;
      this.size = size;
  }

  public String getName() {
    return name;
  }

  public int getSize() {
    return size;
  }

  @Override
  public void accept(Visitor v) {
    v.visit(this);
  }
}
```

### Directory Class

- Composite 패턴의 예제와 유사
- iterator는 포함되어 있는 Directory Entry의 종류를 얻기 위한 Iterator 반환
- accept가 visit(Directory) 메소드를 호출

```java
public class Directory extends Entry {
  private String name;
  private ArrayList directory = new ArrayList();

  public Directory(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    return name;
  }

  public int getSize() {
    int size = 0;
    for (Object o : directory) {
      Entry entry = (Entry) o;
      size += entry.getSize();
    }
    return size;
  }

  @Override
  public Entry add(Entry entry) {
    directory.add(entry);
      return this;
  }

  @Override
  public Iterator iterator() throws FileTreatmentException {
    return directory.iterator();
  }

  @Override
  public void accept(Visitor v) {
    v.visit(this);
  }
}
```

### ListVisitor Class

- Visitor Class의 하위 클래스로, 데이터 구조를 돌아다니며 종류를 표시한다
- visit(File)과 visit(Directory)를 구현
- currentdir는 현재 주목하고 있는 Directory 이름을 저장
- visit(File) 메소드는 **File 클래스의 인스턴스에 대해서 실행해야 될 처리**를 기술
- visit(Directory) 메소드는 **Directory 클래스의 인스턴스에 대해서 실행해야 될 처리**를 기술

> directory의 처리란? Directory의 Iterator를 가져와, 각 엔트리에 대한 accept 메소드를 호출

- accept 메소드는 visit 메소드를 호출하고, visit 메소드는 accept를 호출
- visit 메소드와 accept 메소드가 서로 상대를 호출

```java
public class ListVisitor extends Visitor{
  private String currentdir = ""; // 현재 주목하고 있는 디렉토리 이름 저장하는 필드

  @Override
  public void visit(File file) {
    System.out.println(currentdir + "/" + file);
  }

  @Override
  public void visit(Directory directory) {
    System.out.println(currentdir + "/" + directory);
    String savedir = currentdir;
    currentdir = currentdir + "/" + directory.getName();
    Iterator it = directory.iterator();
    while (it.hasNext()) {
      Entry entry = (Entry)it.next();
      entry.accept(this);
    }
    currentdir = savedir;
  }
}
```

### FileTreatment Exception

- 예외처리

```java
public class FileTreatmentException extends RuntimeException {
  public FileTreatmentException() {
  }

  public FileTreatmentException(String message) {
    super(message);
  }
}
```

### Main Class

- composite 패턴과 매우 유사하지만, Directory 표시를 위해, 표시 방문자인 ListVisitor의 인스턴스를 사용한다

```java
public class Main {
  public static void main(String[] args) {
    try{
      System.out.println("Making root entries...");
      Directory rootdir = new Directory("root");
      Directory bindir = new Directory("bin");
      Directory tmpdir = new Directory("tmp");
      Directory usrdir = new Directory("usr");

      rootdir.add(bindir);
      rootdir.add(tmpdir);
      rootdir.add(usrdir);
      bindir.add(new File("vi", 10000));
      bindir.add(new File("latex", 20000));
      rootdir.accept(new ListVisitor());

      System.out.println("");
      System.out.println("Making user entries...");
      Directory kim = new Directory("Kim");
      Directory lee = new Directory("Lee");
      Directory park = new Directory("Park");
      usrdir.add(kim);
      usrdir.add(lee);
      usrdir.add(park);
      kim.add(new File("diary.html", 100));
      kim.add(new File("Composite.java", 200));
      lee.add(new File("memotex", 300));
      park.add(new File("game.doc", 400));
      park.add(new File("junk.mail", 500));
      rootdir.accept(new ListVisitor());
    }catch(FileTreatmentException e){
      e.printStackTrace();
    }
  }
}
```

### Sequence Diagram

![image-2](https://user-images.githubusercontent.com/66371206/194121630-a8c37d6a-8755-4016-bcb4-2c4b32fc4671.png)

1.  Main 클래스에서 ListVisitor 인스턴스 생성
2.  Main에서 Directory의 accept 메소드 호출. 인자값으로는 ListVisitor 인스턴스 전달
3.  Directory 의 인스턴스에서는 인자로 받은 ListVisitor의 visit(Directory) 메소드 호출
4.  visit(Directory) 로직 내부에서 디렉토리 내부를 조사해 파일의 accept 메소드 호출하며 인자값으로 자기자신(this)를 전달. (자기자신은 ListVisitor)
5.  File의 인스턴스는 인자로 전달받은 ListVisitor의 visit(File)메소드를 호출.
6.  5번 항목의 File의 visit(File)로직이 완료되면 다음 Iterator가 다음 File을 next()로 꺼내와서 두 번쨰 파일의 accept메소드를 호출(4~5과정의 반복)
7.  이전과 동일하게 visit(File)의 메소드를 호출하여 완료되면 자신을 호출한 곳으로 돌아가며 Main까지 돌아갑니다.

즉 정리하자면 다음과 같다

- Directory와 File 인스턴스에 대해서 accept가 한 번 호출된다
- accept 메소드는 각 인스터에서 한번만 호출된다
- ListVisitor의 인스턴스에 대해 visit 메소드가 호출된다
- visit을 처리하고 있는것은 ListVisitor의 인스턴스이다

> **ListVisitor 부분에서 visit에 대한 처리가 집중**된다

## 참고

### Double Dispatch

Element역할은 accept(Visitor)메소드로 Visitor를 받아들이고 Visitor는 visit(element)메소드로 element를 visit한다. 이처럼 Visitor Pattern에서는 ConcreteElement역할과 ConcreteVisitor 역할 한쌍에 의해 실제 처리가 결정되는데 이런 것을 더블 디스패치(double dispatch: 이중 분리)라고 한다.

### 왜 이렇게 분리하는가

굳이 Visitor Pattern을 사용해서 기능과 처리를 분리하고 재귀적으로 호출하며 어렵게 설계를 해야하는 이유는 무엇일까?
'처리'를 데이터 구조에서 분리를 하는게 Visitor Pattern의 핵심인데, 데이터 구조는 요소를 집합으로 정리하거나 요소 사이를 연결해주는 중요한 역할을 한다.
하지만, 구조를 유지하는 것과 구조를 기초로 처리를 정의하는것은 별개다.
만약, 처리내용을 File이나 Diretory 클래스의 메소드로 구현할 경우, 새로운 기능('처리')이 추가될 때마다 클래스를 수정해야하는데, 이러면 클래스의 독립성도 떨어지고 단일 책임 원칙도 위배하게 된다.

### OCP

확장에대해서는 열려있지만, 수정에 대해서는 닫혀있어야 한다는 'The Open-Closed Principle(OCP)' 원칙이 있다.
즉, 클래스를 설계할 때 특별한 이유가 없는 한 확장을 허용해야 한다는 것인데, 이것이 확장에 대해서는 열려있다는 의미이다.
하지만, 확장을 허용한다고 확장을 할 때마다 기존의 클래스를 수정하는것은 곤란한데, 확장을 해도 기존의 클래스는 수정할 필요가 없는 것이 '수정에 대해서는 닫혀있다' 라는 의미가 된다.

> 결국, 기존의 클래스를 수정하지않고 기능추가 확장이 되야한다는 말.

개발을 하게되면 시간이 흐를수록 기능을 확장해야하는 경우가 빈번한데 그럴때마다 이미 완성된 클래스를 수정하는 것은 프로그램의 완성도(품질)를 떨어트릴 위험이 있다.

### 확장성

ConcreteVisitor 역할을 추가하는것은 쉽다. 구조를 담당하는 클래스(ConcreteElement)역할을 수정할 필요가 없기 때문.

반면, ConcreteElement 역할의 추가는 어려운데 그 이유는, 기존의 File과 Directory 클래스에 더해 새로운 Entry의 하위 클래스인 Device 클래스를 만들기 위해서는 기존에 구현되있는 모든 Visitor 클래스에 visitor(Device)메소드를 만들어야 하고 하위 클래스(ConcreteVisitor)에도 visit(Device)메소드를 구현해야 하기 때문.

> 데이터와 데이터를 추상화하고, 그 동작을 추상화를 해주는 패턴! 그리고 Visitor가 알고있는 것은 Concrete Element임에 주의

---

참조

[Java언어로 배우는 디자인 패턴 입문]-영진닷컴
