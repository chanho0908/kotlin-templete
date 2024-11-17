
# 예외 처리 템플릿
* **정수형**
  * [정수형 예외 처리](#정수형 예외 처리)
  * [InputController](#inputcontroller)

## 정수형 예외 처리
+ toIntOrNull
  + 숫자가 아닌 문자, Int.MAX_VALUE, 소수 Null 반환
  + 음수는 검증 못하기 때문에 따로 처리 해야한다
```
enum class Exception(private val msg: String) {
    EMPTY_INPUT("빈 값을 입력 하셨습니다."),
    OVER_MAX_NAME_LENGTH("이름은 최대 5글자 입니다."),
    GUIDE_POSITIVE_ONLY("시도 횟수는 1 이상의 정수여야 합니다."),
    GUIDE_INPUT_ONLY_DIGIT("시도 횟수는 숫자만 입력해 주세요."),
    MAX_TRY_AMOUNT("시도 횟수는 최대 1만회입니다.");

    override fun toString(): String = "$ERROR $msg"

    companion object {
        const val ERROR = "[ERROR]"
    }
}
```

```
class CheckUseCase {
    operator fun invoke(input: String){
        isEmpty(input)
        isNumeric(input)
        isOverMaxTryAmount(input)
        isPositive(input)
    }
    // 비어있을 떄
    private fun isEmpty(input: String){
        require(input.isNotBlank()){ Exception.EMPTY_INPUT }
    }
    
    // 숫자인지 
    private fun isNumeric(input: String){
        requireNotNull(input.toIntOrNull()){ Exception.GUIDE_INPUT_ONLY_DIGIT }
    }
    // 특정 범위를 넘어갈 떄         
    private fun isOverMaxTryAmount(input: String){
        require(input.toInt() <= MAX_TRY_AMOUNT.getValue()){ Exception.MAX_TRY_AMOUNT }
    }
    // 음수가 아닌지
    private fun isPositive(input: String) {
        require(input.toInt() > 0) { Exception.GUIDE_POSITIVE_ONLY }
    }
}
```

```
class CheckUseCaseTest {
    private lateinit var useCase: CheckTryAmountUseCase

    @BeforeEach
    fun setUp(){
        useCase = CheckTryAmountUseCase()
    }

    @Test
    fun `입력값이 비어있을 때 예외 테스트`(){
        Assertions.assertThatThrownBy {
            useCase(" ")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Exception.EMPTY_INPUT}")
    }

    @Test
    fun `시도 횟수 입력값이 음수일 때 예외 테스트`(){
        Assertions.assertThatThrownBy {
            useCase("-1")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Exception.GUIDE_POSITIVE_ONLY}")
    }

    @ParameterizedTest
    @ValueSource(strings = ["가나다, abc, ###, 2147483648"])
    fun `시도 횟수 입력값이 숫자가 아닐 때 테스트`(value: String){
        Assertions.assertThatThrownBy {
            useCase(value)
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Exception.GUIDE_INPUT_ONLY_DIGIT}")
    }

    @Test
    fun `시도 횟수가 1만회를 초과할 때 테스트`(){
        Assertions.assertThatThrownBy {
            useCase("100000")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Exception.MAX_TRY_AMOUNT}")
    }
}
```