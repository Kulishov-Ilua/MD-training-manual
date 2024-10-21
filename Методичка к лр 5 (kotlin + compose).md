# 1. Получим данные с сервера и передадим в LazyCoumn()
В методичке 4 рассматривались способы [получения данных с сервера](https://github.com/Kulishov-Ilua/MD-training-manual/blob/main/Методичка%20к%20лр%204%20(kotlin%20%2B%20compose).md) и  [парсинг json-файлов](https://github.com/Kulishov-Ilua/MD-training-manual/blob/main/Методичка%20к%20лр%204%20(kotlin%20%2B%20compose).md). Воспользуемся одним из способов, получим данные с сервера и распарсим их в массив.
## 1.1 Получение данных с сервера
Для получения данных с сервера воспользуемся ранее написанной функцией, использующей `OKHTTP` и вызовем ее из нашей `@Composable` функции:
```kotlin
@Composable
fun screen(){
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

}
```

## 1.2 Сохранения полученного списка объектов
В блоке `onSuccess` нам предоставляется возможность работать с ответом, полученным с сервера. Так как мы будем передавать список объектов в `LazyColumn()` и получать его с сервера, нам необходимо явно указать, что список будет изменяться, и его изменение нужно отслеживать. Для чего это нужно, и как это реализовать подробно описано в [методичке к лабораторной работе 1](https://github.com/Kulishov-Ilua/MD-training-manual/blob/main/Методичка%20к%20лр%201%20(kotlin%2Bcompose).md).  Добавим список в функцию и запишем в него полученный ответ:
```kotlin
@Composable
fun screen(){
var movies by remember {  
    mutableStateOf(emptyList<Movie>())  
}
	//используем корутину
val scope = rememberCoroutineScope()
scope.launch {
    newGetRequest(
        onSuccess = {
            movie ->
	            movies=movie
            },
        onFailure = { errorMessage ->
                Log.e(TAG, "Error: $errorMessage")
        }
    )

}

}
```
## 1.3 Передадим данные в `LazyColumn()`
В [методичке к лабораторной работе 3](https://github.com/Kulishov-Ilua/MD-training-manual/blob/main/Методичка%20к%20лр%203%20(kotlin%20%2B%20compose).md) мы разбирали, как работать с "ленивыми" списками. Добавим в нашу функцию `LazyColumn()` с именами фильмов из ранее полученного массива:
```kotlin
@Composable
fun screen(){
	var movies by remember {  
	    mutableStateOf(emptyList<Movie>())  
	}
		//используем корутину
	val scope = rememberCoroutineScope()
	scope.launch {
	    newGetRequest(
	        onSuccess = {
	            movie ->
		            movies=movie
	            },
	        onFailure = { errorMessage ->
	                Log.e(TAG, "Error: $errorMessage")
	        }
	    )
	
	}
	LazyColumn {  
		//отображаем список элементов
	    items(movies){ movie->  
              //передали массив и сверстаем элемент интерфейса	              
              Text(text = movie.name)
	    }    
	}

}
```
# 2. Обработка нажатия на элемент списка.
Как и обработку нажатия на любой компонент интерфейса, обработку нажатия на элемент списка можно реализовать с помощью модификатора `clickable`. Его мы разбирали в [методичке к лабораторной работе 2](https://github.com/Kulishov-Ilua/MD-training-manual/blob/main/Методичка%20к%20лр%202%20(kotlin%20%2B%20compose).md). Добавим обработку нажатия на элемент списка:
```kotlin
@Composable
fun screen(){
	var movies by remember {  
	    mutableStateOf(emptyList<Movie>())  
	}
		//используем корутину
	val scope = rememberCoroutineScope()
	scope.launch {
	    newGetRequest(
	        onSuccess = {
	            movie ->
		            movies=movie
	            },
	        onFailure = { errorMessage ->
	                Log.e(TAG, "Error: $errorMessage")
	        }
	    )
	
	}
	LazyColumn {  
		//отображаем список элементов
	    items(movies){ movie->  
              //передали массив и сверстаем элемент интерфейса	              
              Text(text = movie.name, modifier = Modifier.clickable{
	              //Ваше действие
	              })
	    }    
	}

}
```
