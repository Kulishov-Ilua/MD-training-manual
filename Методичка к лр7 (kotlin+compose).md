# 1. Обработка сложных жестов
В методичке к [[Методичка к лр 2 (kotkin + compose)|2 лабораторной работе]] мы разбирали [[Методичка к лр 2 (kotkin + compose)#^3e292a|обработку нажатия на объект ]] .
Использование модификатора `.clickable{}` удобно, однако имеет ключевой недостаток. Оно не дает возможности определять сложные жесты, такие как двойное нажатие, долгое нажатие. Разберем способ реализации такой обработки.
## 1.1 `.pointerInput(Unit) и detectTapGestures`
Как уже было сказано,, проблема использования `clickable`  состоит в том, что он не может различать различные типы жестов - касания, нажатия, длинные нажатия и двойные нажатия. Для этого уровня точности нам нужно использовать функцию `detectTapGestures()` класса `PointerInputScope`. Эта функция применяется к компоненту через модификатор pointerInput(), который дает нам доступ к PointerInputScope следующим образом:
```kotlin
Modifier
    .pointerInput(Unit) {
        detectTapGestures(
            onPress = { /* обработка нажатия */ },
            onDoubleTap = { /* обработка двойного нажатия */ },
            onLongPress = { /* обработка долгого нажатия */ },
            onTap = { /* обработка простого касания */ }            )
    }
```
[Подробнее](https://metanit.com/kotlin/jetpack/18.1.php)
[pointer-input](https://developer.android.com/develop/ui/compose/touch-input/pointer-input)

# 2. Диалоговые окна
Компонент [`Dialog`](https://developer.android.com/reference/kotlin/androidx/compose/ui/window/package-summary#Dialog(kotlin.Function0,androidx.compose.ui.window.DialogProperties,kotlin.Function0)) отображает всплывающие сообщения или запрашивает ввод данных пользователем на уровне, находящемся над основным содержимым приложения. Он создаёт прерывистый пользовательский интерфейс, чтобы привлечь внимание пользователя.

__Среди вариантов использования диалогового окна следующие:__
- Подтверждение действий пользователя, например, при удалении файла.
- Запрашивает ввод данных пользователем, например, в приложении со списком дел.
- Представление списка вариантов для выбора пользователем, например, выбор страны при настройке профиля.
## 2.1 Диалоговое окно предупреждения
Компонент [`AlertDialog`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#AlertDialog(kotlin.Function0,kotlin.Function0,androidx.compose.ui.Modifier,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,androidx.compose.ui.graphics.Shape,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.unit.Dp,androidx.compose.ui.window.DialogProperties)) предоставляет удобный API для создания диалогового окна в стиле Material Design. `AlertDialog` имеет специальные параметры для работы с конкретными элементами диалогового окна. Среди них:
- `title`: Текст, который появляется в верхней части диалогового окна.
- `text`: Текст, который отображается по центру диалогового окна.
- `icon`: Изображение, которое появляется в верхней части диалогового окна.
- `onDismissRequest`Функция, вызываемая, когда пользователь закрывает диалоговое окно, например, нажав за его пределами.
- `dismissButton`: Составной элемент, который служит кнопкой "Закрыть".
- `confirmButton`: Составной элемент, который служит кнопкой подтверждения.
## 2.2 Пример реализации `AlertDialog`
```kotlin
@Composable  
fun AlertDialogMy(  
    onDismissRequest: () -> Unit,  
    onConfirmation: () -> Unit,  
    dialogTitle: String,  
    dialogText: String,  
    icon: Int,  
) {  
    AlertDialog(  
        icon = {  
            Icon(painterResource(icon), contentDescription = "your  icon")  
        },  
        title = {  
            Text(text = dialogTitle)  
        },  
        text = {  
            Text(text = dialogText)  
        },  
        onDismissRequest = {  
            onDismissRequest()  
        },  
        confirmButton = {  
            Button(  
                onClick = {  
                    onConfirmation()  
                }  
            ) {  
                Text("confirm text")  
            }  
        },  
        dismissButton = {  
            Button (  
                onClick = {  
                    onDismissRequest()  
                }  
            ) {  
                Text("dismiss text")  
            }  
        }    )  
}

```

## 2.3 Пример использования `AlertDialog`
Для отслеживания состояния будем использовать переменную.
```kotlin
var openAlertDialog by remember { mutableStateOf(false) }
```
[[Методичка к лр 1 (kotlin+compose)#^dee60c|Подробнее про состояния]] 

Когда происходит событие, вызывающее диалог, устанавливаем переменную `true` и вызываем диалог.
```kotlin
if(openAlertDialog){  
    AlertDialogSaveScren(
	    onDismissRequest = {
		    openAlertDialog=false  
	    },
	    onConfirmation = {  
	        // действие при подтверждении
	        openAlertDialog=false  
	    },  
        "text title",  
        "text",  
        R.drawable.icon
    )  
}
```
[Подробнее про диалог](https://developer.android.com/develop/ui/compose/components/dialog)