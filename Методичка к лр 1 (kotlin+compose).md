# 1. Создание проекта
После установки среды разработки научимся создавать новый проект. Для этого перейдём в меню создания проекта. Выберите пункт "Create new project”, либо если среда разработки уже запущена, в верхнем меню пункт File->New->Project появится следующее окно (рис. 1).
![Pasted image 20241013165716](https://github.com/user-attachments/assets/f8bca2e0-3f21-40c7-a2ff-915082490e7f)

							Рисунок 1 – Окно создания проекта
Выберем "Empty Activity", а затем настроим новый проект (рис. 2).
![Pasted image 20241013165808](https://github.com/user-attachments/assets/ebc957a3-e5eb-49c9-833b-e3766f52a1fe)

							Рисунок 2 – Настройка проекта
При создании проекта разработчик должен определить следующие
поля:
1. Название проекта.
2. Имя пакета. Два приложения могут иметь одинаковое название, но
разные имена пакетов, в таком случае Android Studio будет считать эти
приложения разными и сможет установить их рядом. Если сделать наоборот,
сделать 2 приложения с одинаковыми именами пакета, но с разными
названиями проекта, то система просто заменит проект его более новой
версией.
3. Расположение проекта.
4. Минимальная версия SDK. 
5. Язык сборки приложения.
На данном этапа процесс конфигурации проекта завершен. При нажатии
на кнопку Finish среда разработки подгрузит все необходимые библиотеки и
соберет проект.
# 2. Создание Activity
^9ba218
Теперь научимся создавать новую Activity. Для этого найдём директорию, в которой располагается исходная Activity(рис.3). Нажмем правую кнопку мыши и создадим новую Activity (рис. 4-5).
![Pasted image 20241013170058](https://github.com/user-attachments/assets/ea78e503-d85e-45aa-bb75-7886a0aa51ba)

							Рисунок 3 – Директория UI
![Pasted image 20241013170140](https://github.com/user-attachments/assets/455ae18f-da24-4415-b803-d01d3c7a9b02)

							Рисунок 4 – Создание Activity
![Pasted image 20241013170203](https://github.com/user-attachments/assets/3d2ea206-060e-4ae9-b784-832c69048ddd)

							Рисунок 5 – Выбор Activity
# 3. Jetpack Compose
## 3.1 Пришло время познакомиться с compose
Jetpack Compose — это современный набор инструментов для создания собственного пользовательского интерфейса Android. Jetpack Compose упрощает и ускоряет разработку пользовательского интерфейса на Android за счет меньшего количества кода, мощных инструментов и интуитивно понятных API-интерфейсов Kotlin.

Jetpack Compose построен на компонуемых функциях. Эти функции позволяют вам программно определить пользовательский интерфейс вашего приложения, описывая, как он должен выглядеть, и предоставляя зависимости данных, а не сосредотачиваясь на процессе создания пользовательского интерфейса (инициализация элемента, присоединение его к родительскому элементу и т. д.). Чтобы создать составную функцию, просто добавьте аннотацию `@Composable` к имени функции.

```kotlin
@Composable
fun hello(){
}
```
Так например, чтобы создать экран с текстом, нам достаточно написать composable функцию вида:
```kotlin
@Composable
fun hello(){
	Text("Hello world")
}
```
А затем вызвать ее:
```kotlin
class MainActivity : ComponentActivity() {  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        enableEdgeToEdge()  
        setContent {  
           hello()
        }    
    }  
}
```

## 3.2 Рекомпозиция
Когда изменяется состояние приложения или данных, используемых для отображения пользовательского интерфейса, Compose перестраивает только те компоненты, которые зависят от изменённых данных. Это позволяет Compose работать очень эффективно, перестраивая только необходимые части пользовательского интерфейса.
Рекомпозиция в Jetpack Compose — это процесс перестройки пользовательского интерфейса приложения на основе изменений в его состоянии или данных, которые используются для отображения интерфейса. Этот механизм позволяет создавать декларативный пользовательский интерфейс, который может автоматически обновляться и поддерживаться в актуальном состоянии.

Рекомпозиция — ключевой инструмент в Jetpack Compose, который упрощает процесс разработки и поддержки приложений, повышает их производительность и масштабируемость. В связи с этим, важно верно работать с состояниями.
[Если интересно почитать про жизненный цикл](https://developer.android.com/develop/ui/compose/lifecycle?hl=ru)
## 3.3 Состояния
Состояние в приложении — это любое значение, которое может меняться со временем. Это очень широкое определение, охватывающее все: от базы данных Room до переменной в классе.
Все приложения Android отображают пользователю состояние.
Jetpack Compose помогает вам четко указать, где и как вы храните и используете состояние в приложении Android. В этом руководстве основное внимание уделяется связи между состоянием и составными объектами, а также API-интерфейсам, которые Jetpack Compose предлагает для более простой работы с состоянием.

> Важно!!!
> Compose является декларативным, и поэтому единственный способ его обновления — вызвать тот же составной объект с новыми аргументами. Эти аргументы представляют состояние пользовательского интерфейса. Каждый раз, когда состояние обновляется, происходит _рекомпозиция_ . В результате такие вещи, как `TextField` не обновляются автоматически, как в императивных представлениях на основе XML. Компонуемому объекту необходимо явно сообщить новое состояние, чтобы он мог соответствующим образом обновиться.

Компонуемые функции могут использовать API  для хранения объекта в памяти. Значение, вычисленное функцией `remember` сохраняется в композиции во время первоначальной композиции, и сохраненное значение возвращается во время повторной композиции. `remember` можно использовать для хранения как изменяемых, так и неизменяемых объектов.

[`mutableStateOf`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary?hl=ru#mutableStateOf(kotlin.Any,androidx.compose.runtime.SnapshotMutationPolicy)) создает наблюдаемый [`MutableState<T>`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState?hl=ru) , который является наблюдаемым типом, интегрированным со средой выполнения Compose.


>  Важно
> Любые изменения в `value` планируют рекомпозицию любых компонуемых функций, считывающих `value` .

Существует три способа объявить объект `MutableState` в составном объекте:

- `val mutableState = remember { mutableStateOf(default) }`
- `var value by remember { mutableStateOf(default) }`
- `val (value, setValue) = remember { mutableStateOf(default) }`

Эти объявления эквивалентны и предоставляются в качестве синтаксического сахара для различных вариантов использования состояния. Вам следует выбрать тот, который создает наиболее простой для чтения код в компонуемом объекте, который вы пишете.

Чтобы подучить содержимое `mutableStateOf` используем `.value`. Например:
```kotlin
@Composable
fun myFirstColumn() {
	var mytext = remember{mutableStateOf("Hello compose")}
    Column {
        Text(text = mytext.value)
        Text("New column")
    }
}
```
[Больше про состояния](https://developer.android.com/develop/ui/compose/state?hl=ru)

## 3.4 Разметка в compose

^8ddd3c

Jetpack Compose значительно упрощает проектирование и создание пользовательского интерфейса вашего приложения. Compose преобразует состояние в элементы пользовательского интерфейса с помощью:

1. Состав элементов
2. Расположение элементов
3. Рисование элементов

## 3.5 Стандартные компоненты компоновки

Во многих случаях вы можете просто использовать [стандартные элементы макета Compose](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary?hl=ru).
Используйте [`Column`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary?hl=ru#Column(androidx.compose.ui.Modifier,androidx.compose.foundation.layout.Arrangement.Vertical,androidx.compose.ui.Alignment.Horizontal,kotlin.Function1)(androidx.compose.ui.Modifier,androidx.compose.foundation.layout.Arrangement.Vertical,androidx.compose.ui.Alignment.Horizontal,kotlin.Function1)) , чтобы разместить элементы на экране вертикально.
```kotlin
@Composable
fun myFirstColumn() {
    Column {
        Text("Hello compose")
        Text("New column")
    }
}
```
Аналогичным образом используйте [`Row`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary?hl=ru#Row(androidx.compose.ui.Modifier,androidx.compose.foundation.layout.Arrangement.Horizontal,androidx.compose.ui.Alignment.Vertical,kotlin.Function1)(androidx.compose.ui.Modifier,androidx.compose.foundation.layout.Arrangement.Horizontal,androidx.compose.ui.Alignment.Vertical,kotlin.Function1)) для горизонтального размещения элементов на экране. И `Column` , и `Row` поддерживают настройку выравнивания содержащихся в них элементов.

```kotlin
@Composable
fun myFirstRow() {
    Row {
        Text("Hello compose")
        Text("New column")
    }
}
```

Используйте [`Box`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary?hl=ru#Box(androidx.compose.ui.Modifier,androidx.compose.ui.Alignment,kotlin.Boolean,kotlin.Function1)) , чтобы поместить элементы поверх других. `Box` также поддерживает настройку определенного выравнивания содержащихся в нем элементов.
```kotlin
@Composable
fun myFirstBox(){
	Box(){
		Text("1")
		Image(painter = painterResource(R.drawable.my.png), )
	}
}
```
Часто эти строительные блоки — все, что вам нужно. Вы можете написать свою собственную составную функцию, чтобы объединить эти макеты в более сложный макет, подходящий для вашего приложения (рис. 6).
![Pasted image 20241013174656](https://github.com/user-attachments/assets/35d20f01-d6dd-42bf-8f8b-e0f3ec36bde6)

							Рисунок 6 – Разметка
[Подробнее про разметку](https://developer.android.com/develop/ui/compose/layouts/basics?hl=ru)

## 3.6 Модификаторы

^632d9b

Модификаторы позволяют украсить или дополнить составной объект. Примеры возможностей модификаторов:

- Изменение размера, макета, поведения и внешнего вида составного объекта.
- Добавление информации, например меток доступности.
- Обработка ввода пользователя
- Добавление высокоуровневых взаимодействий, например кликабельность элементов, прокручиваемость, перетаскивание или масштабируемость.

Пример добавления модификатора:
```kotlin
@Composable
fun myFirstBox(){
	Box(modifier = Modifier.padding(24.dp).width(50.dp).height(50.dp)){
		Text("1")
		Image(painter = painterResource(R.drawable.my.png), contentDescription="")
	}
}
```
Для `Box` мы установили отступ от других элементов интерфейса, а также ширину и высоту.
>  Важно!!!
> Порядок функций-модификаторов имеет **значение** . Поскольку каждая функция вносит изменения в `Modifier` , возвращаемый предыдущей функцией, последовательность влияет на конечный результат.

Примеры модификаторов:
`fillMaxSize()` - Задать максимальный размер
`fillMaxWidth()` - Задать максимальную ширину
`fillMaxHeight()` - Задать максимальную высоту
`padding()` - Задать отступ объекта
`width()` - Задать ширину
`height()` - Задать высоту
`clickable{}` - Обработать нажатие на элемент
`background()` - Задать фон
[Больше про модификаторы](https://developer.android.com/develop/ui/compose/modifiers?hl=ru)

# 4. Image
Для отображения изображений будем использовать Image()
```kotlin
@Composable
fun Image(
    painter: Painter,
    contentDescription: String?,
    modifier: Modifier = Modifier,
    alignment: Alignment = Alignment.Center,
    contentScale: ContentScale = ContentScale.Fit,
    alpha: Float = _DefaultAlpha_,
    colorFilter: ColorFilter? = null
)
```
Необходимые параметры для добавления изображения:
1. `Painter` — для загрузки изображения из ресурсов будем использовать `painterResource`. В него передадим идентификатор ресурса в качестве параметра в painterResource, и он вернёт `Painter`.
2. `contentDescription` - вам нужно дать описание изображения. Вы можете установить значение null.
Остальные параметры не являются необходимыми.
Пример использования `Image`:
```kotlin
@Composable
fun myFirstBox(){
	Box(modifier = Modifier.padding(24.dp).width(50.dp).height(50.dp)){
		//Text("1")
		Image(painter = painterResource(R.drawable.my.png), contentDescription="")
	}
}
```

# 5. Text
Для отображения текста используем `Text()`:
```kotlin
@Composable
fun myFirstBox(){
	Box(modifier = Modifier.padding(24.dp).width(50.dp).height(50.dp)){
		Text("1")
		//Image(painter = painterResource(R.drawable.my.png), contentDescription="")
	}
}
```

# 6. TextField

^0d76f3

`TextField` позволяет пользователям вводить и изменять текст.
```kotlin
@Composable
fun SimpleFilledTextFieldSample() {
    var text by remember { mutableStateOf("Hello") }//если не использовать remember и mutableStateOf текст изменяться не будет!!! см. п.3.3

    TextField(
        value = text,
        onValueChange = { text = it }, //изменяем text при изменении текста в форме
        label = { Text("Label") } //лейбл текста
    )
}
```

В качестве параметров мы указали текст, с которым работаем, что происходит при изменении и добавили лейбл окну работы с текстом. Результат на рисунке 7.
![Pasted image 20241013182359](https://github.com/user-attachments/assets/fe734775-a5de-4613-b6d1-8b1438c499f9)

							Рисунок 7 – `TextField`
[Больше про TextField](https://developer.android.com/develop/ui/compose/text/user-input?hl=ru)

# Пример решения лабораторной работы 1
```kotlin
class MainActivity : ComponentActivity() {  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        enableEdgeToEdge()  
        setContent {  
	        var email = remember { mutableStateOf("")}  
			var password = remember { mutableStateOf("")}  
			Column(  
			    Modifier.fillMaxWidth(),  
			    horizontalAlignment = Alignment.CenterHorizontally) {  //распологаем все элементы внутри колонки по центру
			    Box(Modifier  
			        .padding(start = 15.dp, top = 15.dp)  //отступ слева и сверху
			        .fillMaxWidth(),
			        contentAlignment = Alignment.CenterStart{// распологаем объекты внутри Box поцентру слева  
 			        Image(painter = painterResource(id = R.drawable.logo),
		 			    contentDescription ="logo",  
			            modifier = Modifier  
			                .height(50.dp)  
			                .width(50.dp))  
			    }  
			    TextField(value = email.value,  
			        onValueChange = {email.value=it},  
			        label = {Text("Email")},  
			        //isError = error.value,  
			        modifier = Modifier.padding(top=25.dp))  
			    TextField(value = password.value,  
			        onValueChange = {password.value=it},  
			        label = {Text("Password")},  
			        //isError = error.value,  
			        //visualTransformation = PasswordVisualTransformation()  
			        ,modifier = Modifier.padding(top=25.dp, bottom = 25.dp))    
			  
			}
		}    
	}  
}
```
