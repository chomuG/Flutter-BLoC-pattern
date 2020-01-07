# Bloc Solution Page



### Rule



##### Bloc Interface (Bloc 0.13.0 => Bloc 2.0.0)

> 변경 사항 외 추가된 기능 및 함수는
>
> https://bloclibrary.dev/#/flutterbloccoreconcepts
>
> https://pub.dev/packages/flutter_bloc/versions/2.1.1
>
> 사이트들을 참고 바랍니다





#### 1. Event 추가 시 문법 변경

- 기존 문법: 

  ```dart
  Bloc.dispatch(event);
  ```

- 변경된 문법: Bloc.add(event);

  ```dart
  Bloc.add(event);
  ```

- 변경된 문법에서 변경된 기능: 이미 close 된(기존에서는 dispose) bloc에서 add를 호출했다면 BlocDelegate 및 onError 함수로 위임됨



#### 2. transform 함수 사용 문법 변경

- 기존 문법: 

  ```dart
  transform
  ```

- 변경된 문법:

  ```dart
  transformEvents
  ```

- ex)

  > ```dart
  > @override
  > Stream<FindAccountState> transform(
  >   Stream<FindAccountEvent> events,
  >   Stream<FindAccountState> Function(FindAccountEvent event) next,
  > ) {
  >     final observableStream = events as Observable<FindAccountEvent>;
  >     final nonDebounceStream = observableStream.where((event) {
  >       return (event is! EmailChanged);
  >   });
  >   final debounceStream = observableStream.where((event) {
  >     return (event is EmailChanged);
  >   }).debounceTime(Duration(milliseconds: 300));
  >   return super.transform(nonDebounceStream.mergeWith([debounceStream]), next);
  > }
  > ```

  =>

  > ```dart
  > @override
  > Stream<FindAccountState> transformEvents(
  >   Stream<FindAccountEvent> events,
  >   Stream<FindAccountState> Function(FindAccountEvent event) next,
  > ) {
  >   final observableStream = events as Observable<FindAccountEvent>;
  >   final nonDebounceStream = observableStream.where((event) {
  >     return (event is! EmailChanged);
  >   });
  >   final debounceStream = observableStream.where((event) {
  >     return (event is EmailChanged);
  >   }).debounceTime(Duration(milliseconds: 300));
  >   return super.transformEvents(nonDebounceStream.mergeWith([debounceStream]), next);
  > }
  > ```

  

#### 3. dispose 함수 사용 문법 변경

- 기존 문법: 

  ```dart
  Bloc.dispose();
  ```

- 변경된 문법: 

  ```dart
  Bloc.close();
  ```

- 변경된 문법에서 변경된 기능: event가 동작하는 중에 close가 호출된다면 기존에서는 호출 이후 state는 모두 무시되며, transition이 일어나지 않지만 변경 이후에서는 event가 완료 될 때 까지 프로세스를 진행한다

- ex)

  > ```dart
  > @override
  > void dispose(){
  >     _registerChildBloc.dispose();
  >     super.dispose();
  > }
  > ```

  =>

  > ```dart
  > @override
  > void dispose(){
  >     _registerChildBloc.close();
  >     super.dispose();
  > }
  > ```

  

#### 4. BlocBuilder

- BlocBuilder<Bloc(bloc type), BlocState>()의 <Bloc(bloc type), BlocState>부분처럼 타입명시를 해주지 않아도 정상 작동하나 정확한 구분 및 통일성을 위해 타입명시하는것을 권장함

- 크게 변경된 사항은 없으나 추가된 사용법이 있으니 https://bloclibrary.dev/#/flutterbloccoreconcepts 참고바람

##### <농플>

- 보통 bloc을 하나만 사용하는 경우 BlocBuilder<Bloc(bloc type), BlocState>()의 <Bloc(bloc type), BlocState>부분처럼 타입명시를 해주지 않아도 정상 작동한다. 
- bloc을 두개이상 사용할 경우 타입명시를 정확하게 해달라는 메세지가 뜨며 코드에 에러가 난다. 적어줄 타입은 상위 클래스의 타입(?)을 적어주면 해결된다. 즉, <Bloc<Equatable, dynamic>, dynamic>으로 적어주면 된다.
- ex)

