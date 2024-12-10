
* **domain**
	* [Constants](#Constants)
	* [Validate [정수형]](#정수형일반유효성검사)
 	* [Validate[가,나,다]](#Validate[가,나,다])
	* [정수형Enum](#정수형Enum)
 	* [ErrorEnum](#ErrorEnum)
  	* [MaxLength](#MaxLength)
  	* [상태관리인터페이스](#sealedinterface)
  	* [일주일](#일주일)
  	* [월](#월)

* **presentation**<br>
      	* [천원단위변환](#천원단위변환)<br>
      	* [첫째자리까지반올림](#첫째자리까지반올림)<br>
      	* [splitByComma](#splitByComma)<br>
        * [Map<String, List<String>](#Map<String, List<String>)<br>
       

## 월
```
enum class Month(val value: Int, val endDay: Int) {
    JANUARY(1, 31),
    FEBRUARY(2, 28),
    MARCH(3, 31),
    APRIL(4,30),
    MAY(5, 31),
    JUNE(6, 30),
    JULY(7, 31),
    AUGUST(8, 31),
    SEPTEMBER(9, 30),
    OCTOBER(10, 31),
    NOVEMBER(11, 30),
    DECEMBER(12, 31);

    companion object {
        fun from(month: Int): Month {
            return when (month) {
                1 -> JANUARY
                2 -> FEBRUARY
                3 -> MARCH
                4 -> APRIL
                5 -> MAY
                6 -> JUNE
                7 -> JULY
                8 -> AUGUST
                9 -> SEPTEMBER
                10 -> OCTOBER
                11 -> NOVEMBER
                12 -> DECEMBER
                else -> throw IllegalArgumentException("잘못된 월을 입력했어요.")
            }
        }
    }
}
```

## 일주일
```
enum class Week(val value: String, val order: Int) {
    MonDay("월", 1),
    TUESDAY("화", 2),
    WEDNESDAY("수", 3),
    THURSDAY("목", 4),
    FRIDAY("금", 5),
    SATURDAY("토", 6),
    SUNDAY("일", 7);

    companion object {
        fun weeklyList() = listOf(
            MonDay.value,
            TUESDAY.value,
            WEDNESDAY.value,
            THURSDAY.value,
            FRIDAY.value,
            SATURDAY.value,
            SUNDAY.value
        )

        fun from(day: String): Week {
            return when(day){
                MonDay.value -> MonDay
                TUESDAY.value -> TUESDAY
                WEDNESDAY.value -> WEDNESDAY
                THURSDAY.value -> THURSDAY
                FRIDAY.value -> FRIDAY
                SATURDAY.value -> SATURDAY
                else -> SUNDAY
            }
        }

        fun from(day: Int): Week {
            return when(day){
                MonDay.order -> MonDay
                TUESDAY.order -> TUESDAY
                WEDNESDAY.order -> WEDNESDAY
                THURSDAY.order -> THURSDAY
                FRIDAY.order -> FRIDAY
                SATURDAY.order -> SATURDAY
                else -> SUNDAY
            }
        }
    }
}
```

## sealedinterface
```
sealed interface Category {
    val kind: String
    val menu: List<String>
    val key: Int

    data class Japanese(
        override val kind: String = "일식",
        override val menu: List<String> = listOf(
            "규동", "우동", "미소시루", "스시", "가츠동", "오니기리", "하이라이스", "라멘", "오코노미야끼"
        ),
        override val key: Int = 1
    ) : Category
}
```
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

## Validate[가,나,다]
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

## Map<String, List<String>
```
fun onCompleteInputNotEatMenu(input: String, idx: Int) {
	val notEatMenu = notEatMenuUseCase(input)
	val coach = _state.coach[idx]
	val currentNotEatMenu = _state.notEatMenu.toMutableMap()
	currentNotEatMenu[coach] = _state.notEatMenu.getOrDefault(coach, emptyList()) + notEatMenu
	_state = _state.copy(notEatMenu = currentNotEatMenu)
}
```
