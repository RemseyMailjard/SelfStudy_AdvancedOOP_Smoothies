# SelfStudy_AdvancedOOP_Smoothies


# 🍓 Advanced OOP Self-Study Guide: Smoothie Edition

## 🎯 Learning Goals

- Understand how to use **generics** in Java to write flexible, reusable classes.
- Create generic containers for Smoothies that enforce type safety.
- Use **streams** and **lambda expressions** to filter, sort, and calculate data in collections.

---

## 🧃 Scenario: CLI App for Smoothie Bar

You're building a CLI app for a smoothie bar. You want to:

- Organize menu items
- Group smoothie orders
- Calculate totals
- Filter and sort the smoothie list

To make your code clean, flexible, and powerful, you’ll use **Generics** and **Streams with Lambdas**.

---

## 🔹 MODULE 3 — GENERICS

### 🧠 What are Generics?

Generics allow you to write code that works with *any* object type, while still catching errors at **compile time**. Instead of repeating the same class for Smoothies, Drinks, or Snacks—you create one reusable structure.

### 🧪 Example: Generic `MenuItem<T>`

```java
public class MenuItem<T> {
    private String label;
    private T product;

    public MenuItem(String label, T product) {
        this.label = label;
        this.product = product;
    }

    public String getLabel() {
        return label;
    }

    public T getProduct() {
        return product;
    }
}
```

Usage:

```java
Smoothie greenBoost = new Smoothie("Green Boost", 4.99);
MenuItem<Smoothie> special = new MenuItem<>("Special of the Day", greenBoost);

System.out.println(special.getProduct().getName());  // Green Boost
```

---

### 🧪 Example 2: Basket of Smoothies using Generics

```java
public interface PricedItem {
    double getPrice();
    String getName();
}
```

```java
public class Basket<T extends PricedItem> {
    private List<T> items = new ArrayList<>();

    public void addItem(T item) {
        items.add(item);
    }

    public double getTotalPrice() {
        return items.stream().mapToDouble(T::getPrice).sum();
    }

    public void printReceipt() {
        items.forEach(i -> System.out.println(i.getName() + " - €" + i.getPrice()));
    }
}
```

Usage:

```java
Basket<Smoothie> smoothieBasket = new Basket<>();
smoothieBasket.addItem(new Smoothie("Strawberry Dream", 3.5));
smoothieBasket.addItem(new Smoothie("Tropical Twist", 4.25));
smoothieBasket.printReceipt();
System.out.println("Total: €" + smoothieBasket.getTotalPrice());
```

✅ **Practice Tip:** Create another class, like `Snack`, and try using `Basket<Snack>`.

---

## 🔹 MODULE 4 — STREAMS & LAMBDAS

### 🧠 What are Streams?

Streams allow you to **process collections** in a clean, functional style. You can filter, sort, map, reduce, and collect data with just a few lines of code.

### 🧠 What is a Lambda?

A **lambda expression** is a short way to write a function. It looks like this:

```java
x -> x.doSomething()
```

You’ll use lambdas to define what should happen inside your stream operations.

### 🧪 Example: Analyze a Smoothie Menu

```java
List<Smoothie> menu = List.of(
    new Smoothie("Strawberry Dream", 3.5),
    new Smoothie("Green Boost", 4.99),
    new Smoothie("Tropical Twist", 4.25),
    new Smoothie("Berry Blast", 5.75)
);

// Filter smoothies under €5
List<Smoothie> underFive = menu.stream()
    .filter(s -> s.getPrice() < 5.0)
    .collect(Collectors.toList());

// Total price of the full menu
double total = menu.stream()
    .mapToDouble(Smoothie::getPrice)
    .sum();

// Sort smoothies alphabetically
List<Smoothie> sortedByName = menu.stream()
    .sorted(Comparator.comparing(Smoothie::getName))
    .collect(Collectors.toList());

// Print all smoothies
menu.forEach(s -> System.out.println(s.getName() + ": €" + s.getPrice()));
```

### 💡 Lambda vs Anonymous Class

```java
// Lambda expression
menu.forEach(s -> System.out.println(s.getName()));

// Old-style anonymous class
menu.forEach(new Consumer<Smoothie>() {
    @Override
    public void accept(Smoothie s) {
        System.out.println(s.getName());
    }
});
```

---

## 📝 Practice Challenges

1. Create a class `Snack` that also implements `PricedItem`.
2. Use `Basket<Snack>` to create a snack order and calculate total.
3. Create a method that returns only **vegan smoothies** using `.filter(...)`.
4. Sort the menu by **price**, then print it.

---

## 💬 Summary

| Concept   | Use Case                                | Example                      |
|-----------|------------------------------------------|------------------------------|
| Generics  | Reusable containers for products         | `Basket<Smoothie>`           |
| Streams   | Filtering, summing, sorting collections  | `menu.stream().filter(...)`  |
| Lambdas   | Anonymous functions for logic blocks     | `s -> s.getPrice() < 5.0`    |
