# 프로그래밍 요구사항 
1. indent(들여쓰기) depth를 2단계에서 1단계로 줄여라.
2. 메소드의 크기가 최대 10라인을 넘지 않도록 구현한다.
3. else를 사요하지 마라

## Step 2. 문자열 덧셈 계산기

### 2-1. 요구사항 정의서
1. `쉼표(,) 또는 콜론(:)`을 구분자로 가지는 문자열을 전달하는 경우
   + 구분자를 기준으로 분리한 각 숫자의 합을 반환
   

2. `구분자(쉼표, 콜론),커스텀 구분자`를 지정할 수 있다. 
   + 커스텀 구분자는 문자열 앞부분의 “//”와 “\n” 사이에 위치하는 문자를 커스텀 구분자로 사용한다.

   
3. 문자열 계산기에 숫자 이외의 값 또는 음수를 전달하는 경우 
   + `RuntimeException 예외를 throw`한다.

### 2-2 기능 정의서 
1. StringValidation
   + 숫자 하나를 문자열로 입력할 경우 해당 숫자를 반환한다.(예 : “1”)
   + 음수를 전달할 경우 RuntimeException 예외가 발생해야 한다. (예 : “-1,2,3”)

2. DefaultSpliter
   + 숫자 두개를 컴마(,) 구분자로 입력할 경우 두 숫자의 합을 반환한다.(예 : “1,2”)   
   + 구분자를 컴마(,) 이외에 콜론(:)을 사용할 수 있다. (예 : “1,2:3” => 6)

3. PatternSpliter
   + “//”와 “\n” 문자 사이에 커스텀 구분자를 지정할 수 있다. (예 : “//;\n1;2;3” => 6)   
 


### 클래스 명세서
| 클래스명 | 용도 | 
|:---:|:---:| 
|StringValidation | 빈 문자열 또는 null 값을 입력할 경우 0을 반환, 음수 입력시 `RuntimeException`|
|SpliterManager| Spliter를 구현한 클래스를 리턴|
|PatternSpliter| 정규식을 통한 split|
|PatternSpliter| 콤마(,) 또는 콜론(:)으로 split|
|StringCalculator| 분리된 문자열을 합을구하는 클래스|


## Step 3. 로또

### 요구사항정의
+ 로또 구입 금액을 입력하면 `구입 금액에 해당하는 로또`를 발급해야한다.
+ 로또 1장의 가격은 `1000원`이다.
   + 14000원 구매시 총 14개의 로또가 발급 되야한다.
+ 지난주 당첨 번호를 사용자가 입력한다.
+ 당첨 통계를 출력한다.
   + 3개 5000원
   + 4개 50000원
   + 5개 1500000원
   + 6개 2000000000원
+ 판매된 수에 따른 총 수익률은 0.35입니다.
   + 계산식
   + 5000/14000 = 0.35

### 기능사항 정의

- [x] 하나의 로또 번호를 생성한다.
- [x] 로또 번호는 `최대 6자리`이다.
- [x] 사용자가 `구입 금액을 입력시` 1000원당 한장의 로또를 생성해야한다. 
- [x] 입력된 금액이 모자란경우 Exception 
- [x] 사용자가 입력한 금액의 1000 단위마다 6개의 번호 출력 
- [x] 1부터 45까지의 랜덤한 숫자를 하나만 생성한다.
- [x] 중복되지 않는 랜덤한 숫자 6개를 출력한다. 
- [x] 사용자 발급한 로또로 당첨 통계를 계산한다. 
  - 갯수별 금액 산정 
  - 총 수익률 계산
- [x] 사용자가 입력한 문자열을 로또번호로 변경 
- [x] 정규식 패턴 유틸 
- [x] 생성된 로또번호와 지난주 로또번호의 갯수가 동일한지 확인

### 변화할 수 있는 부분이 어떤 것인가?
- 생성하는 부분이 변경될 수 있다. 
- 입력하는 부분이 변경될 수 있다.

