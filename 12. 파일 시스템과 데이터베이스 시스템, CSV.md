## **CSV(파일 형식)**

데이터베이스 테이블을 CSV 파일로 저장하고 다뤄야 했기 때문에 가장 먼저 CSV파일 형식에 대해서 알아봤다. 

CSV(comma-separated values)는 몇 가지 필드를 쉼표(,)로 구분한 텍스트 데이터 및 텍스트 파일이다. 확장자는 .csv이며 MIME 형식은 text/csv이다.

오래전부터 스프레드시트나 데이터베이스 소프트웨어에서 많이 쓰였으나 세부적인 구현은 소프트웨어에 따라 다르다.

비슷한 포맷으로는 탭으로 구분하는 TSV나, 반각 스페이스로 구분하는 SSV등이 있으며, 이것들을 합쳐서 CSV라고 부르는 경우가 많다.

### 예시)

---

연도	제조사	모델	설명	가격 <br/>
1997	Ford	E350	ac, abs, moon	3000.00 <br/>
1999	Chevy	Venture "Extended Edition"		4900.00 <br/>
1999	Chevy	Venture "Extended Edition, Very Large"		5000.00 <br/>
1996	Jeep	Grand Cherokee	MUST SELL! <br/>
air,moon roof, loaded	4799.00 <br/>

위의 데이터 표는 다음과 같이 CSV형식으로 표현할 수 있다.

연도,제조사,모델,설명,가격 <br/>
1997,Ford,E350,"ac, abs, moon",3000.00 <br/>
1999,Chevy,"Venture ""Extended Edition""","",4900.00 <br/>
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00 <br/>
1996,Jeep,Grand Cherokee,"MUST SELL! <br/>
air, moon roof loaded",4799.00 <br/>

문장과 문자열은 " "로 감싸서 표현한 것을 볼수 있었다.
-> 하지만 미션에서는 문자열에 " "를 붙이지 않는다.

## **📌 파일 시스템과 데이터베이스 시스템 비교**

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/9d2a1521-c301-4952-927e-6874339b0203)

cf) [https://velog.io/@hsshin0602/CS-지식-데이터베이스-시스템-VS-파일-처리-시스템](https://velog.io/@hsshin0602/CS-%EC%A7%80%EC%8B%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%8B%9C%EC%8A%A4%ED%85%9C-VS-%ED%8C%8C%EC%9D%BC-%EC%B2%98%EB%A6%AC-%EC%8B%9C%EC%8A%A4%ED%85%9C)

> 파일 시스템과 데이터베이스 시스템의 경우 위의 그림으로 한번에 이해했다. 기존 파일 시스템의 단점를 보완하기 위해 데이터베이스 시스템이 만들어졌고, 이 두 시스템의 장단점을 활용하여 적재적소에 사용해야 한다.
> 

## **CLI 인터페이스 구현**

```jsx
"bin" : {
    "run" : "./input.js"
}
```

```jsx
npm i -g 
```

이번에도 cli가 동작하지 않는다. 저번의 경우 명령어와 같은 이름의 파일이 있는 경우 동작을 안한다고 판단했지만 이번 경우로 그런 이유가 아닌 다른 이유가 있다고 생각했다. 이번에는 직접 파일을 열어보기로 했다. 

아래의 첫번째 코드는 저번 미션 13-14에서 package.json 파일에 명령어 mit을 이용하여 input.js를 열기위해 `npm i -g` 로 npm을 전역변수로 다운 받고 생긴 npm 파일이다. 

두번째 코드는 이번 미션을 cli로 구현하기 위해서 명령어는 다르지만 형식은 같게 유지하여 package.json 파일을 만들고 `npm i -g`를 통해 다운 받은 경우다. 

두개의 차이가 보인다. 두번째 코드의 경우 node로 실행시키는 방법이 빠져있다. 이 차이로 인해 계속 실행이 되지 않고 새탭으로만 파일이 열렸던 것이다.

```bash
#!/bin/sh
basedir=$(dirname "$(echo "$0" | sed -e 's,\\,/,g')")

case `uname` in
    *CYGWIN*|*MINGW*|*MSYS*) basedir=`cygpath -w "$basedir"`;;
esac

if [ -x "$basedir/node" ]; then
  exec "$basedir/node"  "$basedir/node_modules/boostcamp_day13-14/input.js" "$@"
else 
  exec node  "$basedir/node_modules/boostcamp_day13-14/input.js" "$@"
fi
```

```bash
#!/bin/sh
basedir=$(dirname "$(echo "$0" | sed -e 's,\\,/,g')")

case `uname` in
    *CYGWIN*|*MINGW*|*MSYS*) basedir=`cygpath -w "$basedir"`;;
esac
```

이 아래의 코드는 window 스크립트를 열었을 때 나오는 코드이다. 마찬가지로 node로 연결하는 부분이 빠져있는걸 확인했다. 

```bash
@ECHO off
GOTO start
:find_dp0
SET dp0=%~dp0
EXIT /b
:start
SETLOCAL
CALL :find_dp0

IF EXIST "%dp0%\node.exe" (
  SET "_prog=%dp0%\node.exe"
) ELSE (
  SET "_prog=node"
  SET PATHEXT=%PATHEXT:;.JS;=;%
)

endLocal & goto #_undefined_# 2>NUL || title %COMSPEC% & "%_prog%"  "%dp0%\node_modules\boostcamp_day13-14\input.js" %*
```

```bash
@ECHO off
GOTO start
:find_dp0
SET dp0=%~dp0
EXIT /b
:start
SETLOCAL
CALL :find_dp0
"%dp0%\node_modules\boostcamp_day16-17\input.js" %*
```

npm을 다운받았던 방식의 차이인지 아니면 다른 요인에 의해 생긴 변수인지 확인하지 못했지만 다음에 CLI를 구현해야 할 때는 어느 부분에서 문제가 생긴건지 바로 알 수 있게 되었다.
