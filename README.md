# Flutter Provider

생성: 2023년 7월 13일 오후 11:22
해시태그: Dart, Flutter, Provider

<aside>
💡 Flutter로 앱을 만들 때 만이 사용되는 패키지인 Provider에 대해 알아보자

</aside>

## 목록

> [analysis_options.yaml](https://www.notion.so/analysis_options-yaml-9c5ee1455be04ac999fea6c9e92d58f8?pvs=21)
> 
> 
> [ChangeNotifier](https://www.notion.so/ChangeNotifier-77ad65f9495a4ff78498003408da0657?pvs=21) 
> 
> [Provider 인스턴스](https://www.notion.so/Provider-a77d1172eb1a4399a96f8934525baacf?pvs=21) 
> 
> [T 타입의 인스턴스 생성](https://www.notion.so/T-f383fbd32caa4473ae156f639b488fb0?pvs=21) 
> 
> [Extension method](https://www.notion.so/Extension-method-895438a036fc4e49be9a46878674083d?pvs=21) 
> 
> [Provider](https://www.notion.so/Provider-5ac04e02ec694d5ab41cc6a8240b75d1?pvs=21) 
> 
> [MultiProvider](https://www.notion.so/MultiProvider-0e2757f1cffc4f4f937921e8f533fbc2?pvs=21) 
> 
> [FutureProvider](https://www.notion.so/FutureProvider-501a95f08d964b1d88c21fc4e121534e?pvs=21) 
> 
> [StreamProvider](https://www.notion.so/StreamProvider-b5c5276544804b03864a2b896ac5fd34?pvs=21) 
> 
> [Consumer](https://www.notion.so/Consumer-8914df49c45c4a29b93eff596b4a196e?pvs=21) 
> 
> [Slector](https://www.notion.so/Slector-16b4551d0420482ab4e076e66b9384b4?pvs=21) 
> 
> [Provider Access](https://www.notion.so/Provider-Access-f496c3c6f73c4a8ba7e7bff76aca93b3?pvs=21) 
> 
> [Anonymous route](https://www.notion.so/Anonymous-route-1af6b6738fd644e2919a6149bf2e5db3?pvs=21) 
> 
> [Named route](https://www.notion.so/Named-route-e3d2ad5d62484ff69be8d50cacb43276?pvs=21) 
> 
> [ProxyProvider](https://www.notion.so/ProxyProvider-25737e90f8b343bc946f05ab7a1d9d31?pvs=21) 
> 
> [ChangeNotiferProxyProvider](https://www.notion.so/ChangeNotiferProxyProvider-5fa29c926ae64f74bb318067df7516ec?pvs=21)
> 
> [addPostFrameCallback](https://www.notion.so/addPostFrameCallback-5bc206278c6b4f87bc94dbceb87956e7?pvs=21) 
> 

## analysis_options.yaml

`include: package:flutter_lints/flutter.yam`을 주석 처리 해준다. provider로 개발하는 과정에서 번거롭게 하기 때문

```yaml
# include: package:flutter_lints/flutter.yaml
```

## ChangeNotifier

### 클래스 생성법

```dart
class Examle with ChangeNotifier{
	int counter = 0;

	const Example({required this.counter]);

	void increment() {
		counter++;
		notifyListeners(); //등록된 리스너들에게 값이 바뀌었다고 알려주는 함수임
	}
}
```

### 리스너 등록법

```dart
final example = Example();

// 리스너를 등록하는 메소드
// 반드시 void 함수만 입력해야한다.
example.addListener(myListener);

void myListener() {
	//codes..
}
```

### 주의사항

ChangeNotifier는 자동으로 dispose 되지 않아서 수동으로 해줘야 한다.

```dart
@override
void dispose() {
	example.removeListener(myLisstener)
	super.dispose();
}
```

## Provider 인스턴스

### T 타입의 인스턴스 생성

of에 listen이란 parameter가 있는데 이를 false를 할 경우 값이 바뀌어도 안 바뀌게 할 수 있다.

주로 버튼 같은거에 사용할 수 있다.

인스턴스를 생성한 거기 때문에 public 프로버티나 메소드들을 사용하여 값을 가져오거나 값을 변환 시킬 수 있다.

```dart
Provider.of<T>(context)
```

### Extension method

| extension | original |
| --- | --- |
| context.read<T> | Provider.of<T>(context, listen: false) |
| context.watch<T> | Provider.of<T>(context) |
| context.select<T, R>((T value) → R) | 존재 X, 위젯 상으론 Selector가 있음 |

`**context.select<T, R>((T value) → R)`추가 설명**

특정 프로버티의 업데이트만 감지 할 수 있음 **성능에서 매우 유리함**!!

- context.select 예제 코드
    
    ```dart
    context.select<Example, int>((Example value) => value.counter);
    // Example에 다른 프로버티가 바귀어도 counter 프로버티 값이 바뀔때만 rebuild 됨
    ```
    

## Provider

위젯업데이트 필요 없을 때 사용

```dart
Provider<T>(
	create: (context) => T(),
	child: ...
)
```

## ChangeNotifierProvider

ChangeNotifier를 이용하여 매우 Stateful 위젯으로 setState를 해줘야하는 번거로움이 생긴다.

이때 ChangeNotifierProvider를 이용하면 간편해진다.

```dart
ChangeNotifierProvider<T>( //이때 T는 with ChangeNotifier
	create : (context) => T(),
	child: ... //Provider 사용법을 참고하여 사용하면 된다.
);
```

## MultiProvider

ChangeNotifierProvider로 provider를 여러개 적용하려면 child안에 ChangeNotifierProvider 또 child안에 ~

되기 때문에 가독성이 크게 저하 된다. 이 때 MultiProvider란 것을 사용하여 provider를 한꺼번에 적용 할 수 있다.

```dart
MutiProvider(
	providers: [
		ChangeNotifierProvider<T> (
			create: (context) => T(),
		),
		ChangeNotifierProvider<S> (
			create: (context) => S(),
		),
		ChangeNotifierProvider<R> (
			create: (context) => R(),
		),
		...
	],
	child: ...
);
```

## FutureProvider

비동기로 딱 한번만 실행되어 나중에 state를 업데이트 해야할 때 사용한다. (async)

```dart
FutureProvider<T>(
	initalData: ..., // 기본 값
	create: (context) => Future<T>
);
```

## StreamProvider

계속해서 바뀔때 사용된다. (async yield)

```dart
StreamProvider<T>(
	initalData: ..., // 기본 값
	create: (context) => Stream<T>
);
```

## Consumer

```dart
Consumer<T>(
	builder: (BuildContext context, T value, Widget? child) => Widget
)
```

만약 변경을 원하지 않는 위젯이 있는 경우 Consumer constructor에 있는 child parameter를 이용하면 된다. 이때 builder에 있는 child에는 null이 아닌 Widget이 들어오고 이 child는 변경되지 않는다.

```dart
Consumer<T>(
	builder: (BuildContext context, T value, Widget? child) => Widget 
	widget: Widget
)
```

## Slector

```dart
Selector<T, R>(
	selector: (BuildContext context, T value) => R,
	builder: (BuildContext context, R value, Widget? child) => Widget
)
```

 Consumer와 같이 변경하고 싶지 않는 위젯은 child를 이용하면 된다.

<aside>
💡 **Selector**를 사용하는 것은 번거로운 편이기 때문에 보통 **context.select**를 사용한다.

</aside>

## Provider Access

<aside>
💡 주로 Navigator push로 다른 페이지로 넘어갈 경우 Provider가 상위 위젯에 없기 때문에 오류 발생 이때 프로바이더를 엑세스를 할 수 있도록 해줘야함

</aside>

### Anonymous route

ChangeNotifierProvider의 value constructor 사용

단점 자동으로 close하지 않는다. 보통 **계속해서 필요로 할 때** 사용!!

```dart
ChangeNotifierPoriver.value(
	value: context.read<T>(,) //context가 반드시 현재 페이지의 context 여야 함
	child: Widget
);
```

### Named route

1. MaterialApp에서 미리 route를 지정한다.
2. 프로버티로 ChangeNotifer 생성한다
3. rouete 페이지를 ChangeNotifierProvider.value로 프로버이더를 상속시킨다.
4. dispose를 통해서 Provider를 dispose 해준다.

### 예제 코드

```dart
import 'package:flutter/material.dart';
import 'package:learn/counter.dart';
import 'package:learn/show_me_counter.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {

  MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  final Counter _counter = Counter();

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.blue
      ),
      routes: {
        "/": (context) => ChangeNotifierProvider.value(
          value: _counter,
          child: MyHomePage(),
        ),
        "/counter": (context) => ChangeNotifierProvider.value(
          value: _counter,
          child: ShowMeCounter(),
        ),
      },
    );
  }

  @override
  void dispose() {
    _counter.dispose();
    super.dispose();
  }
}

class MyHomePage extends StatelessWidget{
  const MyHomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              child: Text(
                "Show Me Counter",
                style: TextStyle(fontSize: 20),
              ),
              onPressed: () => Navigator.pushNamed(context, '/counter')
            ),
            SizedBox(height: 20,),
            ElevatedButton(
              child: Text(
                "Increment Counter",
                style: TextStyle(fontSize: 20,),
              ),
              onPressed: () => context.read<Counter>().increment(),
            )
          ],
        ),
      ),
    );
  }
}
```

### 예제코드 깃허브로 자세히 보기

[https://github.com/found-cake/ProviderAccess](https://github.com/found-cake/ProviderAccess)

### Generated route

Named route와 매우 많이 유사하다.

- 예제 코드
    
    ```dart
    import 'package:flutter/material.dart';
    import 'package:learn/counter.dart';
    import 'package:learn/show_me_counter.dart';
    import 'package:provider/provider.dart';
    
    void main() {
      runApp(MyApp());
    }
    
    class MyApp extends StatefulWidget {
    
      MyApp({super.key});
    
      @override
      State<MyApp> createState() => _MyAppState();
    }
    
    class _MyAppState extends State<MyApp> {
      final Counter _counter = Counter();
    
      // This widget is the root of your application.
      @override
      Widget build(BuildContext context) {
        return MaterialApp(
          title: 'Flutter Demo',
          debugShowCheckedModeBanner: false,
          theme: ThemeData(
            primarySwatch: Colors.blue
          ),
          onGenerateRoute: (settings) {
            switch(settings.name){
              case '/':
                return MaterialPageRoute(
                  builder: (context) => ChangeNotifierProvider.value(
                    value: _counter,
                    child: MyHomePage(),
                  ),
                );
              case '/counter':
                return MaterialPageRoute(
                  builder: (context) => ChangeNotifierProvider.value(
                    value: _counter,
                    child: ShowMeCounter(),
                  ),
                );
              default:
                return null;
            }
          },
        );
      }
    
      @override
      void dispose() {
        _counter.dispose();
        super.dispose();
      }
    }
    
    class MyHomePage extends StatelessWidget{
      const MyHomePage({super.key});
    
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  child: Text(
                    "Show Me Counter",
                    style: TextStyle(fontSize: 20),
                  ),
                  onPressed: () => Navigator.pushNamed(context, '/counter')
                ),
                SizedBox(height: 20,),
                ElevatedButton(
                  child: Text(
                    "Increment Counter",
                    style: TextStyle(fontSize: 20,),
                  ),
                  onPressed: () => context.read<Counter>().increment(),
                )
              ],
            ),
          ),
        );
      }
    }
    ```
    

## ProxyProvider

Provider를 필요로 하는 Provider가 있을 때 사용함

```dart
ProxyProvider(
	create: () => R//필수는 아님 딱 한번만 실행됨
	update: (BuilderContext context, T value, R? previous){} // 필요로 하는 Provider가 바뀔때마다 실행됨

)
```

## ChangeNotiferProxyProvider

```dart
ChangeProxyProvider<T, R>(
	create: (BuildContext constext) => T(),
	update: (BuildContext context, T t, R r) {}
)
```

<aside>
💡 update를 통해서 create 하지 말것!!

가능하면 **ProxyProvider**를 사용할것!

</aside>

## addPostFrameCallback

state가 변경 또는 rendering 하는 도중 요청할 때 발생하는 에러를 해결하기 위한 방안

이미 그리고 있는데 또 그려달라고 해서 발생하는 문제

```dart
 WidgetBinding.instance!.addPostFrameCallback(context) {});
```
