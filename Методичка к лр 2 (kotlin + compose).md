# 1. Работа с разной ориентацией экрана
Для определения текущей ориентации устройства можно использовать `LocalConfiguration.current.orientation` и в зависимости от его значения отображать вертикальную либо горизонтальную разметку. Пример:
```kotlin
@Composable  
fun labScreenCompose(navController: NavController){  
    val configuration = LocalConfiguration.current  
    val orientation = configuration.orientation  
  
    if (orientation != Configuration.ORIENTATION_LANDSCAPE) {  
        verticalScreenHome() //верстка для вертикальной ориентации 
    } else {  
        horizontalScreenHome()  //верстка для горизонтальной ориентации
       }  
  
}
```
[[Методичка к лр 1 (kotlin+compose)#^8ddd3c|Знакомство с разметкой]]

# 2. Обработка нажатия на объект
В прошлой методичке рассматривались [[Методичка к лр 1 (kotlin+compose)#^632d9b|модификаторы]], для обработки нажатия можно использовать один из них: `clickable`. Пример:
```kotlin  
@Composable  
fun clicker(){  
    var count = remember{mutableStateOf(0)}  
    Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center){  
        Box(Modifier  
            .width(100.dp)  
            .height(100.dp)  
            .background(Color.Green)  
            .clickable {    
				count.value++  
            })  
    }  
}
```

# 3. Button
Кнопки — это фундаментальные компоненты, которые позволяют пользователю запускать определенное действие. 
## поверхность API
- `onClick` : функция, вызываемая, когда пользователь нажимает кнопку.
- `enabled` : если этот параметр имеет значение false, кнопка становится недоступной и неактивной.
- `colors` : экземпляр `ButtonColors` , определяющий цвета, используемые в кнопке.
- `contentPadding` : отступ внутри кнопки.
Пример реализации кнопки:
```kotlin
@Composable
fun FilledButtonExample() {
    Button(onClick = { /*Действие*/ }) {
        Text("Нажми на кнопку")
    }
}
```
[Подробнее про кнопки](https://developer.android.com/develop/ui/compose/components/button?hl=ru)
# 4. TextField (isError)
В качестве параметров [[Методичка к лр 1 (kotlin+compose)#^0d76f3|TextField]] можно передавать состояние `isError` если состояние `isError = true`, то форма будет выделена как ошибка (рис.1).

![Pasted image 20241013203926](https://github.com/user-attachments/assets/a6916524-58af-499d-bb1b-98fb78413883)
							Рисунок 1 – Состояние ошибки
Пример реализации:
```kotlin
@Composable
fun SimpleFilledTextFieldSample() {
    var text by remember { mutableStateOf("Hello") }//если не использовать remember и mutableStateOf текст изменяться не будет!!! см. п.3.3
	var errorFlag = remember{mutableStateOf(false)}
    TextField(
        value = text,
        onValueChange = { text = it }, //изменяем text при изменении текста в форме
        label = { Text("Label") }, //лейбл текста
        isError = errorFlag.value
    )
}
```

# 5. Работа с массивом строк в ресурсах
Для начала создадим массив строк в ресурсах. Для этого создадим файл ресурсов в директории `values`(рис.2)
![Pasted image 20241013204551](https://github.com/user-attachments/assets/2d9c0885-6490-43e2-839e-841e7c77b60d)

							Рисунок 2 – Создания файла ресурсов
Пример заполнения массива строк:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <string-array        name="users">  
        <item>"Ivan 11111"</item>  
        <item>"Petr 222"</item>  
        <item>"Vasya 333"</item>  
        <item>"Natalya Pechota"</item>  
        <item>"Vite Viyti"</item>  
    </string-array>
</resources>
```
Теперь научимся копировать массив из ресурсов в массив в коде используя `stringArrayResource`,  в который необходимо передать идентификатор массива:
```kotlin
@Composable 
fun func(){
	val array: Array<String> = stringArrayResource(id =R.array.users )
}
```
# 6. Запускаем новую Activity
Создавать новую `Activity` мы научились в прошлой [[Методичка к лр 1 (kotlin+compose)#^9ba218|методичке]].
Теперь научимся её запускать. Для этого нам необходимо получить локальный контекст, создать новый `itent` и передать в него контекст и `Activity`, а затем запустить его. Пример:
```kotlin
@Composable
fun newActivity(){
	val context = LocalContext.current //получим локальный контекст
	val intent = Intent(context, MainActivity3::class.java) // Создаем Intent для запуска SecondActivity  
	context.startActivity(intent) // Запускаем Activity
}
```
# 7. Пример реализации 2 лабы

```kotlin
class MainActivity : ComponentActivity() {  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        enableEdgeToEdge()  
        setContent {  
            labScreenCompose
        }      
	}
}	

@Composable  
fun labScreenCompose(){  
    val configuration = LocalConfiguration.current  
    val orientation = configuration.orientation  
  
    if (orientation != Configuration.ORIENTATION_LANDSCAPE) {  
        verticalScreenHome()  
    } else {  
        horizontalScreenHome()  
       }  
  
}

@Composable  
fun verticalScreenHome(){  
    val context = LocalContext.current  
    var email = remember { mutableStateOf("")}  
    var password = remember { mutableStateOf("")}  
    val array: Array<String> = stringArrayResource(id =R.array.users )  
    Column(  
        Modifier  
            .padding(top = 50.dp)  
            .fillMaxWidth(), horizontalAlignment = Alignment.CenterHorizontally) {  
  
        Image(painter = painterResource(id = R.drawable.logo), contentDescription ="logo",  
            modifier = Modifier.height(150.dp))  
        TextField(value = email.value,  
            onValueChange = {email.value=it},  
            label = {Text("Email")},  
            isError = error.value,  
            modifier = Modifier.padding(top=25.dp))  
        TextField(value = password.value,  
            onValueChange = {password.value=it},  
            label = {Text("Password")},  
            isError = error.value,  
            visualTransformation = PasswordVisualTransformation()  
            ,modifier = Modifier.padding(top=25.dp, bottom = 25.dp))  
        Button(onClick = {  
            println("${email.value} ${password.value}")  
            if(array.contains("${email.value} ${password.value}")) {   
                val intent = Intent(context, MainActivity3::class.java) // Создаем Intent для запуска SecondActivity  
                context.startActivity(intent) // Запускаем Activity  
                error.value=false  
            }else{  
                error.value=true  
            }  
        }) {  
            Text(text = "Проверить")  
        }  
  
    }}  
  
@Composable  
fun horizontalScreenHome(){  
    val context = LocalContext.current  
    val array: Array<String> = stringArrayResource(id =R.array.users )  
    var email = remember { mutableStateOf("")}  
    var password = remember { mutableStateOf("")}  
    Column(  
        Modifier  
            .fillMaxWidth(),  
        horizontalAlignment = Alignment.CenterHorizontally) {  
        Box(  
            Modifier  
                .padding(start = 15.dp, top = 15.dp)  
                .fillMaxWidth(), contentAlignment = Alignment.CenterStart){  
            Image(painter = painterResource(id = R.drawable.logo), contentDescription ="logo",  
                modifier = Modifier  
                    .height(50.dp)  
                    .width(50.dp))  
        }  
        TextField(value = email.value,  
            onValueChange = {email.value=it},  
            label = {Text("Email")},  
            isError = error.value,  
            modifier = Modifier.padding(top=25.dp))  
        TextField(value = password.value,  
            onValueChange = {password.value=it},  
            label = {Text("Password")},  
            isError = error.value,  
            visualTransformation = PasswordVisualTransformation()  
            ,modifier = Modifier.padding(top=25.dp, bottom = 25.dp))  
        Button(onClick = {  
            if(array.contains("${email.value} ${password.value}")) {   
                val intent = Intent(context, MainActivity3::class.java) // Создаем Intent для запуска SecondActivity  
                context.startActivity(intent) // Запускаем Activity  
            error.value=false  
        }else{  
            error.value=true  
        } }) {  
            Text(text = "Проверить")  
        }  
  
    }}
```
