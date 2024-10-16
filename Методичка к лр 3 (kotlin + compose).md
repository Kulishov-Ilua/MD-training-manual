# 1. @Composable
Используя `Compose` , вы можете создать свой пользовательский интерфейс, определив набор составных функций, которые принимают данные и создают элементы пользовательского интерфейса. Простым примером является виджет `Greeting` , который принимает `String` и создает виджет `Text`, отображающий приветственное сообщение.
```kotlin
@Composable
fun Greeting(name:String){
	Text("Hello $name")
}
```
[[Методичка к лр 1 (kotlin+compose)#^8ddd3c|Подробнее про разметку]]

* Функция помечена аннотацией @Composable . Все составные функции должны иметь
эту аннотацию; эта аннотация сообщает компилятору Compose, что эта функция
предназначена для преобразования данных в пользовательский интерфейс.
* Функция принимает данные. Компонуемые функции могут принимать параметры,
которые позволяют логике приложения описывать пользовательский интерфейс. В
этом случае наш виджет принимает String , чтобы он мог приветствовать
пользователя по имени.
* Функция отображает текст в пользовательском интерфейсе. Это делается путем
вызова составной функции Text() , которая фактически создает текстовый элемент
пользовательского интерфейса. Составные функции создают иерархию
пользовательского интерфейса, вызывая другие составные функции.
* Функция ничего не возвращает. Функции Compose, которые создают
пользовательский интерфейс, не должны ничего возвращать, поскольку они
описывают желаемое состояние экрана, а не создают виджеты пользовательского
интерфейса.
* Эта функция быстрая, идемпотентная и не имеет побочных эффектов .
[Подробнее про composable функции](https://developer.android.com/develop/ui/compose/mental-model?hl=ru)

# 2. Scaffold
В `Material Design Scaffold` — это фундаментальная структура, обеспечивающая стандартизированную платформу для сложных пользовательских интерфейсов. Он объединяет различные части пользовательского интерфейса, такие как панели приложений и плавающие кнопки действий, придавая приложениям целостный вид.

## 2.1 Пример
Composable `Scaffold` предоставляет простой API, который можно использовать для
быстрой сборки структуры вашего приложения в соответствии с рекомендациями Material
Design. `Scaffold` принимает несколько компонуемых объектов в качестве параметров.
Среди них следующие:
* `topBar` : панель приложения в верхней части экрана.
* `bottomBar` : панель приложения в нижней части экрана.
* `floatingActionButton` : кнопка, которая нависает над правым нижним углом экрана
и которую можно использовать для отображения ключевых действий.
Вы также можете передавать содержимое Scaffold так же, как и в другие
контейнеры. Он передает значение innerPadding в лямбда- content , которое затем
можно использовать в дочерних составных объектах.
В следующем примере (рис.1) представлен полный пример того, как можно реализовать `Scaffold` . Он содержит верхнюю панель приложения, нижнюю панель приложения и кнопку плавающего действия, которая взаимодействует с внутренним состоянием `Scaffold` .
```kotlin
@Composable
fun ScaffoldExample() {
    var presses by remember { mutableIntStateOf(0) }

    Scaffold(
        topBar = { // сверху
            TopAppBar(
                colors = topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.primaryContainer,
                    titleContentColor = MaterialTheme.colorScheme.primary,
                ),
                title = {
                    Text("Top app bar")
                }
            )
        },
        bottomBar = { //снизу
            BottomAppBar(
                containerColor = MaterialTheme.colorScheme.primaryContainer,
                contentColor = MaterialTheme.colorScheme.primary,
            ) {
                Text(
                    modifier = Modifier
                        .fillMaxWidth(),
                    textAlign = TextAlign.Center,
                    text = "Bottom app bar",
                )
            }
        },
        floatingActionButton = { //плавающая кнопка
            FloatingActionButton(onClick = { presses++ }) {
                Icon(Icons.Default.Add, contentDescription = "Add")
            }
        }
    ) { innerPadding -> //основной контент
        Column(
            modifier = Modifier
                .padding(innerPadding),
            verticalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Text(
                modifier = Modifier.padding(8.dp),
                text =
                """
                    This is an example of a scaffold. It uses the Scaffold composable's parameters to create a screen with a simple top app bar, bottom app bar, and floating action button.

                    It also contains some basic inner content, such as this text.

                    You have pressed the floating action button $presses times.
                """.trimIndent(),
            )
        }
    }
}

```
![Pasted image 20241015123358](https://github.com/user-attachments/assets/4755a4b4-6f51-4dc3-9cb7-f3cdc69ed615)

Рисунок 1 - Пример реализации интерфейса с помощью Scaffold
[Подробнее про Scaffold](https://developer.android.com/develop/ui/compose/components/scaffold?hl=ru)
# 3. Bottom Navigation

`Bottom Navigation` - один из способов добавить навигацию в приложение. Далее
рассмотрим, как реализовать данный способ:
0. Добавить зависимость в build.gradle.kts 
```kotlin
dependencies{
	val nav_version = "2.7.7"
	implementation("androidx.navigation:navigation-compose:$nav_version")
}

```
1. Создадим класс для хранения элементов навигации
```kotlin
//############################################################################
//класс для хранения элементов навигации  
//############################################################################

data class BottomNavItem(  
	val label: String, //название элемента навигации  
	val icon: Int, //иконка элемента навигации  
	val route:String, //маршрут элемента навигации  
)
```
 2. Добавим объекты навигации
 ```kotlin
//############################################################################
//объекты навигации  
//############################################################################

object Constants {  
	val BottomNavItems = listOf( //cписок объектов навигации  
		BottomNavItem(  
			label = "Имя1",  
			icon = R.drawable.icon 1 ,  
			route = "route 1 " ),  
		BottomNavItem(  
			label = "Имя2",  
			icon = R.drawable.icon 2 ,  
			route = "route 2 "  
			),  
		BottomNavItem(  
			label = "имя3",  
			icon = R.drawable.icon 3 ,  
			route = "route 3 "  
			),  
		BottomNavItem(  
			label = "Имя4",  
			icon = R.drawable.icon 4 ,
			
			route = "route 4 "  
		)  
	)  
}
```
3. Добавление navController()
Для управления стеком навигации и самой навигацией применяется контроллер
навигации, представленным классом NavHostController. И первым шагом при
добавлении навигации в проект приложения является создание экземпляра
NavHostController:
```kotlin
class MainActivity : ComponentActivity() {
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		enableEdgeToEdge()
		setContent {
			val navController = rememberNavController()
		}
	}
}
```
4. Реализуем NavHostContainer()
NavHost - это специальный компонент, который добавляется в макет
пользовательского интерфейса действия и служит заполнителем для страниц, по
которым будет перемещаться пользователь.
При вызове в NavHost передается объект NavHostController, компонент, который
будет служить начальным экраном/начальным пунктом назначения, и граф
навигации (navigation graph):
```kotlin
@Composable
fun NavHostContainer(
navController: NavHostController, //передаем NavHostController
padding: PaddingValues //передаем отступ
) {
	NavHost(
		navController = navController,
		startDestination = "route1", //Начальное положение(путь домашнего
		экрана)
		modifier = Modifier.padding(paddingValues = padding),
		builder = {
			composable("route1") {
			//Разметка страницы
			}
			composable("route2") {
			//Разметка страницы
			}
			composable("route3") {
			//Разметка страницы
			}
			composable("route4") {
			//Разметка страницы
			}
	})
}

```
5. Реализуем сам BottomBar

```kotlin
//============================================================================
//BottomBar реализация
//============================================================================
@Composable
fun BottomNavigationBar(navController: NavController) {
	BottomNavigation( backgroundColor = MaterialTheme.colorScheme.background//цвет фона
	) {
		val navBackStackEntry by navController.currentBackStackEntryAsState()
		val currentRoute = navBackStackEntry?.destination?.route
		Constants.BottomNavItems.forEach { navItem ->
			BottomNavigationItem(
			selected = currentRoute == navItem.route,
			onClick = {
			navController.navigate(navItem.route) // обработканажатия но объект
			},
			{
				Image(painter = painterResource(navItem.icon),
				contentDescription = navItem.label,
				modifier = Modifier.scale(if(currentRoute ==
				navItem.route) 0.9F else 0.7F)) // иконка объекта
				},
				
				label = {
				Text(text = navItem.label) },
			alwaysShowLabel = false )
		} //подпись объекта
	}
}

```
6. Осталось только вызвать BottomBar() :
```kotlin

class MainActivity : ComponentActivity() {
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		enableEdgeToEdge()
		setContent {
			val navController = rememberNavController()
			Surface() {
				Scaffold (
					bottomBar = {
						BottomNavigationBar(navController =
						navController)
					},
					content = {
						padding -> NavHostContainer(navController
						= navController, padding = padding)
					}
				)
			}
		}
	}
}
```
# 4. Navigation Drawer
Компонент [панели навигации](https://material.io/components/navigation-drawer) — это выдвижное меню, которое позволяет пользователям переходить к различным разделам вашего приложения. Пользователи могут активировать его, проведя пальцем сбоку или коснувшись значка меню.
1. Создадим боковое окно
```kotlin
@Composable
fun screen(){
// Состояние для отображения выбранного элемента меню
    val drawerState = rememberDrawerState(initialValue =
    DrawerValue.Closed)// состояние бокового окна (закрыто по умолчанию)
    val scope = rememberCoroutineScope()
    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = {
            ModalDrawerSheet { /* контент бокового окна */ }
        },
    ) {
        Scaffold(
            floatingActionButton = { //кнопка открытия бокового окна
                ExtendedFloatingActionButton(
                    text = { Text("Show drawer") },
                    icon = { Icon(Icons.Filled.Add, contentDescription =
                    "") },
                    onClick = {
                        scope.launch {
                            drawerState.apply {
                                if (isClosed) open() else close()
                            }
                        }
                    }
                )
            }
        ) { contentPadding ->
            // контент экрана
        }
    }
}
```

2. Добавим примитивную навигацию по строке
```kotlin
@Composable  
fun screen(){  
    var selectedItem by mutableStateOf("route1")  
// Состояние для отображения выбранного элемента меню  
    val drawerState = rememberDrawerState(initialValue =  
    DrawerValue.Closed)// состояние бокового окна (закрыто по умолчанию)  
    val scope = rememberCoroutineScope()  
    ModalNavigationDrawer(  
        drawerState = drawerState,  
        drawerContent = {  
            ModalDrawerSheet {  
                Column(  
                    modifier = Modifier  
                        .fillMaxSize()  
                ) {  
//Заголовок  
                    Text("Menu", style =  
                    MaterialTheme.typography.headlineSmall)  
                    Spacer(modifier = Modifier.height(20.dp))  
// первый элемент  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable { //обрабатываем нажатие  
                            selectedItem = "route1"  
                            scope.launch { drawerState.close() }// закрываем  
                            боковое окно  
                        }){  
                        Icon(painterResource(id = R.drawable.icon1),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))// иконка  
                        первого объекта  
                                Text(  
                                    text = "name1",  
                                    style = MaterialTheme.typography.bodyLarge,  
                                    modifier = Modifier  
                                        .fillMaxWidth()  
                                        .padding(8.dp)  
                                )// название первого объекта  
                    }  
//второй объект  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable {  
                            selectedItem = "route2"  
                            scope.launch { drawerState.close() }  
                        }) {  
                        Icon(painterResource(id = R.drawable.icon2),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))  
                        Text(  
                            text = "name2",  
                            style = MaterialTheme.typography.bodyLarge,  
                            modifier = Modifier  
                                .fillMaxWidth()  
                                .padding(8.dp)  
                        )  
                    }  
//3 объект  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable {  
                            selectedItem = "route3"  
                            scope.launch { drawerState.close() }  
                        }) {  
                        Icon(painterResource(id = R.drawable.icon3),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))  
                        Text(  
                            text = "name3",  
                            style = MaterialTheme.typography.bodyLarge,  
                            modifier = Modifier  
                                .fillMaxWidth()  
                                .padding(8.dp)  
                        )  
                    }  
                }            }        },  
    ) {  
        Scaffold(  
            floatingActionButton = { //кнопка открытия бокового окна  
                ExtendedFloatingActionButton(  
                    text = { Text("Show drawer") },  
                    icon = { Icon(Icons.Filled.Add, contentDescription =  
                    "") },  
                    onClick = {  
                        scope.launch {  
                            drawerState.apply {  
                                if (isClosed) open() else close()  
                            }  
                        }                    }                )  
            }  
        ) { contentPadding ->  
            Column(  
                modifier = Modifier  
                    .fillMaxSize()  
                    .padding(contentPadding)  
                5. Пример решения 3 лабораторной работы  
                    .padding(16.dp),  
                verticalArrangement = Arrangement.Center,  
                horizontalAlignment =  
                androidx.compose.ui.Alignment.CenterHorizontally  
            ) {  
//навигация по строке  
                if(selectedItem=="route1"){  
                    screen1()  
                }  
                if(selectedItem=="route2"){  
                    screen2()  
                }  
                if(selectedItem=="route3"){  
                    screen3()  
                }  
            }  
        }    }}
```
# 5. Пример решения 3 лабораторной работы
```kotlin
var chooseTown by  mutableStateOf("Не выбрано")  
// Состояние для отображения выбранного элемента меню  
var selectedItem by mutableStateOf("towns")  
@SuppressLint("ResourceType")  
@Composable  
fun Greeting() {  
    val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed)  
    val scope = rememberCoroutineScope()  
    ModalNavigationDrawer(  
        drawerState = drawerState,  
        drawerContent = {  
            ModalDrawerSheet {  
                Column(  
                    modifier = Modifier  
                        .fillMaxSize()  
//.padding(16.dp)  
                ) {  
                    Text("Menu", style =  
                    MaterialTheme.typography.headlineSmall)  
                    Spacer(modifier = Modifier.height(20.dp))  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable {  
                            selectedItem = "towns"  
                            scope.launch { drawerState.close() }  
                        }){  
                        Icon(painterResource(id = R.drawable.town),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))  
                        Text(  
                            text = "Towns",  
                            style = MaterialTheme.typography.bodyLarge,  
                            modifier = Modifier  
                                .fillMaxWidth()  
                                .padding(8.dp)  
                        )  
                    }  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable {  
                            selectedItem = "home"  
                            scope.launch { drawerState.close() }  
                        }) {  
                        Icon(painterResource(id = R.drawable.my_town),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))  
                        Text(  
                            text = "home",  
                            style = MaterialTheme.typography.bodyLarge,  
                            modifier = Modifier  
                                .fillMaxWidth()  
                                .padding(8.dp)  
                        )  
                    }  
                    Row(verticalAlignment = Alignment.CenterVertically,  
                        modifier = Modifier.clickable {  
                            selectedItem = "save"  
                            scope.launch { drawerState.close() }  
                        }) {  
                        Icon(painterResource(id = R.drawable.save_towns),  
                            contentDescription = "",  
                            tint = Color.Blue,  
                            modifier = Modifier.padding(end=25.dp))  
                        Text(  
                            text = "Save towns",  
                            style = MaterialTheme.typography.bodyLarge,  
                            modifier = Modifier  
                                .fillMaxWidth()  
                                .padding(8.dp)  
                        )  
                    }  
                }            }        },  
    ) {  
        Scaffold(  
            floatingActionButton = {  
                ExtendedFloatingActionButton(  
                    text = { Text("Show drawer") },  
                    icon = { Icon(Icons.Filled.Add, contentDescription = "")  
                    },  
                    onClick = {  
                        scope.launch {  
                            drawerState.apply {  
                                if (isClosed) open() else close()  
                            }  
                        }                    }                )  
            }  
        ) { contentPadding ->  
            Column(  
                modifier = Modifier  
                    .fillMaxSize()  
                    .padding(contentPadding)  
                    .padding(16.dp),  
                verticalArrangement = Arrangement.Center,  
                horizontalAlignment =  
                androidx.compose.ui.Alignment.CenterHorizontally  
            ) {  
                if(selectedItem=="towns"){  
                    townsScreen()  
                }  
                if(selectedItem=="home"){  
                    myTownScreen()  
                }  
                if(selectedItem=="save"){  
                    saveScreen()  
                }  
            }  
        }    }}  
@Composable  
fun townsScreen(){  
    val array: Array<String> = stringArrayResource(id =R.array.cities_array)  
    array.sort()  
    LazyColumn {  
        items(array){ town->  
            Box(modifier = Modifier  
                .fillMaxWidth()  
                .height(50.dp)  
                .background(if(town== chooseTown) Color.Green else  
                    Color.White)  
                .clickable {  
                    chooseTown = town  
                    selectedItem = "home"  
                }, contentAlignment = Alignment.Center){  
                Text(text = town)  
            }  
        }    }}  
@Composable  
fun myTownScreen(){  
    Column(horizontalAlignment = Alignment.CenterHorizontally, modifier =  
    Modifier.fillMaxSize()) {  
        Text("Выбранный город")  
        Box(modifier = Modifier.fillMaxSize(), contentAlignment =  
        Alignment.Center){  
            Text(text = chooseTown)  
        }  
    }}  
@Composable  
fun saveScreen() {  
    Box(  
        modifier = Modifier.fillMaxSize(), contentAlignment =  
        Alignment.CenterEnd  
    ) {  
        Text(text = "Это абсолютно безполезная вкладка")  
    }  
}
```