![img](https://lh4.googleusercontent.com/g97QsNR1HKWV6PsYYRzkH0rsb-fQwzpLw16Gw04ybiFzLnaNAaH9YmGTGFuiAPf25eiNjY1U9PKtnrS2nGQfuNnpvrftIhGw4oYQo5Y5X7GACof1Uj_ULiyGrU7mOdF0Fd9Xo6Qk)





#### 5. BlocProvider 함수 사용 방법 변경

- 기존 사용법:

- Bloc을 사용 후 수동으로 Closing(0.13.0버전에서는 dipose)  해 주어야 했음

  ```dart
  BlocProvider(
    bloc: BlocA(),
    child: ChildA(),
  );
  ```

- 변경된 사용법:

  - create

    - Bloc을 새로 생성하여 하위 subtree에 넘겨줌

    - 사용시 Bloc type의 명시를 정확하게 해 주어야 함

    - Closing을 자동으로 수행함

      

    > ```dart
    > BlocProvider<BlocABloc>(
    >   create: (BuildContext context) => BlocA(),
    >   child: ChildA(),
    > );
    > ```

  - value

    - 기존에 사용하던 Bloc을 하위 subtree에서 이어서 사용하기 위해 넘겨줌
    - 앞선 페이지에서 Bloc을 계속 사용해야 하니 Closing을 자동으로 수행하지 않음

    > ```dart
    > BlocProvider.value(
    >   value: BlocProvider.of(<BlocA>(context),//or 선언한 변수 ex)_blocA
    >   child: ChildA(),
    > );
    > ```



- BlocProvider로 넘겨준 Bloc instance 사용법

  - Bloc을 선언해줘야 하는 경우

    - Event call(bloc.add)를 해 주어야 하는 경우

      > Event call 시에는 bloc instance를 필요로 하기 때문에 선언을 해 줄 필요가 있다

    - 해당 페이지에서 Bloc instance를 여러개 사용하는 경우

      > MultiBlocProvider를 통해 Bloc instance를 여러 개 넘겨받은 경우, 해당 Bloc instance가 무엇인지 선언하지 않으면 구분하여 사용할 수 없기 때문에 선언을   통해 해당 Bloc instance에 access하여 사용한다

    - ex) Bloc instance 선언방법 및 사용법

      ```dart
      BlocA _blocA = BlocProvider.of<BlocA>(context);
      ```

      ```dart
      BlocBuilder(//BlocListener도 동일
        bloc: _blocA,
        builder: (context, state){
          // return widget here based on _blocA's state
        }
      );
      ```

      

  - Bloc을 선언해주지 않아도 되는 경우

    - Event call을 하지 않고 단일 Bloc instance를 사용하는 경우

      > BlocBuilder, BlocListener를 사용할 때 bloc 파라미터를 명시하지 않고 바로 사용해도 자동으로 BlocProvider에서 넘겨받은 Bloc instance로 접근하여 사용하게 된다

    - ex)

      ```dart
      BlocBuilder(//BlocListener도 동일
        builder: (context, state){
          // return widget here based on _blocA's state
        }
      );
      ```

      

- BlocProvider create를 사용하는 이유

  - Bloc instance는 create가 아니더라도 다음과 같이 생성할 수 있다

    ```dart
    BlocA _blocA = BlocA();
    ```

    그러나 이와 같이 Bloc instance를 생성할 경우 따로 페이지 dispose 시에 _blocA.close() 처리를 해 주어야 한다.  

    BlocProvider create를 사용하면 close 처리를 자동으로 수행해 준다. 따라서 Bloc instance를 다른 페이지에 넘겨줘 공유하며 사용하지 않고 단일 페이지에서 사용하더라도 create를 사용하여 Bloc instance를 생성하는 것이 더 효율적라고 할 수 있다.



#### 6. Nested BlocProvider를 위한 위젯 생김(MultiBlocProvider)

- 기존

![img](https://lh4.googleusercontent.com/96JH6Nt27SsqppaCfBTz8A28PYaMrxPkLz54XPmpFLR2AWDoyRjGAHm5Wl0fKC-5il_zX6PEjP_u4zGsTtV3yxnPKbyuTQTIg6reMVA6-_hcn9Ap4fCVo6MZgbWJN6kDyG-yXgem)

- 변경 후

![img](https://lh5.googleusercontent.com/al_8KzgtBYQ9_G3Yz8o0Ytccp0hHako2TkPKDr7kgBTareDWJeTSkdxhQNjourStu4X7t-xUkO6bHR2VSAG7ekIE1HokHWZTsyP3u8Grppr783V1ABtW250JyZhkl8Ap1YicoBlV)





#### 7. BlocListener

- BlocListener<Bloc(bloc type), BlocState>()의 <Bloc(bloc type), BlocState>부분처럼 타입명시를 해주지 않아도 정상 작동하나 정확한 구분 및 통일성을 위해 타입명시하는것을 권장함

- 크게 변경된 사항은 없으나 추가된 사용법이 있으니 https://bloclibrary.dev/#/flutterbloccoreconcepts 참고바람





#### 8. Nested BlocListener를 위한 위젯 생김(MultiBlocListener)

- 기존

![img](https://lh4.googleusercontent.com/1uQWOluR_JS0jl8I4VxDVTTUJVcS6TJAyVFPNdjMICUWfSEWBZy-_0bBEDjKel7cnMKmpBzVg2wgX5vgeIXvNJtHxumWMfYA00cidpgYuKuuewKI3TLxrbkaoMZGZPIp99TmyrXp)

- 변경 후

![img](https://lh5.googleusercontent.com/cwSaN80RaAvYZuYl3Ayxv9LWEN-0eF8gClfHiP-zjHaGEVyzpeuRFB1lT84zJCmos_d7_oYTJyKqfZFdwZiwLXnGVrsgKcPCL1scP3VPlm2L4xsJDXjl229SAuG9_VPnquukrrl8)





## 부록 : 새롭게 추가된 함수 및 위젯

#### 1. transformStates

- Stream<State>를 새로운 Stream<State>로 변환하는 함수
- 기본적으로 transformState는 들어오는 스트림을 반환
- transformState를 재정의하여 transition이 발생하는 빈도와 specificity를 조작할 수 있다.





#### 2. RepositoryProvider

- BlocProvider와 완전히 같은 기능. Bloc이 아니라 Repository를 전달한다는 것이 차이점

- ex)

  ```dart
  RepositoryProvvider(
    create: (context) => RepositoryA(),
    child: ChildA(),
  );
  ```

  

