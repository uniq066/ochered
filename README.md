# ochered
```csharp
using System;

public struct QueueNode<T>
{
    public T Data;
    public QueueNode<T>? Next; //Nullable для возможности присваивания null

    public QueueNode(T data)
    {
        Data = data;
        Next = null;
    }
}

public class MyQueue<T>
{
    private QueueNode<T>? _head; //Nullable для возможности присваивания null
    private QueueNode<T>? _tail; //Nullable для возможности присваивания null
    private int _count;

    public MyQueue()
    {
        _head = null;
        _tail = null;
        _count = 0;
    }

    public int Count
    {
        get { return _count; }
    }

    public void Enqueue(T item)
    {
        QueueNode<T> newNode = new QueueNode<T>(item);

        if (_tail == null) // Очередь пуста
        {
            _head = newNode;
            _tail = newNode;
        }
        else
        {
            _tail.Next = newNode;
            _tail = newNode;
        }
        _count++;
    }

    public T Dequeue()
    {
        if (_head == null)
        {
            throw new InvalidOperationException("Queue is empty.");
        }

        T item = _head.Data;
        _head = _head.Next;

        if (_head == null) // Очередь стала пустой
        {
            _tail = null;
        }

        _count--;
        return item;
    }

    public T Peek()
    {
        if (_head == null)
        {
            throw new InvalidOperationException("Queue is empty.");
        }

        return _head.Data;
    }

    public bool IsEmpty()
    {
        return _count == 0;
    }
}

// Пример использования
public class QueueExample
{
    public static void Main(string[] args)
    {
        MyQueue<string> queue = new MyQueue<string>();
        queue.Enqueue("First");
        queue.Enqueue("Second");
        queue.Enqueue("Third");

        Console.WriteLine("Peek: " + queue.Peek()); // Вывод: Peek: First
        Console.WriteLine("Dequeue: " + queue.Dequeue()); // Вывод: Dequeue: First
        Console.WriteLine("Count: " + queue.Count); // Вывод: Count: 2

        while (!queue.IsEmpty())
        {
            Console.WriteLine("Dequeue: " + queue.Dequeue());
        }

        Console.WriteLine("IsEmpty: " + queue.IsEmpty()); // Вывод: IsEmpty: True
    }
}
```

**Пояснения:**

1.  **`QueueNode<T>`:** Структура, представляющая узел очереди. Содержит данные (`Data`) и ссылку на следующий узел (`Next`). `Next` является `QueueNode<T>?`, чтобы узел мог указывать на `null` (конец очереди).
2.  **`MyQueue<T>`:** Класс, представляющий очередь.
    *   `_head`: Указатель на первый элемент очереди.  Тип `QueueNode<T>?` позволяет указать, что очередь пуста (`_head == null`).
    *   `_tail`: Указатель на последний элемент очереди.  Тип `QueueNode<T>?` позволяет указать, что очередь пуста (`_tail == null`).
    *   `_count`: Количество элементов в очереди.
3.  **`Enqueue(T item)`:** Добавляет элемент в конец очереди.
    *   Создается новый узел `QueueNode<T>` с данными `item`.
    *   Если очередь пуста (`_tail == null`), новый узел становится и головой, и хвостом очереди.
    *   Иначе, `Next` текущего хвоста устанавливается на новый узел, а новый узел становится новым хвостом.
    *   Увеличивается счетчик элементов `_count`.
4.  **`Dequeue()`:** Удаляет и возвращает элемент из начала очереди.
    *   Если очередь пуста (`_head == null`), выбрасывается исключение `InvalidOperationException`.
    *   Извлекаются данные из головы очереди.
    *   Голова очереди перемещается на следующий узел (`_head = _head.Next`).
    *   Если очередь становится пустой (`_head == null`), то и хвост устанавливается в `null`.
    *   Уменьшается счетчик элементов `_count`.
    *   Возвращаются извлеченные данные.
5.  **`Peek()`:** Возвращает элемент из начала очереди, не удаляя его.  Выбрасывает исключение `InvalidOperationException`, если очередь пуста.
6.  **`Count`:** Возвращает количество элементов в очереди.
7.  **`IsEmpty()`:** Возвращает `true`, если очередь пуста, и `false` в противном случае.

**Преимущества использования структуры для узла:**

*   **Легче по весу:** Структуры являются типами значений, поэтому создаются непосредственно в стеке (или внутри содержащего их объекта), что может быть быстрее, чем выделение памяти в куче для объектов классов.
*   **Меньше накладных расходов GC:**  Структуры не подвержены сборке мусора (GC) напрямую, что может уменьшить нагрузку на GC.

**Недостатки использования структуры для узла:**

*   **Копирование:**  При присваивании структуры происходит ее копирование.  Для больших структур это может быть неэффективно.  В данном случае, структура `QueueNode<T>` содержит ссылку на другой узел (`Next`), поэтому копирование структуры копирует и эту ссылку (то есть, по сути, имеем дело с поверхностным копированием графа).  Однако, если бы структура содержала большие данные, это могло бы стать проблемой.
*   **Нельзя наследовать:** Структуры не поддерживают наследование.

В данном случае использование структуры для узла очереди может быть полезным, так как узлы относительно простые и их много, поэтому уменьшение накладных расходов GC может быть значительным.  Однако, если бы узлы содержали большие данные, то использование класса могло бы быть более предпочтительным.

**Важно:** Использование `QueueNode<T>?` (Nullable) критически важно для правильной работы кода, т.к. позволяет узлам указывать на `null`, сигнализируя о конце списка или о пустой очереди.  Без этого пришлось бы придумывать другие способы обозначения конца очереди, что усложнило бы код.
