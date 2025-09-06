# Домашнее задание «Функции и работа с файлами»  

## Инструкция к выполнению
Написать функцию ReadProcessWrite, которая читает весь текст из файла inputPath, передаёт его в функцию process и записывает результат в файл outputPath.   
В случае ошибки на любом этапе возвращает её вызывающему.  

## Задание
Напишите функцию:  
func ReadProcessWrite(  
inputPath string,  
outputPath string,  
process func(string) (string, error),  
) error  



## Требования к реализации:
- Открыть файл inputPath и прочитать всё содержимое.  
- Использовать defer для закрытия исходного файла (если используется os.Open).  
- Вызвать process, передав прочитанный текст.  
- Создать или перезаписать файл outputPath.  
- Записать в него результат process.  
- Закрыть файл записи с помощью defer(если используется os.OpenFile).  
- При любой ошибке (открытие, чтение, обработка, запись) вернуть её, обернув контекстным сообщением через fmt.Errorf(… %w …).  

## Выполнение домашнего задания

[Cсылка на песочницу](https://go.dev/play/p/NR96ETw_8Kn)

### Базовое использование  
 
 ```go
err := ReadProcessWrite("input.txt", "output.txt", toUpperWrapper)
if err != nil {
    log.Printf("Ошибка: %v", err)
}
```
### Преобразование в верхний регистр

```go
func toUpperWrapper(text string) (string, error) {
    return strings.ToUpper(text), nil
}
```

### Обработка с возможной ошибкой

```go
customProcessor := func(text string) (string, error) {
    if len(text) == 0 {
        return "", fmt.Errorf("пустой текст")
    }
    return strings.ReplaceAll(text, "Go", "Golang"), nil
}
```

### Структура проекта

go-file-processor/  
├── go.mod              # Go модуль  
├── main.go             # Основной файл с функцией ReadProcessWrite   
├── input.txt           # Тестовый входной файл  
├── output.txt          # Результат обработки (создается автоматически)  
└── README.md           # Документация  
  
## Технические детали
### Все ошибки оборачиваются с контекстной информацией:

```go
return fmt.Errorf("ошибка чтения файла %s: %w", inputPath, err)
```

## Локальная версия кода 

 ```go
package main

import (
	"fmt"
	"log"
	"os"
	"strings"
)

func toUpperWrapper(text string) (string, error) {
	return strings.ToUpper(text), nil
}

func ReadProcessWrite(
	inputPath string,
	outputPath string,
	process func(string) (string, error),
) error {
	content, err := os.ReadFile(inputPath)
	if err != nil {
		return fmt.Errorf("ошибка чтения файла %s: %w", inputPath, err)
	}

	processedContent, err := process(string(content))
	if err != nil {
		return fmt.Errorf("ошибка обработки содержимого: %w", err)
	}

	err = os.WriteFile(outputPath, []byte(processedContent), 0644)
	if err != nil {
		return fmt.Errorf("ошибка записи в файл %s: %w", outputPath, err)
	}

	return nil
}

func demonstrateErrors() {
	fmt.Println("\nТестирование обработки ошибок:")
	
	err := ReadProcessWrite("nonexistent.txt", "output.txt", toUpperWrapper)
	if err != nil {
		fmt.Printf("Ошибка с несуществующим файлом: %v\n", err)
	}

	errorProcess := func(text string) (string, error) {
		return "", fmt.Errorf("тестовая ошибка обработки")
	}
	
	err = ReadProcessWrite("input.txt", "output.txt", errorProcess)
	if err != nil {
		fmt.Printf("Ошибка обработки: %v\n", err)
	}

	err = ReadProcessWrite("input.txt", "/root/forbidden/output.txt", toUpperWrapper)
	if err != nil {
		fmt.Printf("Ошибка записи: %v\n", err)
	}
}

func main() {
	err := ReadProcessWrite("input.txt", "output.txt", toUpperWrapper)
	if err != nil {
		log.Printf("Ошибка: %v", err)
		return
	}

	fmt.Println("Файл успешно обработан")

	result, err := os.ReadFile("output.txt")
	if err != nil {
		log.Printf("Ошибка чтения результата: %v", err)
		return
	}

	fmt.Println("Результат обработки:")
	fmt.Printf("%s", string(result))

	demonstrateErrors()
}
 ```

### ВСЕ ТРЕБОВАНИЯ ЗАДАНИЯ ВЫПОЛНЕНЫ:
- Функция ReadProcessWrite реализована с правильной сигнатурой.  
- Чтение файла через os.ReadFile - работает (431 байт прочитано).  
- Обработка содержимого через переданную функцию process - работает.  
- Запись результата через os.WriteFile - работает (файл output.txt создан).  
- Обработка ошибок с fmt.Errorf(...%w...) - все ошибки правильно обернуты.  
- Демонстрация работы с strings.ToUpper в обертке - работает идеально.  
  
### ТЕСТИРОВАНИЕ ОШИБОК:
- Несуществующий файл → правильная ошибка с контекстом.  
- Ошибка в функции обработки → правильно обернута.  
- Ошибка записи в недоступное место → правильно обработана.  

![image](https://github.com/Byzgaev-I/GO-Functions-and-working-with-files-/blob/main/Снимок%20экрана%202025-09-06%20в%2017.26.53.png)
