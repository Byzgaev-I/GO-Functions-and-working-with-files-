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

## Выполнение домашнего задания

[Cсылка на песочницу](https://go.dev/play/p/NR96ETw_8Kn)

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

[image](
