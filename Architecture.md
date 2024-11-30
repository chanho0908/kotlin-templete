## InputView
```
import camp.nextstep.edu.missionutils.Console.readLine

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

## UiEvent
```
sealed interface UiEvent {
    val msg: String
    data class OnUiEvent1(override val msg: String): UiEvent
    data class OnUiEvent2(override val msg: String): UiEvent
    data class OnUiEvent3(override val msg: String): UiEvent
    data class OnUiEvent4(override val msg: String): UiEvent
}
```

## ViewModel
```
data class State(
    val uiEvent: UiEvent
) {
    companion object {
        fun create(): State {
            return State(
                UiEvent.OnUiEventInputPurchasePrice()
            )
        }
    }
}

class ViewModel {
    private val _state = State.create()
    val state get() = _state

    fun onCompleteInputPurchasePrice(input: String){

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

    private fun onUiEventUserInput2(msg: String){
        retryWhenNoException {
            outputView.printMessage(msg)
            val input = inputView.readItem()
            //viewModel.
        }
        checkUiEvent()
    }
```



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

## DataSource
```
class ProductDataSource {
    private val baseUrl = "src/main/resources/"

    fun getProduct() = File(baseUrl).useLines { lines ->
        processLines(lines)
    }

    private fun processLines(lines: Sequence<String>) =
        lines.drop(1)
            .map {  }
            .toList()
}
```

# RepositoryImpl
```
class ProductRepositoryImpl(
    private val dataSource: 
) : Repository {
    
}
```

