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

[Cсылка на песочницу](https://go.dev/play/)
