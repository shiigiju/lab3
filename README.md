## Отчет по лабораторной работе № 3

#### № группы: `ПМ-2401`

#### Выполнил: `Безносова Варвара Владиславовна`

#### Вариант: `2`

### Cодержание:

- [Постановка задачи](#1-постановка-задачи)
- [Выбор структуры данных](#2-выбор-структуры-данных)
- [Описание классов](#3-описание-классов)
- [Программа](#4-программа)
- [Анализ правильности решения](#5-анализ-правильности-решения)

### 1. Постановка задачи
>Разработать функционал для управления товарами с учетом истории изменения их цен,
>включая работу с себестоимостью, проверку минимально допустимой цены и выполнение расчетов на >основе цен.
1. Создание товара
Товар создается с заданным названием, себестоимостью и начальной ценой. Если
начальная цена ниже себестоимости, она автоматически устанавливается равной
себестоимости.
2. Вывод информации о товаре
Отображение названия, себестоимости и текущей цены товара.
3. Изменение цены товара
Устанавливается новая цена товара. Если она ниже себестоимости, система выдает
предупреждение или автоматически устанавливает цену равной себестоимости.
4. Добавление наценки в процентах
Увеличение текущей цены товара на заданный процент. Проверка, чтобы итоговая
цена не опустилась ниже себестоимости.
5. Применение скидки в процентах
Уменьшение текущей цены товара на указанный процент. Обеспечение, что итоговая цена не ниже себестоимости.
6. Изменение цены на фиксированное значение
Увеличение или уменьшение цены товара на указанное число. Проверка ограничения на минимальную цену.
7. Вычисление средней стоимости товара
Возвращает среднюю цену на основе истории изменения цен.
8. Определение средней наценки
Рассчитывает среднюю наценку относительно себестоимости на основе истории
изменения цен
9. Расчет возможной выручки
Определяет сумму, которую можно получить, если продать указанное количество
товара по текущей цене.
10. Разрешение продаж ниже себестоимости
Добавление флага, который позволяет устанавливать цену ниже себестоимости.
Флаг по умолчанию установлен в false.
11. История изменения цен
Хранение массива истории изменений цены. Включает текущую цену и ограниченное количество предыдущих значений.
12. Печать истории цен
Вывод истории изменения цен в виде массива для визуализации.
13. Конструктор только по названию и себестоимости
Реализация конструктора для создания товара только с указанием его названия
и себестоимости. Цена в этом случае устанавливается равной себестоимости.


### 2. Выбор структуры данных

На вход программа получает название товара `name` типа String, себестоимость `costPrice` и текущую цену `currentPrice` класса double и флаг `allowBelowCost` типа boolean

### 3. Описание классов
- класс Product вмещает в себя все методы, необходимые для решения задачи
- класс Test тестирует работу класса Product
  
### 4. Программа
```java
public class Product {
    private String name; // Название товара
    private double costPrice; // Себестоимость
    private double currentPrice; // Текущая цена
    private double[] priceHistory; // История изменений цен (фиксированный размер)
    private int historyIndex; // Текущий индекс в истории
    private boolean allowBelowCost; // Флаг разрешения продажи ниже себестоимости
    private int priceCount = 1;

    // Конструктор
    public Product(String name, double costPrice, double initialPrice) {
        this.name = name;
        this.costPrice = costPrice;
        this.currentPrice = Math.max(initialPrice, costPrice);
        this.priceHistory = new double[10]; // Храним максимум 10 последних цен
        this.priceHistory[0] = this.currentPrice;
        this.allowBelowCost = false;
    }
    // Конструктор только по названию и себестоимости
    public Product(String name, double costPrice) {
        this(name, costPrice, costPrice);
    }
    // Запись новой цены в историю
    private void recordPrice(double newPrice) {
        if (priceCount < priceHistory.length) {
            priceHistory[priceCount] = newPrice;
            priceCount = priceCount + 1;
        }
    }
    
    //Изменение цены товара
    public void changePrice(double newPrice) {
        if (allowBelowCost || newPrice >= costPrice) {
            currentPrice = newPrice;
            recordPrice(newPrice);
        }
    }

    // Добавление наценки в процентах
    public void addMarkup(double percent){
        changePrice(currentPrice * (1 + percent / 100));
    }
    
    // Добавление скидки в процентах
    public void addDiscount(double percent) {
        changePrice(currentPrice * (1 - percent / 100));
    }
    
    // Увеличение или уменьшение цены товара на указанное число
    public void changeFixCost(double amount) {
        double newPrice = currentPrice + amount;
        if (allowBelowCost || newPrice >= costPrice) {
            currentPrice = newPrice;
        }
    }

    // Вычисление средней цены товара
    public double getAveragePrice() {
        double sum = 0;
        int count = 0;
        for (int i = 0; i < priceCount; i++) {
            sum = sum + priceHistory[i];
            count = count + 1;
        }
        if (count == 0) {
            return 0;
        }
        return sum / count;
    }
    
    // Вычисление средней наценки товара
    public double getAverageProfit() {
        double sum = 0;
        int count = 0;
        for (int i = 0; i < priceCount; i++) {
            sum = sum + priceHistory[i] - costPrice;
            count = count + 1;
        }
        if (count == 0) {
            return 0;
        }
        return sum / count;
    }

    // Вывод истории изменения цен
    public void printPriceHistory() {
        System.out.print("История цен: ");
        for (int i = 0; i < historyIndex; i++) {
            System.out.print(priceHistory[i] + " ");
        }
        System.out.println();
    }

    // Разрешение или запрет продаж ниже себестоимости
    public void allowSellingBelowCost(boolean allow) {
        this.allowBelowCost = allow;
    }

    // Вычисление возможной выручки при продаже указанного количества товара
    public double calculateRevenue(int quantity) {
        return quantity * currentPrice;
    }
    // Вывод информации о товаре
    @Override
    public String toString() {
        return "Товар: " + name + ", Себестоимость: " + costPrice + ", Текущая цена: " + currentPrice;
    }
}
```
### 5. Анализ правильности решения
```
public class Test {
    public static void main(String[] args) {
        // Создание товара "Телевизор" с себестоимостью 20000 и ценой 25000
        Product tv = new Product("Телевизор", 20000, 25000);

        // Вывод информации о товаре
        System.out.println(tv.name, tv.costPrice, tv.currentPrice);

        // Изменение цены
        tv.changePrice(23000);
        System.out.print("Новая цена: ", tv.currentPrice);

        // Применение скидки 10%
        tv.addDiscount(10);
        System.out.print("Цена после скидки 10%: ", tv.currentPrice);
        
        // Наценка 20%
        tv.addMarkup(20);
        System.out.print("Цена после наценки 20%: ", tv.currentPrice);

        // Изменение цены на фиксированное значение (+2000)
        tv.changeFixCost(2000);
        System.out.print("Цена после увеличения на 2000: ", tv.currentPrice);
        
        //Попытка уменьшить цену ниже себестоимости
        tv.changePrice(18000);
        System.out.print("Попытка установить цену 18000 при запрете продаж ниже себестоимости: ");
        System.out.println(tv.currentPrice);

        //  Включение разрешения продажи ниже себестоимости 
        tv.allowSellingBelowCost(true);
        tv.changePrice(15000);
        System.out.print("Цена после разрешения продаж ниже себестоимости: ", tv.currentPrice);

        // Вычисление средней цены
        System.out.print("Средняя цена товара: ", tv.getAveragePrice());
        
        // Вычисление средней наценки
        System.out.print("Средняя наценка товара: ", tv.getAverageProfit());

        // Расчёт возможной выручки (если продадим 100 единиц)
        double revenue = tv.calculateRevenue(100);
        System.out.print("Возможная выручка при продаже 100 единиц: ");
        System.out.println(revenue);

        // Вывод истории цен
        System.out.println("История изменения цен:");
        tv.printPriceHistory();
    }
}
```
