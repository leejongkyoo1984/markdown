# 명칭
* 원문 : [Names](https://golang.org/doc/effective_go.html#names)
* 번역자 : MinJae Kwon (@mingrammer)


다른 언어들과 마찬가지로 Go에서도 이름은 중요하다. 이는 심지어 의미에 영향을 줄 수도 있다. 이름의 첫 문자가 대문자인지 아닌지에 따라서 이름의 패키지 밖에서의 노출여부가 결정된다. 그러므로 Go의 네이밍 규칙에 대해 살펴볼 필요가 있다.

## 패키지명


패키지가 임포트되면, 패키지명은 패키지 내용들에 대한 접근자가 된다. 다음과 같이 하면

```go
import "bytes"
```

임포트한 패키지는 `bytes.Buffer`를 사용할 수 있다. 패키지를 사용하는 모든 사람들이 패키지 내용을 참조하기위해 같은 이름을 사용할 수 있다는건 패키지명이 잘 작성되어야 함(짧고, 간결하고, 연상하기 쉬운)을 의미한다. 관례적으로, 패키지명은 소문자, 한 단어로만 부여하며 언더바(_)나 대소문자 혼용에 대한 필요가 없어야한다. 이는 간결함에 치우쳐있는데, 패키지를 사용하는 모든 사람들이 패키지명을 직접 타이핑할 것이기 때문이다 (편의성). 그리고 충돌에 대한 걱정은 하지 말라. 패키지명은 오직 임포트를 위한 이름이다. 이는 모든 소스코드에서 유일할 필요는 없으며, 드문 경우지만 임포트를 하는 패키지가 충돌할때엔 국지적으로 다른 이름을 선택할 수 있다. 어느 경우에나, 임포트에 있는 파일명이 사용될 패키지명을 바로 정하기 때문에 혼란이 드물다.


또 다른 규칙은 패키지명은 소스 디렉토리 이름 기반이라는 것이다. `src/encoding/base64`에 있는 패키지는 `encoding/base64`로 임포트가 된다. `base64`라는 이름을 가지고 있지만, `encoding_base64`나 `encodingBase64`를 쓰지 않는다.


패키지를 임포트하는 입장에서는 패키지 내용을 참조하기위해 패키지명을 사용한다, 그래서 패키지 밖으로 노출되는 이름들은 이름의 지저분함을 피하기위해 이러한 사실을 활용할 수 있다. (`import .`표현을 사용하지 말라. 이는 테스트를 수행중인 패키지의 외부에서 필수적으로 실행해야하는 테스트를 단순화 할 수는 있지만, 그렇지 않을 경우엔 피해야한다.) 예를 들면, `bufio` 패키지에 있는 버퍼 리더는 `BufReader`가 아닌 `Reader`로 불린다. 왜냐하면 사용자는 이를 `bufio.Reader`로 보게되며, 이것이 더 명확하고 간결하기 때문이다. 게다가 임포트된 객체들은 항상 패키지명과 함께 불려지기 때문에 `bufio.Reader`는 `io.Reader`와 충돌하지 않는다. 비슷하게, Go에 존재하는 `ring.Ring`이라는 구조체의 인스턴스를 만드는 함수는 보통은 `NewRing`으로 불릴테지만, `Ring`은 패키지 밖으로 노출된 유일한 타입이며, 패키지가 `ring`으로 불리기 때문에, 이 함수는 그냥 `New`라고 부르고 `ring.New`와 같이 사용한다. 좋은 이름을 위해 이러한 패키지 구조를 활용하라.


또 다른 간단한 예시는 `once.Do`이다. `once.Do(setup)`는 읽기가 쉬우며 `once.DoOrWaitUntilDone(setup)`으로 개선될게 없다. 긴 이름은 좀 더 쉽게 읽는것을 방해한다. 문서에 주석을 다는것이 긴 이름을 사용하는 것보다 더 좋을 것이다.

## 게터 (Getters)


Go는 getters와 setters를 자체적으로 제공하지 않는다. 스스로 getters와 setters를 만들어 사용하면 되는데 이는 전혀 문제될게 없으며 이는 적절하고 일반적인 방법이다. 그러나  getter의 이름에 Get을 넣는건 Go언어 답지도, 필수적이지도 않다. 만약 owner(첫 문자가 소문자이며  패키지 밖으로 노출되지 않는다.)라는 필드를 가지고 있다면 getter 메서드는 GetOwner가 아닌 Owner(첫 문자가 대문자이며, 패키지 밖으로 노출됨)라고 불러야한다. 패키지밖으로 노출하기 위해 대문자 이름을 사용하는 것은 메서드로부터 필드를 식별할 수 있는 훅(hook)을 제공한다. 만약 필요하다면, setter 함수는 SetOwner라고 불릴 것이다. 두 이름 모두 읽기 쉽다.

```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

## 인터페이스명


관례적으로, 하나의 메서드를 갖는 인터페이스는 메서드 이름에 `-er` 접미사를 붙이거나 에이전트 명사를 구성하는 유사한 변형에의해 지정된다. 예를 들면, Reader, Writer, Formatter, CloseNotifier등이 있다.


이런류의 이름들이 있고, 이것들과 이것들을 통해 알 수 있는 함수이름들을 지켜나가는 것은 생산적이다. Read, Write, Close, Flush, String 등등은 각 각의 용법과 의미를 가지고 있다. 혼란을 피하기 위해 같은 용법과 의미를 가지지 않는한 메서드에 이 이름들과 같은 이름을 쓰지 말라. 반대로 말하면, 만약 당신이 구현한 타입에 이미 잘 알려진 타입들이 가지고 있는 메서드들과 같은 의미를 갖는 메서드를 구현하려고 한다면, 같은 이름을 부여하면된다. 문자 변환 메서드를 만든다면 `ToString`이 아닌 `String`을 사용하라.

## 대소문자 혼합


마지막으로, Go에서의 네이밍 규칙은 여러 단어로된 이름을 명명할 때 언더바(_) 대신 대소문자 혼합(`MixedCaps`나 `mixedCaps`)을 사용하는 것이다.