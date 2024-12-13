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
            println(e.message)
        }
    }
}
```

## ViewModel
```
data class State(
    
) {
    companion object {
        fun create(): State {
            return State(
                UiEvent.OnUiEvent1("abc")
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
            .map { it.splitByComma(). }
            .toList()
}
```

## Repository
```
interface ProductRepository {
    fun getProduct(): Products
}
```

## RepositoryImpl
```
class ProductRepositoryImpl(
    private val dataSource: 
) : Repository {
    
}
```

## DTO
```
data class ProductResponse(
) {
    fun toDomainModel():  {
    }
}
fun List<String>.toProductResponse(): ProductResponse {
```
