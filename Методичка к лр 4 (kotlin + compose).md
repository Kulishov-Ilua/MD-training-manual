# 1. JSON
**`JSON`** (англ. _JavaScript Object Notation_) — текстовый формат обмена данными, основанный на _JavaScript_. Но при этом формат независим от JS и может использоваться в любом языке программирования. [Подробнее](https://habr.com/ru/articles/554274/)

Для эффективного чтения файла JSON (объектная нотация JavaScript) в Kotlin можно использовать популярную библиотеку Gson.

## 1.1 GSON
### 1.1.1 Настройка GSON
Сначала необходимо добавить зависимость в проект:
```kotlin
dependencies {
    implementation ("com.google.code.gson:gson:2.10.1")
}
```
### 1.1.2 Чтению файла JSON в Kotlin с помощью Gson

Чтобы прочитать файл JSON в Kotlin, выполните следующие действия:
1. Импортируйте класс `File` из пакета `java.io` и `Gson` из библиотеки Gson.
2. Создайте `File` объект, указывающий на ваш файл JSON.
3. Преобразует содержимое файла в строку.
4. Используйте Gson для преобразования строки JSON в объект Kotlin или класс данных, соответствующий структуре JSON.
**Пример:**
**data.json**
```json
{
  "name": "Apple",
  "age": 30,
  "email": "apple@example.com"
}
```
Создадим класс данных, соответствующий структуре JSON:
```kotlin
data class User(val name: String, val age: Int, val email: String)
```
Теперь прочтём и проанализируйте файл JSON.
**Main.kt**
```kotlin
import java.io.File
import com.google.gson.Gson

data class User(val name: String, val age: Int, val email: String)

fun main() {
    val filePath = "data.json" 
    val file = File(filePath)

    val jsonString = file.readText()
    val user = Gson().fromJson(jsonString, User::class.java)

    println(user)
}
```
[Подробнее про Gson](https://kotlinandroid.org/kotlin/kotlin-read-json-file/)

### 1.1.3 Чтение сложных Json с помощью Gson
Структура может содержать много объектов, для корректного чтения такого `Json` будем использовать `TypeToken`.  Класс используется для передачи информации о типах во время выполнения программы. Конструктор класса возвращает представленный класс из заданного типа. Прочитаем `Json`:
```json
[
{
  "name": "Apple",
  "age": 30,
  "email": "apple@example.com"
},
{
  "name": "Apple2",
  "age": 30,
  "email": "apple@example.com"
},
{
  "name": "Apple3",
  "age": 30,
  "email": "apple@example.com"
},
]
```
Пример чтения:
```kotlin
import java.io.File
import com.google.gson.Gson

data class User(val name: String, val age: Int, val email: String)

fun main() {
     val filePath = "data.json"
        val file = File(filePath)

        val jsonString = file.readText()

        // Создаем Type для списка пользователей
        val userType = object : TypeToken<List<User>>() {}.type

        // Десериализуем JSON как список
        val users: List<User> = Gson().fromJson(jsonString, userType)

        // Выводим всех пользователей
        users.forEach { println(it) }
}

```

# 2. Многопоточность
## 2.1 Базовые понятия
`Многопоточность` - способность процессора обеспечивать одновременное выполнение нескольких потоков внутри одного процесса

`Процесс` - экземпляр программы во время выполнения / Независимый объект, которому выделены системные ресурсы

`Поток выполнения` - определенный способ выполнения процесса / набор инструкций, которые выполняются последовательно

Потоки при выполнении на одном ядре работают последовательно в рамках одного процесса

`Промежутки между задачами` - время которое нужно диспатчеру, чтобы переключиться между потоками
## 2.2 Зачем нужна многопоточность

Однопоточные программы имеют недостатки в случаях, когда требуется обработка различных данных событий и выполнение блокирующих задач.
В `Android` приложение живет в одном или более процессах, каждый из которых содержит основной поток `Main thread`. Только он отвечает за отрисовку `UI`. Помимо этого, он отвечает за обработку пользовательских действий, и запуск основных компонентов (рис.1).
![Pasted image 20240903202531](https://github.com/user-attachments/assets/7a6085c7-bfe2-4b57-bc59-34ad2c92b62c)

[Рисунок 1](https://yandex.ru/video/preview/8830654701698806592?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024) - Main Thread 
Помимо этого, основной поток может выполнять и другие задачи, но есть ограничения по времени. Каждые 16мс. интерфейс перерисовывается (рис.2).
![Pasted image 20240903202540](https://github.com/user-attachments/assets/e1948da3-6cf8-4ea9-a4ef-2aff6e0a9666)
[Рисунок 2](https://yandex.ru/video/preview/8830654701698806592?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024) - Отрисовка UI
Если поток будет занят другой задачей и не будет успевать отрисовывать UI, кадры будут теряться, а приложение будет тормозить(рис. 3). Если блокировка будет слишком долго, можно  словить ошибку(рис. 4).
![Pasted image 20240903202548](https://github.com/user-attachments/assets/6bc65e7c-aff8-43cc-a8c1-001bac6c0e48)
[Рисунок 3](https://yandex.ru/video/preview/8830654701698806592?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024) - блокирующая операция
[Рисунок 4](https://yandex.ru/video/preview/8830654701698806592?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024) - слишком долгая блокировка потока.
**Многопоточный код:**
1. Эффективное использование ядер
2. Отзывчивость
3. Повышение производительности
## 2.3 Потоки
### 2.3.1 Создание потока
```kotlin
import kotlin.concurrent.thread

fun main(){
	val thread = thread{
		val threadName = Thread.currentThread().name //Вытащить текущий объект потока
		println("Hello world!")
	}
}
```
### 2.3.2 Приостановка потока
Для безопасной остановки потоков предусмотрен `interrupt` подход
```kotlin
fun main(){
	val thread = thread{
		repeat(100000) {
			val thread = Thread.currentThread()
			if(!thread.isInterrupted) {
				//Проискодит работа
			}else{
				//Противный случай
			}
		}	
	}
	//хотим остановить поток
	thread.interrupt()
}
```

### 2.3.3 Ожидание завершения потока
```kotlin
fun main() {
	val thread = thread {
		Thread.sleep(2000)
		println("end of thread")
	}
	//ожидание выполнения потока
	thread.join()
	println("end of main")
}
```
[Многопоточность ШМР2024](https://yandex.ru/video/preview/8830654701698806592?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024)
[[Многопоточность (ШМР 2024)]]
## 2.4 Корутины
`Корутины(сопрограммы)` - это экземпляры приостанавливаемых вычислений

> Концептуально похожа на поток

> Позволяет приостановить выполнение кода и возобновить его позже

> Когда приостановлена, поток не блокируется и готов выполнять другие задачи

В Android сопрограммы помогают управлять долго выполняющимися задачами, которые в противном случае могут заблокировать основной поток и привести к тому, что ваше приложение перестанет отвечать на запросы.

### 2.4.1 Особенности корутин
> Соблюдают принципы Structured cincurrency

> Имеют встроенную поддержку отмены задач

> Поддерживают обработку исключений

> Полностью интегрированы в Android Jetpack

### 2.4.2 CoroutineBuilder
### 2.4.2.1 Launch
Не возвращает результат. Концептуально похоже на поток.

```kotlin
suspend fun main(): Unit = coroutineScope {
	launch {
		delay(1000)
		println("Text 1")
	}
	launch{
		delay(2000)
		println("Text 2")
	}
}
```
### 2.4.2.2 async
Возвращает результат

```kotlin
suspend fun main():Unit = coroutineScope {
	val text1: Deferred<String> = async {
		delay(1000)
		"Text 1"
	}
	val text2: Deferred<String> = async {
		delay(1000)
		"Text 2"
	}
	val result = text1.await() + text2.await()
}
```

### 2.4.2.3 runBlocking
Блокирует поток на котором был запущен до тех пор пока все что находится внутри его action кода (лямбды, которую мы ему передаем)

```kotlin
suspend fun main():Unit = runBlocking{
	val text1: Deferred<String> = async {
		delay(1000)
		"Text 1"
	}
	val text2: Deferred<String> = async {
		delay(1000)
		"Text 2"
	}
	val result = text1.await() + text2.await()
}
```

### 2.4.3 CoroutineScope
Нужен для того, чтобы быть входной точкой в корутины и обозначать ограничительный момент где и сколько по времени корутины могут работать (рис.5).
![Pasted image 20240903203918](https://github.com/user-attachments/assets/7fa1e24a-9e2c-4c5a-a4e3-96c672e4043a)
[Рисунок 5](https://yandex.ru/video/preview/9450652162475930810?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024) - CoroutineScope

> Входная точка в корутины

> Следит за всеми дочерними корутинами

> Имеет возможность отменить работу дочерних корутин

> Содержит CoroutineContext

### 2.4.3.1 Создание `CoroutineScope`
```kotlin
val scope = CoroutineScope(EmptyCoroutineContext)

val job: Job = scope.launch{
	//inside coroutine, this == coroutineScope
}
```

### 2.4.4 Job

> Абстракция над жизненным циклом работы

> Имеет функцию отмены

> Является частью CoroutineContext

Пример:
```kotlin
suspend fun main(): Unit = coroutineScope {
	val job1 = launch {
		delay(1000)
		println("Text 1")
	}
	val job2 = launch{
		delay(2000)
		println("Text 2")
	}
	job1.join()
	job2.join()
	
	println(job1.isActive)
	println(job2.isCompleted)
}
```
Результат (рис.6):
![Pasted image 20240903204139](https://github.com/user-attachments/assets/ec27fbc5-2ca3-4571-afa1-e661d46702ba)
[Рисунок 6](https://yandex.ru/video/preview/9450652162475930810?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024)- Результат

### 2.4.5 Coroutine Context

> Определяет поведение корутин

> Содержит набор элементов

> Поддерживают переопределение элементов

> Дочерние корутины наследуют контекст родителя


### 2.4.6 Отмена корутин

Отмена CoroutineScope отменяет все дочерние корутины(рис.7).

```kotlin
fun CoroutineScope.cancel(cause: CancellationException ?= null)
```

```kotlin
val scope = CoroutineScope(context)

val job1 = scope.launch {/*coroutine1*/}
val job2 = scope.launch {/*coroutine2*/}
val job3 = scope.launch {/*coroutine3*/}

scope.cancel()
```
![Pasted image 20240903204227](https://github.com/user-attachments/assets/6fc51fc1-a6e3-4fc2-a200-0210dffb9a04)
[Рисунок 7](https://yandex.ru/video/preview/9450652162475930810?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024)- отмена CoroutineScope
```kotlin
val scope = CoroutineScope(context)

val job1 = scope.launch {/*coroutine1*/}
val job2 = scope.launch {/*coroutine2*/}
val job3 = scope.launch {/*coroutine3*/}

job2.cancel()
```
![Pasted image 20240903204237](https://github.com/user-attachments/assets/0cac0cef-13fa-4bb9-86a3-8854afa5cc21)
[Рисунок 8](https://yandex.ru/video/preview/9450652162475930810?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024)- отмена job

**При отмене корутин:**

> Отменяются дочерние корутины

> Родитель не отменяется

> После отмены Job больше не может быть использован для создания новых корутин

### 2.4.6.1 Треккинг приостановки

### 2.4.6.1.1 Вручную

```kotlin
val job = launch {
	repeat(100000) {
		if(isActive) {
			//do heavy work
		} else {
			return@launch
		}
	}
}

job.cancel()
```

### 2.4.6.1.2 ensureActive

```kotlin
val job = launch {
	repeat(100000) {
		ensureActive()
	}
}

job.cancel()
```

### 2.4.6.1.1 yield

Не выполнять текущую корутину, а переключиться на что-нибудь другое

```kotlin
val job = launch {
	repeat(100000) {
		yield()
	}
}

job.cancel()
```
[Корутины ШМР2024](https://yandex.ru/video/preview/9450652162475930810?from=tabbar&parent-reqid=1729025307360672-14417298636071586710-balancer-l7leveler-kubr-yp-vla-9-BAL&text=шмр+яндекс+2024)
[[Корутины (ШМР 2024)]]
# 3. Retrofit
`Retrofit` — это библиотека, которая упрощает работу с сетевыми запросами в приложениях на Android. Например, с этим инструментом можно настроить работу приложения с погодой. Разработчик задаёт параметры запросов, которые должны поступать на сервер и забирать данные по температуре в разных городах. Всё остальное происходит автоматически. В итоге пользователь видит актуальные данные каждый раз, когда открывает приложение.
## 3.1 Пример Retrofit запроса
1. Добавим зависисмости
```kotlin
dependencies {
    implementation ("com.squareup.retrofit2:retrofit:2.9.0")
    implementation ("com.squareup.retrofit2:converter-gson:2.9.0")
}
```
2. Создания класса для представление данных:
```kotlin
data class Movie(
	//поля класса (Названия должны совпадать с названием полей тела ответа)
    val name: String,
    ...
)
```
3. Настроим интерфейс API
Программный интерфейс — это договорённость между приложением и сервером, на котором хранятся данные. Он описывает, как приложение будет взаимодействовать с сервером. В этом интерфейсе разработчик определяет методы или функции для сетевых запросов. Например, метод getData() может быть использован для запроса данных с сервера. Приложение может отправлять на сервер разные запросы: получить данные, добавить их, удалить. Чтобы уточнить тип запроса, используют аннотации — специальные пометки в коде. Например, аннотация @GET перед методом указывает, что это GET-запрос для получения информации с сервера. Аннотация @POST укажет на то, что запрос предназначен для отправки данных на сервер.
```kotlin
interface MyApi{
	@GET("относительный адресс") //get запрос и относительный URL
	fun getData(): Call<List<Movie>> // Определение метода для выполнения GET-запроса
	
}
```
4. Создание функции для запроса
```kotlin

fun retrofitRequest(onSuccess: (List<Town>) -> Unit, onFailure: (String) -> Unit) { // при удачном запросе вернем список объектов, при неудачном - строку ошибки
    val retrofit = Retrofit.Builder()
        .baseUrl("базовый адресс")
        .addConverterFactory(GsonConverterFactory.create()) //указываем на использование Gson для работы с Json
        .build()

    val movieApi: MyApi = retrofit.create(MyApi::class.java)
    movieApi.getData().enqueue(object : Callback<List<Movie>> {
	    //получили ответ
        override fun onResponse(call: Call<List<Movie>>, response: Response<List<Movie>>){
	        //если ответ положительный
            if(response.isSuccessful){
                val movie = response.body() // получаем тело ответа
                //если тело не пустое
                if (movie != null) {
                    Log.d(TAG, "Response body: $movie")
                    onSuccess(movie) // вызываем callback с результатом
                } else {
                    onFailure("Response body is null")
                }
            }else{
                Log.e(TAG,"Failed ${response.message()}" )
                onFailure("Request failed: ${response.message()}")
            }
        }
        //не получили ответ
        override fun onFailure(call: Call<List<Ьщмшу>>, t: Throwable) {
            Log.e(TAG,"Network error ${t.message}")
            onFailure("Network error: ${t.message}")
        }
    })
}
```
5. Не забудем добавить разрешение в манифест
```xml
<uses-permission android:name="android.permission.INTERNET"/>
```
6. Выполним запрос асинхронно
Во время асинхронного запроса приложение не блокируется в ожидании ответа от сервера. Пока данные по одним картинкам подгружаются, пользователь может просматривать другие посты. Если запрос успешен, приложение обновляет интерфейс. Например, отображает заголовок поста на экране или картинку. Если запрос завершился ошибкой, приложение может уведомить об этом пользователя. Например, предложит повторить запрос или перезагрузить программу.
```kotlin
retrofitRequest(  
    onSuccess = {  
            movie ->  
        //работаем с ответом
    },  
    onFailure = { errorMessage ->  
        Log.e(TAG, "Error: $errorMessage")  
    }  
)
```
[Retrofit](https://github.com/square/retrofit)
[Больше про Retrofit](https://practicum.yandex.ru/blog/retrofit-na-android/)
[Ещё статейка](https://habr.com/ru/articles/520544/)
[Network ШМР 2024](https://www.youtube.com/live/hocpaSjrS84?si=gAZzEvRoWpeyFaV9)
[[Network (ШМР 2024)#^3b7096]]
# 3. OKHTTP
OkHttp — библиотека и по совместительству HTTP‑клиент с открытым исходным кодом для Java и Kotlin, разработанная Square, которая также создала Retrofit.

OkHttp предоставляет простой, легкий в использовании API для выполнения HTTP‑запросов, включая поддержку протоколов HTTP/1.1 и HTTP/2. Библиотека поддерживает все стандартные методы HTTP и может легко обрабатывать несколько одновременных запросов, а также предоставляет расширенные возможности: кэширование запросов/ответов, объединение подключений в пул (connection pooling), аутентификация и др.

OkHttp — это библиотека более низкого уровня, чем Retrofit. Это означает, что HTTP‑запросы, автоматизированные в Retrofit с помощью аннотаций, придётся писать вручную. Однако в этом и главный плюс библиотеки: она предоставляет более обширный функционал и настройки соединения, что может повысить производительность и сократить использование памяти. К слову, Retrofit под капотом использует OkHttp.

Библиотека разработана как легкая и эффективная, с акцентом на снижение задержек и повышение работоспособности. Это достигается за счет применения различных методов оптимизации, таких как повторное использование соединений, сжатие и конвейеризация.

Преимущества OkHttp:

- Гибкость: Библиотека предоставляет больше контроля над процессом сетевого взаимодействием за счёт дополнительных функций, например, пользовательской обработки запросов и ответов.
    
- Лёгкость: OkHttp — более компактная библиотека, чем Retrofit, что позволяет минимизировать размер используемой приложением памяти.
    
- Кэширование: Библиотека имеет встроенную поддержку HTTP‑кэширования, что может повысить производительность и снизить нагрузку на сеть.
    
- Аутентификация: OkHttp предоставляет гибкий и расширяемый API аутентификации, что упрощает реализацию различных её моделей.
    
- Перехватчики (Interceptors): Это механизм, позволяющий легко настраивать запросы и ответы, а также хороший выбор для приложений, требующих расширенной обработки запросов.
    
- WebSockets: OkHttp обеспечивает встроенную поддержку WebSockets, что позволяет легко реализовать коммуникацию с сервером в режиме реального времени.
## 3.1 Пример OKHTTP запроса
1. Добавим зависимость 
```kotlin
dependencies {
	implementation ("com.squareup.okhttp3:okhttp:4.12.0")
}
```
2. Создания класса для представление данных:
```kotlin
data class Movie(
	//поля класса (Названия должны совпадать с названием полей тела ответа)
    val name: String,
    ...
)
```
3. Создадим функцию для отправки запроса
```kotlin
fun newGetRequest(onSuccess: (List<Movie>) -> Unit, onFailure: (String) -> Unit) {
	// при удачном запросе вернем список объектов, при неудачном - строку ошибки
    
    //создаем клиент  
    val client = OkHttpClient()  
  
    //создаём запрос  
    val request = Request.Builder()  
        .url("адресс запроса")  
        .build()  
  
    //newCall  
    client.newCall(request).enqueue(object : Callback {  
        //если неудача  
        override fun onFailure(call: Call, e: java.io.IOException) {  
            onFailure("Network error")  
        }  
  
        //получили ответ  
        override fun onResponse(call: Call, response: Response) {  
            response.use {  
                //ответ не успешно  
                if (!response.isSuccessful) {  
                    onFailure("Not successful")  
                    throw IOException("Запрос к серверу не был успешен:" +  
                            " ${response.code} ${response.message}")  
                }  
                val myResponse = response.body?.string()//преобразуем тело в строку  

				//Преобразуем строку в список объектов
                val gson = Gson()  
                val moviejson = object : TypeToken<List<Movie>>() {}.type  
                val movie: List<Movie> = gson.fromJson(myResponse, moviejson)  
                //и вернем её
                onSuccess(Movie)  
            }  
        }  
    })  
}
```
4. Не забудем добавить разрешение в манифест
```xml
<uses-permission android:name="android.permission.INTERNET"/>
```
5. Выполним запрос асинхронно
Во время асинхронного запроса приложение не блокируется в ожидании ответа от сервера. Пока данные по одним картинкам подгружаются, пользователь может просматривать другие посты. Если запрос успешен, приложение обновляет интерфейс. Например, отображает заголовок поста на экране или картинку. Если запрос завершился ошибкой, приложение может уведомить об этом пользователя. Например, предложит повторить запрос или перезагрузить программу.

```kotlin
//используем корутину
val scope = rememberCoroutineScope()
    scope.launch {
        newGetRequest(
            onSuccess = {
                movie ->
	                //работаем с ответом
                },
            onFailure = { errorMessage ->
                Log.e(TAG, "Error: $errorMessage")
            }
        )

    }

```
[OKHTTP](https://github.com/square/okhttp)
[Больше про OKHTTP](https://habr.com/ru/articles/717900/)
