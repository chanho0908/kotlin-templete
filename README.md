
* **App**
	* [DependencyInjector](#DependencyInjector)
	* [Application](#Application)
* **Domain**
	* [Constants](#Constants)
	* [정수형 에러처리 Model](#정수형유효성검사)
	* [정수형Enum](#정수형Enum)
 	* [ErrorEnum](#ErrorEnum)
  	* [Regex_["가,나,다"]](#Regex)
  	* [Validate[가,나,다]](#Validate[가,나,다])
  	* [isEmpty](#isEmpty)
  	* [CheckDuplicated](#CheckDuplicated)
  	* [MaxLength](#MaxLength)
* **Presentation**
	* [UiEvent](#UiEvent)
 	* [InputView](#InputView)
    	* [OutputView](#OutputView)
     	* [Retry](#Retry)
      	* [천원단위변환](#천원단위변환)
      	* [첫째자리까지반올림](#첫째자리까지반올림)
      	* [splitByComma](#splitByComma)
## DependencyInjector
```
class DependencyInjector {

    fun injectViewController(): ViewController {
        val inputView = injectInputView()
        val outputView = injectOutPutView()
        val viewModel = injectViewModel()
				return ViewController(inputView, outputView, viewModel)
    }

    private fun injectViewModel(): ViewModel {
        return ViewModel()
    }

    private fun injectInputView() = InputView()
    private fun injectOutPutView() = OutputView()
}
```

## Application
```
fun main() {
    val di = DependencyInjector()
    di.injectViewController()
}
```

## Constants
```
enum class Constants(private val value: String) {
    DELIMITER(",");

    override fun toString(): String = value

}
```

## 정수형유효성검사
```
@JvmInline
value class TryCount (
    val value: Int
){
    init {
        validate()
    }
    private fun validate(){
        require(value > 0){ Error.ONLY_DIGIT }
    }

    companion object{
        private fun from(input: String): TryCount{
            require(input.isNotBlank()) { Error.EMPTY }
            requireNotNull(input.toIntOrNull()){ Error.ONLY_DIGIT }
            return TryCount(input.toInt())
        }

        operator fun invoke(input: String) = from(input)
    }
}
```

## 정수형Enum
```
enum class NumericConstants(val value: Int) {
}
```

## ErrorEnum
```
enum class Error(private val msg: String) {
    EMPTY("빈 이름이 입력 되었어요."),
    INVALID_LENGTH("자동차 이름은 5글자 이하만 허용됩니다."),
    DUPLICATED("자동차 이름은 중복될 수 없어요."),
    INVALID_INPUT("잘못된 입력입니다. 이름은 한/영/숫자로 2명 이상 입력해주세요."),
    ONLY_DIGIT("0 이상의 정수만 입력해주세요.");

    override fun toString(): String = "$ERROR $msg"

    companion object {
        private const val ERROR = "[Error]"
    }

}
```

## Regex
```
^[가-힣,]+
```

# Validate[가,나,다]
```
class CheckWorkerUseCase {
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

## UiEvent
```
sealed interface UiEvent {
    val msg: String
    data class InputCarNames(override val msg: String): UiEvent
    data class InputTryCount(override val msg: String): UiEvent
    data class PlayGame(override val msg: String): UiEvent
    data class DisplayWinner(override val msg: String): UiEvent
}
```

## InputView
```
class InputView {
    fun readItem(): String{
        val input = readLine()
        return input
    }
}
```

## OutputView
```
class OutputView {
    fun printMessage(msg: String){
        println(msg)
    }
}
```

## ViewController
```
class ViewController(
    private val inputView: InputView,
    private val outputView: OutputView,
    private val viewModel: ViewModel
) {
    init {
        checkUiEvent()
    }

    private fun checkUiEvent(){
        //when(val event = viewModel.state.uiEvent){
        //    is UiEvent. -> (event.msg)
        //}
    }

    private fun onUiEventUserInput(msg: String){
        retryWhenNoException {
            outputView.printMessage(msg)
            val input = inputView.readItem()
            //viewModel.
        }
        checkUiEvent()
    }

    private fun onUiEventUserInput(msg: String){
        retryWhenNoException {
            outputView.printMessage(msg)
            val input = inputView.readItem()
            //viewModel.
        }
        checkUiEvent()
    }
```

## Retry
```
fun<T> retryWhenNoException(action : () -> T): T{
    while (true){
        try {
            return action()
        }catch (e: IllegalArgumentException){
            println(e)
        }
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
fun String.splitByComma() = this.split(DELIMITER.toString()).filter { it.isNotBlank() }.map { it.trim() }
```
