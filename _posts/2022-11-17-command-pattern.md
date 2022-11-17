## Command Pattern

![Command Pattern 001](https://user-images.githubusercontent.com/66371206/202383203-558eb9f4-7433-4b6c-a5b5-8c27f52c1dd7.jpeg)

커맨드 패턴에 대하여 알아봅시다

![Command Pattern 002](https://user-images.githubusercontent.com/66371206/202383211-00446e40-9907-4b8c-84e3-f093f8120dac.jpeg)

간단한 게임을 하나 제작할 예정입니다.
이번 게임에서는 두가지 버튼만 활용할 예정이고, 버튼들은 `alt` 와 `ctrl` 만 사용할 예정이기에, `alt`에는 `jump` `ctrl` 에는 `attack` 이라는 함수를 매핑을 했습니다.

![Command Pattern 003](https://user-images.githubusercontent.com/66371206/202383233-2b84ab5a-7db6-482f-947d-59eb3a38d294.jpeg)

사실 이정도의 게임이라면 문제가 없습니다. 하지만 키보드 세팅이 복잡한 게임이라면 어떻게 될까요?
그럼 키 마다 세팅을 할 것입니다. 그리고 모든 키 매핑이 character에 관한 것도 아니고, `esc`는 게임 세팅창을 여는 등 다양한 `action`이 이루어 질 것입니다.

여기서 더 복잡한 상황을 생각해 보겠습니다.

![img](https://user-images.githubusercontent.com/66371206/202387140-a83c1032-65f7-444d-b5e8-6767e8866b83.gif)

위의 영상을 보면, 같은 공격키를 누르지만, 공격에는 곰 -> 새 -> 호랑이 -> 창 순서로 공격하게 되어 있습니다.

![Command Pattern 004](https://user-images.githubusercontent.com/66371206/202383238-80b7e3af-ea54-4569-bdc3-a08a86e32bd2.jpeg)

그걸 간단하게 코드로 보면 다음과 같이, `ctrl`을 눌렀을 때, 상황에 따라 다른 공격이 이루어지도록 수정을 해둬야 됩니다.

단순히 `키를 누른다` -> `해당 키에 해당하는 Action을 취한다` 면 됐다고 생각했던 코드가 점점 복잡해져 가는게 보이시나요?

![Command Pattern 005](https://user-images.githubusercontent.com/66371206/202383248-5e72fd6d-45c7-448e-8199-ceb56ce0b2ec.jpeg)

하지만 여기서 끝이 나지 않습니다. 보통 게임에는 유저가 원하는 키에 유저가 원하는 Action을 취하도록 커스텀 가능한 환경을 제공합니다. 그럼 alt에 해당했던 `jump` 가 `npc/채집`으로 바뀌어야 됩니다.
이런 수정사항이 게임에 runtime에 수정되기란 상당히 힘들어 보입니다.

아니면 게임을 진행 할 수록 아까 보았던 attack 순서가 바뀌거나 추가가 되었다면 어떻게 될까요? 공격으로 지정된 키를 찾아 공격에 대한 로직을 수정해야 될 것입니다.

![Command Pattern 006](https://user-images.githubusercontent.com/66371206/202383258-1020a0e2-2f2e-4389-8d0a-660ba409f980.jpeg)

Command Pattern을 활용하면 다음과 같이 간결하고 유지보수가 쉬운 형태로 유지 할 수 있습니다.

![Command Pattern 007](https://user-images.githubusercontent.com/66371206/202383263-0b6a0c2e-f958-4ebd-9554-bd3d1858f8a0.jpeg)

Command Pattern에는 5개의 등장인물이 존재합니다.

1. Command : 명령의 Interface를 정의
2. Concrete Command : Command Interface를 구현
3. Invoker : 명령의 행동을 개시하는 역할로 Command를 호출
4. Receiver : Command가 실행될 때 대상이 되는 역할
5. Client : Concrete Command를 생성 및 Receiver 역할을 할당

Command는 단순히 실행을 하라는 execute 메소드만 생성을 하고, Concrete Command에서 해당하는 execute 메소드를 구현을 합니다.

예를 들어, AttackCommand에서의 execute라는 명령은 캐릭터를 공격을 유발합니다.

![Command Pattern 008](https://user-images.githubusercontent.com/66371206/202383268-9e6626e0-f74f-441e-8f3d-787d7bbdf0c2.jpeg)

이와 유사하게 JumpCommand도 만들 수 있습니다.

![Command Pattern 009](https://user-images.githubusercontent.com/66371206/202383273-f1c4eec2-1842-4e28-9219-621bad62eb1d.jpeg)

마찬가지로, SettingCommand는 game에 해당하는 setting창을 여는 Concrete Command또한 만들 수 있다.

> 해당 command는 멤버변수는 character가 아닌 game임에 주의!

![Command Pattern 010](https://user-images.githubusercontent.com/66371206/202383276-e859b904-42ef-4d3d-8da2-b235c8c740ce.jpeg)

이제 Receiver는 Concrete Command의 실행에서 사용되는 클래스이다. 여기서 게임에서 동작하는 실제 로직을 작성하면 된다.

![Command Pattern 011](https://user-images.githubusercontent.com/66371206/202383278-0a4eebac-5603-4b20-93fe-afd07b044966.jpeg)

Client는 버튼으로, 해당 버튼이 어떠한 커맨드를 할 지 지정할 수 있는 setCommand 메소드가 존재하며, Invoke된 경우 커맨드를 실행시킬 수 있는 onPressed란 메소드를 구현해 주었다.

![Command Pattern 012](https://user-images.githubusercontent.com/66371206/202383279-7ea3c8ed-f171-41ee-9400-17e7ad2f54e6.jpeg)

이제 실제로 Main에서 동작시켜보자.

우선, 필요한 버튼, 커맨드, Receiver 등을 할당시켜준다.
그 다음, 필요한 버튼에 원하는 커맨드를 setCommand로 할당시켜 준다.
마지막으로, 어떠한 입력이 나왔을 때, 원하는 버튼에 해당하는 command만 실행시켜주면 끝이다.

![Command Pattern 013](https://user-images.githubusercontent.com/66371206/202383286-03ab9de2-cb11-4dc8-ba69-298d752f7669.jpeg)

괜히 더 복잡해 보일 수 있지만, 유지보수의 관점에서 보면 정말 좋다.
아까와 같은 상황처럼, 버튼에 해당하는 Action이 수정되어야 한다면, `Button.setCommand(원하는 커맨드)` 한줄로 수정할 수 있게 된다.

![Command Pattern 014](https://user-images.githubusercontent.com/66371206/202383293-d9a3720e-2cba-41d2-82ea-09248e220ac5.jpeg)

혹은 공격에 대한 로직에 수정이 필요하다면, character 안에서 attack의 로직만 수정해 주면 되기에, 유지보수가 정말 쉬워진다.

![Command Pattern 015](https://user-images.githubusercontent.com/66371206/202383298-b5d3877f-3572-4c2b-8b52-8404b8499019.jpeg)

이걸 sequence diagram에서 보면 다음과 같다.

1. 미리 버튼에 Command를 설정해준다
2. 버튼을 누른다면
3. 해당하는 Command의 execute를 호출
4. execute에 해당하는 행동을 Receiver가 동작
