# Display

`fmt::Debug` выглядит не очень компактно и красиво, поэтому полезно настраивать внешний вид информации, которая будет напечатана. Это можно сделать реализовав типаж [`fmt::Display`] вручную, который использует маркер `{}` для печати. Его реализация выглядит следующим образом:

```rust
// Импортируем (с помощью `use`) модуль `fmt`, чтобы мы могли его использовать.
use std::fmt;

// Определяем структуру, для которой будет реализован `fmt::Display`.
// Это простая кортежная структура c именем `Structure`, которая хранит в себе `i32`.
struct Structure(i32);

// Чтобы была возможность использовать маркер `{}`
// `типаж (trait) fmt::Display` должен быть реализован вручную
// для данного типа.
impl fmt::Display for Structure {
    // Этот типаж требует реализацию метода `fmt` с данной сигнатурой:
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Записываем первый элемент в предоставленный выходной поток: `f`.
        // Возвращаем `fmt::Result`, который показывает выполнилась операция
        // успешно или нет. Обратите внимание на то, что синтаксис `write!`
        // похож на синтаксис `println!`.
        write!(f, "{}", self.0)
    }
}
```

Вывод `fmt::Display` может быть более чистым, чем `fmt::Debug`, но может быть проблемой для стандартной библиотеки (`std`). Как нестандартные типы должны отображаться? Например, если `std` предоставляет единый стиль вывода для `Vec<T>`, каким этот вывод должен быть? Любой из этих двух?

- `Vec<path>`: `/:/etc:/home/username:/bin` (разделитель `:`)
- `Vec<number>`: `1,2,3` (разделитель `,`)

Нет, потому что не существует идеального стиля вывода для всех типов, поэтому `std` не может его предоставить. `fmt::Display` не реализован для `Vec<T>` или для других обобщённых контейнеров. Для этих случаев подойдёт `fmt::Debug`.

Это не проблема, потому что для любых новых *контейнеров*, типы которых не обобщённые, может быть реализован `fmt::Display`.

```rust,editable
use std::fmt; // Импортируем `fmt`

// Структура, которая хранит в себе два числа.
// Вывод типажа `Debug` добавлен для сравнения с `Display`.
#[derive(Debug)]
struct MinMax(i64, i64);

// Реализуем `Display` для `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Используем `self.номер`, чтобы получить доступ к каждому полю структуры.
        write!(f, "({}, {})", self.0, self.1)
    }
}

// Объявим структуру с именованными полями, для сравнения
#[derive(Debug)]
struct Point2D {
    x: f64,
    y: f64,
}

// По аналогии, реализуем `Display` для Point2D
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Обращаться к полям структуры Point2D будет по имени
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}

fn main() {
    let minmax = MinMax(0, 14);

    println!("Сравниваем структуры:");
    println!("Display: {}", minmax);
    println!("Debug: {:?}", minmax);

    let big_range =   MinMax(-300, 300);
    let small_range = MinMax(-3, 3);

    println!("Большой диапазон - {big} и маленький диапазон {small}",
             small = small_range,
             big = big_range);

    let point = Point2D { x: 3.3, y: 7.2 };

    println!("Сравниваем точки:");
    println!("Display: {}", point);
    println!("Debug: {:?}", point);

    // Ошибка. Типажи `Debug` и `Display` были реализованы, но `{:b}`
    // необходима реализация `fmt::Binary`. Следующий код не сработает.
    // println!("Как выглядит Point2D в виде двоичного кода: {:b}?", point);
}
```

Итак, `fmt::Display` был реализован, но `fmt::Binary` нет, следовательно не может быть использован. `std::fmt` имеет много таких [типажей] и каждый из них требует свою реализацию. Это более подробно описано в документации к <a href="https://doc.rust-lang.org/std/fmt/" data-md-type="link">`std::fmt`</a>.

### Задание

После того, как запустите код, представленный выше, используйте структуру `Point2D` как пример и добавьте новую структуру `Complex`, чтобы вывод был таким:

```txt
Display: 3.3 +7.2i
Debug: Complex { real: 3.3, imag: 7.2 }
```

### Смотрите также:

[`derive`], [`std::fmt`](https://doc.rust-lang.org/std/fmt/), [`макросы`], [`struct`], [`trait`](https://doc.rust-lang.org/std/fmt/#formatting-traits) и [`use`]


[`derive`]: ../../trait/derive.md
[`fmt::Display`]: https://doc.rust-lang.org/std/fmt/
[`макросы`]: ../../macros.md
[`struct`]: ../../custom_types/structs.md
[типажей]: https://doc.rust-lang.org/std/fmt/#formatting-traits
[`use`]: ../../mod/use.md