### 리팩토링 대상 확인
- [x] 테스트를 위한 생성 메소드를 `LottoNumbers`에 넣기
  - [정적팩토리 메소드란](https://velog.io/@ljinsk3/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C)
- [x] 불안전한 객체를 `private`으로 막은 뒤 항상 "중복을 제외한 6개의 숫자"를 만족하는 객체가 되도록 만들기
- [x] LottoPapers 싱글톤 사용 자제 (객체의 생성으로 변환)
- [x] assert 사용보다  throw로 변경 
- [x] private static final Pattern PATTERN_FOO = Pattern.compile("your_regex");
- [x] `LottoNumberService.java` stream trim 을 사용 
- [x] `stream().filter(/*do stuff*/).count()`


### 무엇을 이해했는가?
1. 객체와의 관계을 `역할`과`책임`으로 나누어진다.
2. 객체의 명칭은 의미보다는 `책임`에 의해 이름이 만들어진다.
3. `일급컬렉션`은 상속보다는 합성을 이용하는 것이 용이하다
    - [상속보다 합성을 이용하라](https://tecoble.techcourse.co.kr/post/2020-05-18-inheritance-vs-composition/)
#### before

```java
public class LottoPapers extends AbstractList<LottoNumbers> 
```

#### after
```java
public class LottoPapers {
	private final List<LottoNumbers> list = new LottoNumbers();
} 
```

4. `테스트 픽스처`
    - [테스트 픽스처](https://jojoldu.tistory.com/611?category=1011740)
5. 인터페이스는 `행위`에 집중하여 설계하는 것이 좋다고 생각한다.
    - 이전 방식은 클래스에게 `여러개의 행위에 대한 책임`을 위임한다면
    - 아래 방식은 `하나의 클래스에 하나의 행위`를 위임하고 있다.
#### before
```java
public class AutoMachineValidation implements MachineValidation{
	@Override
	public boolean isOverFlow(int size) {
		if (size == LOTTO_NUMBER_MAX) {
			return false;
		}
		return true;
	}
}

```
#### after
```java
public interface MachineValidator {

    boolean validate();
}

public class OverflowValidator implements MachineValidator {

    @Override
    public boolean validate() {
        // ... overflow 관련 로직
    }
}
```

## Step4. 로또 2등
+ 사용자의 입력을 추가 "보너스 볼을 입력해 주세요.

## 당첨 통계
+ 5개 일치, 보너스 볼 일치(30000000원) - 0개

## 기능사항 정의

- [x] findMatchLottoNumber 로직변경
  - 기존 로직 수정 
    - LottoPapers.java findMatchLottoNumber() 
      - 로또 상금에 해당하는 대상을 찾는 로직 
- [x] LottoNumbers match() 
  - 기존 로직 수정 
    - 유저의 지난주 로또번호와 보너스볼을 가지고서 보너스볼을 가지고 있는지 확인 
    - 보너스볼 유무 및 매칭되는 볼 숫자를 리턴한다.
  - 추가된 로직 
    - matchCount 
      - 6개의 로또번호를 탐색하여 매칭되는 수를 리턴 
    - matchBonusBall
      - 6개의 로또번 중 보너스 볼과 매칭되는 경우 true 아니면 false를 리턴
- [x] LottoNumbers getList 추가 
  - 대상의 데이터는 `불변` 하도록 처리 
- [x] ViewException 추가 
  - 화면에서 처리한 로직 Exception의 상위 클래스 생성 
    - 각각의 오류에 대한 Exception 클래스 생성 
- [x] 문자열 상수 정의
  - 문자열을 코드에 넣었던 형태를 상수로 전환
- [x] LottoNumbersService 삭제 
  - 행위의 의한 범위가 아닌 하나의 대한 인터페이스는 의미 없어보이므로 삭제 
  - 해당 로직을 LottoNumbers.from으로 변경
- [x] Money `명령-쿼리` 
  - 값의 조회와 함께 수정은 오류를 예측하기 어려워지므로 `하나의 일`만 하도록 처리
- [x] `보너스볼` 추가 
  - 보너스 볼을 `LottoNumber`를 상속하여 번호의 범위가 1 ~ 45 범위 이도록 변경 
- [x] LottoNumber 번호의 범위 추가 
  - 로또번호는 1 ~ 45까지의 범위이어야한다.


### 생각 이상으로 객체지향은 어렵다.

```java
import java.util.Collections;
import java.util.Map;
import java.util.stream.Collectors;

public interface InputView {
	// 화면 호출 메시지
	message();

	// 입력
	enter();
}

public class MoneyView implements InputView {
	@Override
	public void message() {
		System.out.println("구입금액을 입력하세요");
	}

	@Override
	public String enter() {
		return sc.nextLine();
	}
}

public class BonusBallView implements InputView {
	@Override
	public void message() {
		System.out.println("보너스 볼을 입력해 주세요");
	}

	@Override
	public String enter() {
		return sc.nextLine();
	}
}

public interface Config {
	Map.Entry<InputView, Object> getFactory();
}

public class FactoryConfig implements Config {
	private Map<InputView, Object> factory

	public FactoryConfig() {
		factory = new HashMap<>();
		factory.put(new MoneyView(), new CreateOperation());
		factory.put(new BonusBallView(), new BonusBallOperation());
	}

	public Map.Entry<InputView, Object> getFactory() {
		return Collections.unmodifiableMap(factory.entrySet());
	}
}

public class LottoManagement {

	private FacotryConfig facotryConfig;

	public LottoManagement(FactoryConfig factoryConfig) {
		this.factory = facotryConfig;
	}

	public void run() {
		List<InputView> views = facotryConfig.getView();

		for (Map.Entry<InputView, Object> factory : getFactory) {
			InputView view = factory.getKey();
			Object operation = factory.getValue();
			view.message();
			String enter = view.enter();
			operation.start(enter);
		}
	}
}

public interface CreateOperation {
	void start(Money money);
}

public interface BonusBallOperation {
	void start(LottoNumbers lottoNumbers, BonusBall bonusBall);
}

public interface LottoOperation extends CreateOperation, BonusBallOperation {
	void start(String enter);
}

public class Main {

	LottoManagement lottoManagement = new LottoManagement(new FactoryConfig());
	lottoManagement.run();
}


```

## 알게된점 
1. EnumMap<Rank, Integer> winningResults; (순서를 보장한다.)
2. 테스트 코드 역시 유지보수의 대상이기 때문에 잘못된 테스트는 오히려 개발을 방해할 수도 있습니다.
3. [(이펙티브 자바 3판) 3장 - 모든 객체의 공통 메서드, equals는 일반 규약을 지켜 재정의하라](https://perfectacle.github.io/2018/11/26/effective-java-ch03-item10-equals-method/)

# Step5. 5단계 - 로또(수동)

## 요구사항 추가 
+ 수동으로 구매할 로또 수를 입력해 주세요.
+ 수동으로 구매할 번호를 입력해 주세요.
+ 수동으로 3장, 자동으로 11개를 구매했습니다.


## 기능사항 정의 

화면
 - 입력
   - [x] 수동으로 구매할 로또 수를 입력 
   - [x] 수동으로 구매할 번호를 입력 화면
 - 출력
   - [x] 수동 구매수와 자동 구매수를 출력해야한다.
 - 기능 
   - [x] 로또번호를 수동으로 입력
제약사항 
 - [x] 입력된 로또번호가 적거나 중복인경우 예외 처리
 


