***Задача 3***. Написание кода.
Цель этого задания закрепить знания о базовом синтаксисе языка. Можно использовать редактор кода на своем компьютере, 
либо использовать песочницу: https://play.golang.org/.

Напишите программу для перевода метров в футы (1 фут = 0.3048 метр). Можно запросить исходные данные у пользователя, а 
можно статически задать в коде. Для взаимодействия с пользователем можно использовать функцию Scanf:

package main

import "fmt"

func main() {
    fmt.Print("Enter a number: ")
    var input float64
    fmt.Scanf("%f", &input)

    output := input * 2

    fmt.Println(output)    
}
Напишите программу, которая найдет наименьший элемент в любом заданном списке, например:

x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}
Напишите программу, которая выводит числа от 1 до 100, которые делятся на 3. То есть (3, 6, 9, …).

В виде решения ссылку на код или сам код.

**Ответ**
1. 
```
package main
import "fmt"

import "math"

  func main() {
  fmt.Print("Введите значение: ")
  var input float64

  fmt.Scanf("%f", &input)
  out := input * float64(0.3048)
  aOut := math.Round(out)
  bOut := fmt.Sprintf("( %.2f)", out)
  fmt.Println("Значеие в метрах:", aOut, bOut )
}
```
```
    /var/run/media/k55/share/share/git    main +1 !2 ?5  go run ./test.go                                                                                                   2 ✘ 
Введите значение: 10
Значеие в метрах: 3 ( 3.05)
 ```
2.
```
package main
  import "fmt"
    func main() {
     x := []int {48,2, 96,86,3,68,57,82,63,70,37,34,83,27,19,97,9,17,1}
     current := 0
     fmt.Println ("Значения : ", x)
     for i, value := range x {
     if (i == 0) {
     current = value
    } else {
     if (value < current){
     current = value
     }
   }
 }
   fmt.Println("Минимальное число : ", current)
}
```
```
   /var/run/media/k55/share/share/git    main +1 !2 ?5  go run ./test.go                                                                                                   2 ✘ 
Значения :  48 2 96 86 3 68 57 82 63 70 37 34 83 27 19 97 9 17 1
Минимальное число :  1
```
3. 
```
package main

 import "fmt"

 func main() {

   for i := 1; i <= 100; i++ {
       if ((i-1)%10) ==0 {
       fmt.Print(i-1, " - ")
        }

       if (i%3) == 0 {
       fmt.Print(i,", ")
        }

       if (i%10) ==0 {
       fmt.Println()
        }
   }
}
```
```
    /var/run/media/k55/share/share/git    main +1 !2 ?5  go run ./test.go                                                                                                   2 ✘ 
0 - 3, 6, 9, 
10 - 12, 15, 18, 
20 - 21, 24, 27, 30, 
30 - 33, 36, 39, 
40 - 42, 45, 48, 
50 - 51, 54, 57, 60, 
60 - 63, 66, 69, 
70 - 72, 75, 78, 
80 - 81, 84, 87, 90, 
90 - 93, 96, 99, 
```



