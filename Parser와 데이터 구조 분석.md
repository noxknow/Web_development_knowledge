## 배경

Parser를 만드는 이론적인 배경은 오토마타 이론이고, 오토마타 이론을 기반으로 컴파일러 이론이 나오게 됐다. 컴파일러를 만드는 여러 계층 중 2가지의 계층을 자주 사용하게 된다.

컴파일러는 구문 분석 → 최적화 → 코드 생성 → 링킹의 과정을 거치게 되는데, 이때의 구문 분석과정이 tokenizer, lexer, parser의 과정이다. 이 결과로 AST(추상 구문 트리)가 나온다. 이러한 점을 생각하고 코드를 작성했다.  → (이때 구문 분석은 더 자세하게 나눈다면 어휘 분석기의 lexical analyzer (tokenizer + lexer) 와 구문 분석기의 syntax analzer (parser)로 나뉜다.)

## 오토마타 이론

- 오토마타 이론의 경우 인터넷에 나와있는 내용도 많고, 깊게 들어가면 매우 어려웠기 때문에 어떤 정보에 집중해야하는지 파악하는 것이 어려웠다. 가장 이해하기 쉬웠던 예시를 가져오면

<p align=center>
  <img src='https://bestowing.github.io/assets/img/2020-09-07/automata1/2.png'>
  <img src='https://bestowing.github.io/assets/img/2020-09-07/automata1/1.png'>
</p>

스위치를 이용했던 예시가 가장 이해하기 편했다. “기계적인 장치를 추상화해서 표현한다.” 이것이 오토마타 이론의 핵심이라고 생각했다. off라는 동그라미에서 push를 따라가면 on이 되고, on에서 push를 따라가면 다시 off로 동그라미와 화살표 만으로 기계장치를 표현할 수 있다는 사실을 이용하여 어떤 기계장치라도 추상화하여 표현한다면 연산능력을 수학적으로 분석할 수 있다는 결론에 도달할 수 있었다.

## lexical analyzer

### **Tokenizer**

> 어떤 대상의 의미있는 요소들을 토큰으로 쪼개는 역할
> 

여기서 토큰이란, "어휘 분석의 단위" 를 의미하는 컴퓨터 용어이다. 이 단위는 단어나 단어구, 문자열 등 보통 **의미있는 단위**로 정한다. 

### **Lexer**

> Tokenizer에 의해 쪼갠 토큰의 의미를 부여하는 역할
> 

Tokenizer + Lexer의 두가지 역할을 합해 Lexcial analyze라고 한다. Lexical Analyze는 의미 있는 조각을 검출하여 토큰을 생성하는 것을 말한다.

## Parser

> `Lexical analyze`되어 `tokenize 된 데이터`를 가지고, 구조적으로 나타낸다.
> 

그리고 데이터를 구조적으로 바꾸는 과정에서, 데이터가 올바른지 검증하는 역할도 수행한다. 이 Parser에 의해 도출된 결과가 AST의 모습을 보인다.

### Parsing

- 소스코드가 컴파일 과정을 거치기 전에, 소스코드 내용을 **파싱**하여 컴파일이 가능한 단위로 만드는 과정이 있다.
- 문장을 그것을 이루고 있는 구성 성분으로 분해하고 그들 사이의 위계 관계를 분석하여 문장의 구조를 결정하는 것 (구문 분석)

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/65db8ec3-2a29-4482-ad70-35a3bcb7f9fe)

## AST

추상 구문 트리(abstract syntax tree, AST), 또는 간단히 구문 트리(syntax tree)는 프로그래밍 언어로 작성된 소스 코드의 추상 구문 구조의 트리이다.

## 정규식

> 문자열에서 특정 문자 조합을 찾기 위한 패턴, 프로그램을 작성할 때는 특성상 일정한 규칙을 가진 텍스트 문자열을 사용하는 경우가 많은데, 이럴 때 정규 표현식을 사용한다.
> 
- 알게된 사실 : 특히 컴파일러의 파서 부분은 이 정규표현식이 반드시 들어간다.

> 파이썬과 자바의 경우 각각의 숫자, 영어, 한글을 판별하는 함수가 존재했지만, 자바스크립트에는 존재하지 않아서 당황했다. 아래와 같은 간단한 정규 표현식은 알아두는 것이 좋을 것 같다.
> 

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/5d5ea1bb-c66b-4034-9c80-116f49b5151b)

```jsx
var pattern1 = /[0-9]/; //숫자

//숫자 체크하고 싶을 때
if (pattern1.test(testStr)) {
	alert("숫자가 포함됩니다."); //true
}
```

### 캡처링 그룹

정규표현식에서 캡쳐링 그룹은 괄호로 둘러싼 영역이다. 캡쳐링 그룹이 있으나 없으나 패턴의 유효성을 검증하는 `test` 메소드의 결과는 같다. 하지만 문자열(String) 객체가 가지고 있는 match 메소드의 결과는 달라진다. 예를 들어 ‘github.com’ 사이트가 복수의 서브도메인을 가진다고 할 때, 서브도메인을 포함한 주소를 표현하는 패턴은 다음과 같다.

```jsx
var url = '[https://rhostem.github.com](https://rhostem.github.com/)'
var domain = /https?:\/\/\w+\.github.com/
url.search(domain) // 0
url.match(domain) // ["[https://rhostem.github.com](https://rhostem.github.com/)", index: 0, ...]
```

서브도메인 영역을 괄호로 둘러싸서 캡쳐링 그룹을 만들면, `match` 메소드의 결과는 달라진다.

```jsx
var url = '[https://rhostem.github.com](https://rhostem.github.com/)'
var domain = /https?:\/\/(\w+)\.github.com/
url.search(domain) // 0
url.match(domain) // ["[https://rhostem.github.com](https://rhostem.github.com/)", "rhostem", index: 0, ...]
```

[search](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search) 메소드는 문자열 내부에서 정규표현식이 매칭이 시작되는 지점을 반환한다. 위의 예제에서는 문자열 처음부터 매칭되기 때문에 결과가 `0`이다.

```jsx
const regex = /\b(?:VAR|SET|FUNC|ADD|SUB|[a-zA-Z_][a-zA-Z_0-9]*|[0-9]+(?:\.[0-9]*)?)\b|[;:,=\{\}]/g;

\b : 단어의 경계를 나타냄
?: : 그룹으로 나눠는 주지만 캡처하지않고 패턴 찾을때만 사용

위의 정규식을 토대로 match했을 때 원래였다면 위의 정규식대로 
```
