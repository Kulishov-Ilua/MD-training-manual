#Android #Android_Study #Методичка
В этой методичке мы познакомимся с хранением данных в ваших приложениях на `Android`. 
# 1. Способы хранения информации в `Android` приложениях
Есть несколько типов хранилищ (рис.1).
![[Pasted image 20240904174449.png]]
Рисунок 1 - Типы хринилищ.
Первые два типа в этой методичке рассматриваться не будут. Про них можно узнать  в лекции про хранение данных `ШМР Яндекс 2024`
Рассмотрим хранение данных в базах данных.
# 2. SQLite
В Android есть встроенная реализация пакета баз данных [SQLite](https://www.geeksforgeeks.org/introduction-to-sqlite/) — базы данных SQL с открытым исходным кодом, которая хранит данные в виде текста на устройствах.

SQLite — это автономная, высоконадёжная, встраиваемая, полнофункциональная база данных SQL в открытом доступе. Это самый популярный в мире движок на основе данных. Эта библиотека работает в процессе, и её код находится в открытом доступе. Её можно свободно использовать в любых целях, коммерческих или личных. По сути, это встраиваемый движок на основе данных SQL. SQLite может легко считывать и записывать обычные файлы на диск, поскольку у него нет отдельного сервера, как у SQL. Формат файлов базы данных SQLite является кроссплатформенным, поэтому любой может легко копировать ресурсы данных между 32-разрядными и 64-разрядными последовательностями. Благодаря всем этим функциям он является популярным форматом файлов приложений.

# 3. Room
Библиотека Room предоставляет уровень абстракции поверх SQLite, чтобы обеспечить удобный доступ к базе данных и использовать все возможности SQLite. В частности, Room предоставляет следующие преимущества:

- Проверка SQL-запросов во время компиляции.
- Удобные аннотации, которые сводят к минимуму повторяющийся и подверженный ошибкам шаблонный код.
- Упрощенные пути миграции базы данных.
## 3.2 Реализация Room
### 3.2.1 Обновим свой gradle файл, добавив необходимые зависимости
```kotlin
plugins {  
    id("androidx.room") version "2.6.1" apply false  
    kotlin("kapt")  
}
dependencies {
	val room_version = "2.6.1"
	kapt("androidx.room:room-compiler:$room_version")
    implementation("androidx.room:room-ktx:$room_version")
    implementation("androidx.room:room-runtime:$room_version")
    annotationProcessor("androidx.room:room-compiler:$room_version")
    implementation(libs.androidx.lifecycle.livedata.ktx)
}
```
### 3.2.2 Создадим таблички нашей базы данных
В качестве таблиц бд будем использовать `data class` с анотацией `@Entity`. Колонки бд имеют анотацию `@ColumnInfo(name = "Имя колонки")`, первичный ключ - `@PrimaryKey(autoGenerate = true/*Автогенерация*/)`. Вот пример реализации таблички:
```kotlin
//------------------------------------------------------------------------------  
//Класс для групп задач  
//  Поля:  
//      uid     - Идентификатор  
//      name    - Название  
//      color   - цвет  
//------------------------------------------------------------------------------  
@Entity  
data class GroupTask(  
    @PrimaryKey(autoGenerate = true) val uid: Int?=null,  
    @ColumnInfo(name = "name") val name: String?,  
    @ColumnInfo(name = "color") val color: String  
    )
```
### 3.2.3 Реализация интерфейса для работы с табличками
Для работы с табличками необходимо создавать интерфейсы с анотацией `@Dao`.
`Room` поддерживает все `CRUD` операции
`C - create`
`R - redact`
`U - update`
`D - delete`
Приведем пример интерфейса:
```kotlin
//------------------------------------------------------------------------------  
//Интерфейс Dao для групп  
//  Функции:  
//      insertGroup  - Добавить группу  
//      getAllGroup  - Получить список групп  
//      deleteGroup  - Удалить группу  
//      updateGroup  - Обновить группу  
//------------------------------------------------------------------------------  
@Dao  
interface DaoGroup{  
	//Обновить группу
    @Update  
    fun updateGroup(vararg group: GroupTask)
    //Удалить группу  
    @Delete  
    fun deleteGroup(group: GroupTask)
    //SQL запрос на получение данных  
    @Query("SELECT * FROM GroupTask ")  
    fun getAllGroup(): Flow<List<GroupTask>>  
    //Добавить элемент
    @Insert  
    fun insertGroup(group: GroupTask)  
}
```

### 3.3.4 Реализуем базу данных 
```kotlin
//#################################################################################
//База данных приложения            
//#################################################################################
@Database(
    entities = [
        GroupTask::class,
        ...
    ],
    version = 1,
)
abstract class TrackerDatabase:RoomDatabase(){
    abstract fun groupDao(): DaoGroup
    ...
}
```

## 3.3 Инициализация базы данных
Теперь необходимо инициализировать базу данных в нашем приложении.
```kotlin
//Инициализации базы данных  
val db = Room.databaseBuilder(  
    applicationContext,  
    TrackerDatabase::class.java,  
    name = "Tracker"  
).build()
```
## 3.4 Взаимодействие с базой данных
Создадим несложный интерфейс. В центре экрана будет располагаться кнопка создания новой группы, а под ней список существующих групп. Если нажать на кнопку, то добавиться новая группа. При нажатии на номер группы, группа удаляется, а при нажатии на название - обновляется ее название. 
### 3.4.1 Создание переменных для обновляемого значения и состояний
Создадим глобальные переменные для работы с базой данных в приложении.
```kotlin
  
//флаги обновления групп  
var updateGroup by  mutableStateOf(false)  
var deleteGroup by  mutableStateOf(false)  
//список групп в приложении  
var listGroup = mutableStateOf(emptyList<GroupTask>())  
//новая или обновленная группа  
var updateGroupValue=GroupTask(null,null,"")
```

### 3.4.2 Получим данные с Room
Через переменную `db` мы можем получить доступ к нашим `dao` интерфейсам, через которые нам доступны методы.
```kotlin
//Получить данные с Room  
var groups=db.groupDao().getAllGroup().asLiveData().observe(this){  
    listGroup.value= emptyList()  
    it.forEach {  
        listGroup.value+=GroupTask(it.uid,it.name, it.color)  
    }  
}
```

`.asLiveData()` - позволяет нам обновлять данные в реальном времени.
`.observe(this)` - при каждом изменении переменной будет обновлять значение.

### 3.4.3 Реализуем удаление и обновление групп

> [!NOTE] Важно
> Для того, чтобы не блокировать UI поток, будем использовать корутину

[[Методичка к лр 4 (kotlin + compose)#^cedf88|Подробнее про могопоточность]]

```kotlin
val coroutineScope = CoroutineScope(Dispatchers.IO)//Dispatchers.IO указывает, что эта сопрограмма должна выполняться в потоке, предназначенном для операций ввода-вывода.  
if(updateGroup|| deleteGroup){  
    coroutineScope.launch {  
        if(updateGroup){  
            //если индентификатор не нулевой обновляем, иначе создаем новое  
            if(updateGroupValue.uid!=null){  
                db.groupDao().updateGroup(updateGroupValue)  
                updateGroupValue = GroupTask(null, "", "")  
            }else {  
                db.groupDao().insertGroup(updateGroupValue)  
            }  
            updateGroup=false  
        }  
        if(deleteGroup){  
            db.groupDao().deleteGroup(updateGroupValue)  
            deleteGroup=false  
        }  
    }  
}
```

### 3.4.4 Реализуем интерфейс
```kotlin
Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center){
                LazyColumn {
                    item {
                        Button(onClick = {
                            updateGroupValue=GroupTask(null,"Группа: ${listGroup.value.size+1}","")
                            updateGroup=true
                        }) {
                            Text("Добавить")
                        }
                    }
                    items(listGroup.value){
                        group->
                        Column() {
                            Text(group.uid.toString(), modifier = Modifier.clickable {
                                updateGroupValue = group
                                deleteGroup=true
                            })
                            Text(group.name.toString(), modifier = Modifier.clickable {
                                updateGroupValue=GroupTask(group.uid, name = group.name+")", "")
                                updateGroup=true
                            })
                        }
                    }
                }
            }
```

Подробнее про `Room` в лекции про хранение данных `ШМР Яндекс 2024`
Поробнее про [[Методичка к лр 3 (kotlin + compose)#^36243b|LazyColumn()]]
