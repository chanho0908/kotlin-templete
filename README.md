
* **domain**
	* [Constants](#Constants)
	* [Validate [정수형]](#정수형일반유효성검사)
 	* [Validate[가,나,다]](#Validate[가,나,다])
	* [정수형Enum](#정수형Enum)
 	* [ErrorEnum](#ErrorEnum)
  	* [isEmpty](#isEmpty)
  	* [CheckDuplicated](#CheckDuplicated)
  	* [MaxLength](#MaxLength)
* **presentation**
      	* [천원단위변환](#천원단위변환)
      	* [첫째자리까지반올림](#첫째자리까지반올림)
      	* [splitByComma](#splitByComma)

* **Test**
      	* [정수형에러테스트](#정수형에러테스트)
   
## Constants
```
enum class Constants(private val value: String) {

    override fun toString(): String = value

}
```

## 정수형일반유효성검사
```
class Check XX UseCase {
    operator fun invoke(input: String): Int {
        validate(input, winningNumber)
        return input.toInt()
    }

    private fun validate(input: String){
        isEmpty(input)
        isNumeric(input)
    }

    private fun isEmpty(input: String){
        require(input.isNotBlank()) { Error.EMPTY }
    }

    private fun isNumeric(input: String) {
        requireNotNull(input.toIntOrNull()){ Error.ONLY_DIGIT }
    }
}
```

## ErrorEnum
```
enum class Error(private val msg: String) {
    EMPTY("빈 값이 입력 되었어요."),
    DUPLICATED("중복될 수 없어요."),
    ONLY_DIGIT("0 이상의 정수만 입력해주세요."),
    INVALID_RANGE("사이의 숫자로 입력해주세요."),
    INVALID_SIZE("개를 입력해주세요");

    override fun toString(): String = "$ERROR $msg"

    companion object {
        private const val ERROR = "[ERROR]"
    }
}
```

# Validate[가,나,다]
```
class Check XXX UseCase {
    operator fun invoke(input: String): List<String> {
        defaultValidate(input)
        val spliterator = input.splitByComma()
        spliteratorValidate(spliterator)
        return spliterator
    }

    private fun defaultValidate(input: String) {
        isEmpty(input)
        isValidForm(input)
    }

    private fun isEmpty(input: String) {
        require(input.isNotBlank()) { Error.EMPTY }
    }

    private fun isValidForm(input: String) {
        val regex = Regex("^[가-힣,]+")
        require(regex.matches(input)) { Error.INVALID_WORKER }
    }

    private fun spliteratorValidate(spliterator: List<String>) {
        isInvalidWorkerSize(spliterator)
        isDuplicated(spliterator)
        isValidNameLength(spliterator)
    }

    private fun isInvalidWorkerSize(spliterator: List<String>) {
        val range = MIN_WORKER.value..MAX_WORKER.value
        require(spliterator.size in range) { Error.INVALID_WORKER_SIZE }
    }

    private fun isDuplicated(spliterator: List<String>) {
        val map = spliterator.groupingBy { it }.eachCount()
        require(map.all { it.value == 1 }) { Error.DUPLICATED_WORKER }
    }

    private fun isValidNameLength(spliterator: List<String>) {
        require(spliterator.all { it.length <= MAX_NAME_LENGTH.value }) {
            Error.OVER_NAME_SIZE
        }
    }
}
```

## isEmpty
```
private fun isEmpty(input: String) {
   require(input.isNotBlank()) { Error.EMPTY }
}

```

## CheckDuplicated
```
private fun isDuplicated(spliterator: List<String>) {
	val map = spliterator.groupingBy { it }.eachCount()
	require(map.all { it.value == 1 }) { Error.DUPLICATED_WORKER }
}
```

## MaxLength
```
private fun isValidNameLength(spliterator: List<String>) {
	require(spliterator.all { it.length <= MAX_NAME_LENGTH.value }) {
	    Error.OVER_NAME_SIZE
	}
}
```

## 천원단위변환
```
fun Int.convertWithDigitComma(): String = "%,d".format(this)
```

## 첫째자리까지반올림
```
fun Double.convertRoundAtTwoDecimal(): String = "%.1f".format(this)
```

## splitByComma
```
fun String.splitByComma() = this.split(",").filter { it.isNotBlank() }.map { it.trim() }
```

## 정수형에러테스트
```
import lotto.domain.rule.Error
import org.assertj.core.api.Assertions
import org.junit.jupiter.api.BeforeEach
import org.junit.jupiter.api.Test
import org.junit.jupiter.params.ParameterizedTest
import org.junit.jupiter.params.provider.ValueSource

class CheckBonusNumberUseCaseTest {
    private lateinit var 

    @BeforeEach
    fun setUp(){
        
    }

    @Test
    fun `입력값이 비어있을 때 예외 테스트`(){
        Assertions.assertThatThrownBy {
            (emptyList(),"   ")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Error.EMPTY}")
    }

    @Test
    fun `입력값이 음수일 때 예외 테스트`(){
        Assertions.assertThatThrownBy {
            (emptyList(), "-1")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Error.INVALID_RANGE}")
    }

    @ParameterizedTest
    @ValueSource(strings = ["가나다, abc, ###, 2147483648"])
    fun `입력값이 숫자가 아닐 때 테스트`(value: String){
        Assertions.assertThatThrownBy {
            (emptyList(), value)
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Error.ONLY_DIGIT}")
    }

    @Test
    fun `번호가 범위를 초과했을 때 테스트`(){
        Assertions.assertThatThrownBy {
            (emptyList(), "55")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Error.INVALID_RANGE}")
    }

    @Test
    fun `번호가 중복되었을 때 테스트`(){
        Assertions.assertThatThrownBy {
            (listOf(1,2,3,4,5,6),"1")
        }.isInstanceOf(IllegalArgumentException::class.java)
            .hasMessage("${Error.WINNING_DUPLICATED}")
    }
}
```
