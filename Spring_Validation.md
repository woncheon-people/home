### Validation (검증)

**상품 관리 시스템에 새로운 요구사항을 추가한다.**

- 타입검증 
- 필드검증
   - 상품명 : 필수값이고 공백이 있어선 안된다.
   - 가격: 1000원 이상, 1백만원 이하
   - 수량: 최대 9999
- 특정 필드의 범위를 넘어서는 검증

사용자가 상품 등록 폼에서 정상적으로 입력하였을 때는 검증 로직이 통과되어 정상적으로 실행된다.

![image](https://github.com/yybmion/java-algorithms/assets/113106136/5690308b-710a-4528-9e36-bb4451d88c9f)

사용자가 저장 검증을 실패하였을 때는 고객에게 다시 상품 등록 폼을 보여주고, 어떤 값이 잘못 입력했는지 알려주어야한다.

![image](https://github.com/yybmion/java-algorithms/assets/113106136/cde77879-9858-42ef-aa01-c527e7c7b9b8)

___

### 검증 직접 처리

```java
@PostMapping("/add")
public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes, Model model) {

//검증 오류 결과를 보관
Map<String, String> errors = new HashMap<>();
//검증 로직
if (!StringUtils.hasText(item.getItemName())) {
errors.put("itemName", "상품 이름은 필수입니다.");
}

if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >
1000000) {
errors.put("price", "가격은 1,000 ~ 1,000,000 까지 허용합니다.");
}

if (item.getQuantity() == null || item.getQuantity() >= 9999) {
errors.put("quantity", "수량은 최대 9,999 까지 허용합니다.");
}

//특정 필드가 아닌 복합 룰 검증
if (item.getPrice() != null && item.getQuantity() != null) {

int resultPrice = item.getPrice() * item.getQuantity();

if (resultPrice < 10000) {
errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice);
}

}
//검증에 실패하면 다시 입력 폼으로
if (!errors.isEmpty()) {
model.addAttribute("errors", errors);
return "validation/v1/addForm";
}

//성공 로직
Item savedItem = itemRepository.save(item);
redirectAttributes.addAttribute("itemId", savedItem.getId());
redirectAttributes.addAttribute("status", true);
return "redirect:/validation/v1/items/{itemId}";
}
```

- 검증 오류 메시지를 Map에 저장함
- 따라서 검증로직을 보면 put으로 필드명과 오류 메시지를 담는것을 볼 수 있다.
- 이후 price,quantity,복합 룰들을 검증한다.
- 검증이 실패하면 다시 입력 폼으로 돌아온다. (return "validation/v1/addForm")
- 성공 로직은 검증시 오류가 발생하지 않았을 때 정상적으로 동작하게 만든다.

**이제 add.html 렌더링되는 화면을 보자.**

```java
<div th:if="${errors?.containsKey('globalError')}">
    <p class="field-error" th:text="${errors['globalError']}">전체 오류 메시지</p>
</div>
```

- 오류 메시지는 error 내용이 있을때만 출력한다.
- 하지만 errors가 null이면 어떻게 될까?
- 그래서 errors?. 은 null일때 nullPointerException을 반환하는 대신 null을 반환해준다.

**필드 오류 처리**

```java
<input type="text" th:classappend="${errors?.containsKey('itemName')} ? 'fielderror': _" class="form-control">
```

- classappend 를 사용해서 해당 필드에 오류가 있으면 field-error 라는 클래스 정보를 더해서 폼의
색깔을 빨간색으로 강조한다. 만약 값이 없으면 _ (No-Operation)을 사용해서 아무것도 하지 않는다.

**필드 오류 처리 - 메시지**
```java
<div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">상품명 오류
</div>
```


```java
<div>
    <label for="price" th:text="#{label.item.price}">가격</label>
    <input type="text" id="price" th:field="*{price}"
        th:class="${errors?.containsKey('price')} ? 'form-control
field-error' : 'form-control'"
        class="form-control" placeholder="가격을 입력하세요">
    <div class="field-error" th:if="${errors?.containsKey('price')}"
th:text="${errors['price']}">가격 오류
    </div>
</div>
```

### BindingResult1

```java
@PostMapping("/add")
public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult,
RedirectAttributes redirectAttributes) {

if (!StringUtils.hasText(item.getItemName())) {
bindingResult.addError(new FieldError("item", "itemName", "상품 이름은
필수입니다."));
}

if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >
1000000) {
bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~
1,000,000 까지 허용합니다."));
}

if (item.getQuantity() == null || item.getQuantity() > 10000) {
bindingResult.addError(new FieldError("item", "quantity", "수량은 최대
9,999 까지 허용합니다."));
}

//특정 필드 예외가 아닌 전체 예외
if (item.getPrice() != null && item.getQuantity() != null) {
int resultPrice = item.getPrice() * item.getQuantity();

if (resultPrice < 10000) {
bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은
10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
}
}

if (bindingResult.hasErrors()) {
log.info("errors={}", bindingResult);
return "validation/v2/addForm";
}

//성공 로직
Item savedItem = itemRepository.save(item);
redirectAttributes.addAttribute("itemId", savedItem.getId());
redirectAttributes.addAttribute("status", true);
return "redirect:/validation/v2/items/{itemId}";
}
```

!주의
BindingResult bindingResult 파라미터의 위치는 @ModelAttribute Item item 다음에 와야 한다.

**필드 오류**
```java
 if (!StringUtils.hasText(item.getItemName())) {
bindingResult.addError(new FieldError("item", "itemName", "상품 이름은
필수입니다."));
}
```
- 필드에 오류가 있을 시 오류를 bindingResult에 담아둔다.
- 글로벌 오류는 new FieldError가 아닌 new ObjectError로 작성한다.

addForm.html 수정 (일부)
```java
<div th:if="${#fields.hasGlobalErrors()}">
    <p class="field-error" th:each="err : ${#fields.globalErrors()}"
th:text="${err}">글로벌 오류 메시지</p>
</div>
<div>
    <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
    <input type="text" id="itemName" th:field="*{itemName}"
        th:errorclass="field-error" class="form-control"
placeholder="이름을 입력하세요">
    <div class="field-error" th:errors="*{itemName}">
        상품명 오류
    </div>
</div>
```

- #fields : #fields 로 BindingResult 가 제공하는 검증 오류에 접근할 수 있다.
- th:errors : 해당 필드에 오류가 있는 경우에 태그를 출력한다. th:if 의 편의 버전이다.
- th:errorclass : th:field 에서 지정한 필드에 오류가 있으면 class 정보를 추가한다.

## FieldError, ObjectError

- 사용자 입력 오류 메시지가 화면에 남도록 하자.
- FieldError는 앞서 본 파라미터와 다른 파라미터를 제공할 수 있다.

```java
if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
bindingResult.addError(new FieldError("item", "price", item.getPrice(),
false, null, null, "가격은 1,000 ~ 1,000,000 까지 허용합니다."));
}

//특정 필드 예외가 아닌 전체 예외
if (item.getPrice() != null && item.getQuantity() != null) {
int resultPrice = item.getPrice() * item.getQuantity();
if (resultPrice < 10000) {
bindingResult.addError(new ObjectError("item", null, null, "가격 *
수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
    }
}
```

**파라미터 목록**
- objectName : 오류가 발생한 객체 이름
- field : 오류 필드
- rejectedValue : 사용자가 입력한 값(거절된 값)
- bindingFailure : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
- codes : 메시지 코드
- arguments : 메시지에서 사용하는 인자
- defaultMessage : 기본 오류 메시지

그렇다면 사용자 입력 값을 저장하는 방법은 무엇일까?
바로 rejectedValue가 바로 오류 발생시 사용자 입력 값을 저장하는 필드이다.

그다음 이 저장한 값을 어떻게 나타내는가?

**th:field="*{price}"**
타임리프의 th:field 는 정상 상황에는 모델 객체의 값을 사용하지만, 오류가
발생하면 FieldError 에서 보관한 값을 사용해서 값을 출력한다.

### 오류 코드와 메시지 처리 1
- Error 메시지도 체계적으로 관리하는 것이 좋다.
- 이를 위해 Error 메시지 또한 Properties에서 관리한다.

```properties
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
```

```java
if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
bindingResult.addError(new FieldError("item", "price", item.getPrice(),
false, new String[]{"range.item.price"}, new Object[]{1000, 1000000}, null));
}

//특정 필드 예외가 아닌 전체 예외
if (item.getPrice() != null && item.getQuantity() != null) {
int resultPrice = item.getPrice() * item.getQuantity();
if (resultPrice < 10000) {
bindingResult.addError(new ObjectError("item", new String[]
{"totalPriceMin"}, new Object[]{10000, resultPrice}, null));
    }
}
```

- new String[]{"range.item.price"} 부분은 배열로 만들어지는데, 그 이유는 range.item.price가 없을 때 다음 properties를 고르게 만들기 위해서다.
- new Object[]{1000, 1000000} 는 파라미터를 위한 값이다.

### 오류 코드와 메시지 처리 2

- FieldError , ObjectError 는 다루기 너무 번거롭다.

> 컨트롤러에서 BindingResult 는 검증해야 할 객체인 target 바로 다음에 온다. 따라서
BindingResult 는 이미 본인이 검증해야 할 객체인 target 을 알고 있다.

- BindingResult 가 제공하는 rejectValue() , reject() 를 사용하면 FieldError , ObjectError 를
직접 생성하지 않고, 깔끔하게 검증 오류를 다룰 수 있다.

```java
if (!StringUtils.hasText(item.getItemName())) {
bindingResult.rejectValue("itemName", "required");
}

if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() >
1000000) {
bindingResult.rejectValue("price", "range", new Object[]{1000,1000000}, null);
}

//특정 필드 예외가 아닌 전체 예외
if (item.getPrice() != null && item.getQuantity() != null) {
int resultPrice = item.getPrice() * item.getQuantity();
if (resultPrice < 10000) {
bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
    }
}
```

- field : 오류 필드명
- errorCode : 오류 코드(이 오류 코드는 메시지에 등록된 코드가 아니다. 뒤에서 설명할
messageResolver를 위한 오류 코드이다.)
- errorArgs : 오류 메시지에서 {0} 을 치환하기 위한 값
- defaultMessage : 오류 메시지를 찾을 수 없을 때 사용하는 기본 메시지

.

- 확실히 전에 보았던 FieldError에서 간편해진것을 볼 수 있다.
- 하지만 rejectValue는 어떻게 properties의 전체 이름을 어디에도 명시하지 않았는데 메시지를 가져올 수 있었을까? 

### 오류 코드와 메시지 처리3

오류 코드를 만들 때 다음과 같이 자세히 만들 수도 있고,
- required.item.itemName : 상품 이름은 필수 입니다.
- range.item.price : 상품의 가격 범위 오류 입니다.

또는 다음과 같이 단순하게 만들 수도 있다.
- required : 필수 값 입니다.
- range : 범위 오류 입니다.

이후에 required properties의 오류 메시지를 출력하고 싶은데 required.item.itemName이라는 더욱 구체적인 properties가 있으면 더 구체적인 것이 선택되어 출력된다.

```properties
#Level1
required.item.itemName: 상품 이름은 필수 입니다.
#Level2
required: 필수 값 입니다.
```

- 이를 유연성있게 활용하기 위해 MessageCodesResolver 기능을 제공한다.

### 오류 코드와 메시지 처리4

```java
MessageCodesResolver codesResolver = new DefaultMessageCodesResolver();

@Test
void messageCodesResolverObject() {
String[] messageCodes = codesResolver.resolveMessageCodes("required",
"item");
assertThat(messageCodes).containsExactly("required.item", "required");
}

@Test
void messageCodesResolverField() {
String[] messageCodes = codesResolver.resolveMessageCodes("required","item","itemName", String.class);
assertThat(messageCodes).containsExactly(
"required.item.itemName",
"required.itemName",
"required.java.lang.String",
"required"
);
}
```

FieldError , ObjectError 의 생성자를 보면, 오류 코드를 하나가 아니라 여러 오류 코드를 가질 수 있다.
MessageCodesResolver 를 통해서 생성된 순서대로 오류 코드를 보관한다.

- 객체 오류
ObjectError reject("totalPriceMin")
다음 2가지 오류 코드를 자동으로 생성
   - totalPriceMin.item
   - totalPriceMin

- 필드 오류
- FieldError rejectValue("itemName", "required")
다음 4가지 오류 코드를 자동으로 생성
    - required.item.itemName
    - required.itemName
    - required.java.lang.String
    - required


오류 메시지 출력
타임리프 화면을 렌더링 할 때 th:errors 가 실행된다. 만약 이때 오류가 있다면 생성된 오류 메시지 코드를 순서대로 돌아가면서 메시지를 찾는다. 그리고 없으면 디폴트 메시지를 출력한다.

### 오류 코드와 메시지 처리 5

- 여러가지 level의 properties 메시지를 설정하여 출력 결과를 살펴본다.

1. rejectValue() 호출
2. MessageCodesResolver 를 사용해서 검증 오류 코드로 메시지 코드들을 생성
3. new FieldError() 를 생성하면서 메시지 코드들을 보관
4. th:erros 에서 메시지 코드들로 메시지를 순서대로 메시지에서 찾고, 노출

### 오류 코드와 메시지 처리 6

- Integer 필드에 String을 잘못 입력하면 사용자가 알아보기 힘든 오류가 html에 렌더링 된다.
- 이를 좀더 직관적으로 알아볼 수 있게 고쳐보자.

로그를 확인해보면 BindingResult 에 FieldError 가 담겨있고, 다음과 같은 메시지 코드들이 생성된 것을 확인할 수 있다.
codes[typeMismatch.item.price,typeMismatch.price,typeMismatch.java.lang.Integer,typ
eMismatch]

- 즉, properties에 typeMismatch 또한 등록하면 다른 타입이 입력되었을 때 출력하는 메시지도 정할 수 있다.

```properties
#추가
typeMismatch.java.lang.Integer=숫자를 입력해주세요.
typeMismatch=타입 오류입니다.
```
