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
// Assuming Smoothie class is defined with: public Smoothie(String name, double price, boolean isVegan, String... ingredients)
Smoothie greenBoost = new Smoothie("Green Boost", 4.99, true, "Spinach", "Apple");
MenuItem<Smoothie> special = new MenuItem<>("Special of the Day", greenBoost);

System.out.println(special.getProduct().getName());  // Green Boost
```

---

### 🧪 Example 2: Basket of Smoothies using Generics

```java
import java.util.List; // For List interface
import java.util.Arrays; // For Arrays.asList()

// Defines a contract for items that have a name and a price.
public interface PricedItem {
    String getName();
    double getPrice();
    // Optional: Add a category or ingredients for more complex examples later
    // String getCategory();
}
```

```java
// Represents a Smoothie, implementing PricedItem.
// Includes details like vegan status and ingredients.
public class Smoothie implements PricedItem {
    private String name;
    private double price;
    private boolean isVegan;
    private List<String> ingredients;

    public Smoothie(String name, double price, boolean isVegan, String... ingredients) {
        this.name = name;
        this.price = price;
        this.isVegan = isVegan;
        this.ingredients = Arrays.asList(ingredients);
    }

    @Override
    public String getName() { return name; }

    @Override
    public double getPrice() { return price; }

    public boolean isVegan() { return isVegan; }

    public List<String> getIngredients() { return ingredients; }

    @Override
    public String toString() {
        return name + " (€" + String.format("%.2f", price) + (isVegan ? ", Vegan" : "") + ", Ingredients: " + ingredients + ")";
    }
}
```
### ⚠️ Without Generics: Code Duplication

Here's how you'd have to write separate classes if generics didn't exist:

```java
public class SmoothieMenuItem {
    private String label;
    private Smoothie product;

    public SmoothieMenuItem(String label, Smoothie product) {
        this.label = label;
        this.product = product;
    }

    public String getLabel() {
        return label;
    }

    public Smoothie getProduct() {
        return product;
    }
}

public class SnackMenuItem {
    private String label;
    private Snack product;

    public SnackMenuItem(String label, Snack product) {
        this.label = label;
        this.product = product;
    }

    public String getLabel() {
        return label;
    }

    public Snack getProduct() {
        return product;
    }
}
```

As you can see, this creates **repetitive code** for each type of item.

---

### 📌 Why Use Generics?

- ✅ One class, many types — clean and DRY code
- ✅ Compile-time type checking (avoids `ClassCastException`)
- ✅ Better readability and reuse
- ✅ Works naturally with collections like `List<T>`, `Map<K, V>`, etc.
```java
// Represents a Snack, also implementing PricedItem.
public class Snack implements PricedItem {
    private String name;
    private double price;

    public Snack(String name, double price) {
        this.name = name;
        this.price = price;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public double getPrice() {
        return price;
    }

    @Override
    public String toString() {
        return name + " (€" + String.format("%.2f", price) + ")";
    }
}
```

```java
import java.util.List; // For List interface
import java.util.ArrayList; // For ArrayList class
// import java.util.stream.Collectors; // Not strictly needed for this Basket version, but good for general stream use

// A generic Basket that can hold any type T that extends PricedItem.
// It can add items, calculate total price, and print a receipt.
public class Basket<T extends PricedItem> {
    private List<T> items = new ArrayList<>();

    public void addItem(T item) {
        items.add(item);
    }

    // Calculates total price using a stream.
    public double getTotalPrice() {
        return items.stream()
                    .mapToDouble(PricedItem::getPrice) // Extracts price from each item
                    .sum(); // Sums up all prices
    }

    // Prints a receipt for all items in the basket.
    public void printReceipt() {
        items.forEach(item -> 
            System.out.println(item.getName() + ": €" + String.format("%.2f", item.getPrice()))
        );
    }
}
```

Usage:

```java
// Demonstrates using the Basket with Smoothie objects.
// Assumes Smoothie constructor: public Smoothie(String name, double price, boolean isVegan, String... ingredients)
Basket<Smoothie> smoothieBasket = new Basket<>();
smoothieBasket.addItem(new Smoothie("Strawberry Dream", 3.50, false, "Strawberry", "Milk", "Sugar"));
smoothieBasket.addItem(new Smoothie("Tropical Twist", 4.25, true, "Pineapple", "Mango", "Coconut Water"));

System.out.println("\nSmoothie Basket Receipt:");
smoothieBasket.printReceipt();
System.out.println("Total Smoothie Basket Price: €" + String.format("%.2f", smoothieBasket.getTotalPrice()));
```

✅ **Practice Tip:** Create another class, like `Snack`, and try using `Basket<Snack>`. *(The `Snack` class is defined above, and an example with `Basket<Snack>` is in the Practice Challenges section).*

---

## 🔹 MODULE 4 — STREAMS & LAMBDAS

### 🧠 What are Streams?

Streams allow you to **process collections** in a clean, functional style. You can filter, sort, map, reduce, and collect data with just a few lines of code. They don't store data; they operate on a source (like a collection) and produce a result.

### 🧠 What is a Lambda?

A **lambda expression** is a short way to write an anonymous function (a function without a name). It's often used with functional interfaces (interfaces with a single abstract method).
It looks like this: `(parameters) -> expression` or `(parameters) -> { statements; }`

```java
// Example: A lambda expression for a simple addition
(a, b) -> a + b
```

You’ll use lambdas to define what should happen inside your stream operations (e.g., how to filter, what to map to).

### 🧪 Example: Analyze a Smoothie Menu

```java
import java.util.List; // For List.of()
import java.util.Arrays; // For Arrays.asList() if preferred for mutable lists
import java.util.stream.Collectors; // For Collectors.toList()
import java.util.Comparator; // For Comparator.comparing()

// Define a menu of smoothies.
// Constructor: Smoothie(String name, double price, boolean isVegan, String... ingredients)
List<Smoothie> menu = Arrays.asList(
    new Smoothie("Strawberry Dream", 3.50, false, "Strawberry", "Milk"),
    new Smoothie("Green Boost", 4.99, true, "Spinach", "Apple", "Mint"),
    new Smoothie("Tropical Twist", 4.25, true, "Pineapple", "Mango", "Coconut Water"),
    new Smoothie("Berry Blast", 5.75, false, "Mixed Berries", "Yogurt"),
    new Smoothie("Power Up", 6.20, true, "Banana", "Protein Powder", "Almond Milk")
);

// Filter: Get smoothies costing less than €5.00
List<Smoothie> underFiveEuros = menu.stream()
    .filter(s -> s.getPrice() < 5.0) // Lambda for filtering
    .collect(Collectors.toList()); // Collect results into a new list
System.out.println("\nSmoothies under €5.00:");
underFiveEuros.forEach(System.out::println);

// MapToDouble & Sum: Calculate the total price of all smoothies on the menu.
double totalMenuPrice = menu.stream()
    .mapToDouble(Smoothie::getPrice) // Method reference to get price
    .sum();
System.out.println("\nTotal price of the full menu: €" + String.format("%.2f", totalMenuPrice));

// Sorted: Sort smoothies by name alphabetically.
List<Smoothie> sortedByName = menu.stream()
    .sorted(Comparator.comparing(Smoothie::getName)) // Lambda for sorting criteria
    .collect(Collectors.toList());
System.out.println("\nMenu sorted by name:");
sortedByName.forEach(System.out::println);

// ForEach: Print all smoothie names and their prices.
System.out.println("\nAll smoothie names and prices:");
menu.forEach(s -> System.out.println(s.getName() + ": €" + String.format("%.2f", s.getPrice())));
```

### 💡 Lambda vs Anonymous Class

Lambdas often provide a more concise way to implement functional interfaces compared to traditional anonymous classes.

```java
import java.util.function.Consumer; // For Consumer functional interface

// Using a lambda expression with forEach:
System.out.println("\nPrinting with Lambda:");
menu.forEach(s -> System.out.println(s.getName()));

// Using an old-style anonymous class with forEach:
System.out.println("\nPrinting with Anonymous Class:");
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
   *(Solution: The `Snack` class definition has been added in Module 3 above.)*

2. Use `Basket<Snack>` to create a snack order and calculate total.

   **Example Solution:**
   ```java
   // Assuming Snack and Basket classes are defined as shown in Module 3.
   // No new imports needed if run in conjunction with previous examples.

   Basket<Snack> snackBasket = new Basket<>();
   snackBasket.addItem(new Snack("Cookie", 1.50));
   snackBasket.addItem(new Snack("Muffin", 2.00));
   snackBasket.addItem(new Snack("Brownie", 2.25));

   System.out.println("\nSnack Basket Receipt:");
   snackBasket.printReceipt();
   System.out.println("Total Snack Basket Price: €" + String.format("%.2f", snackBasket.getTotalPrice()));
   // Expected output:
   // Cookie: €1.50
   // Muffin: €2.00
   // Brownie: €2.25
   // Total Snack Basket Price: €5.75
   ```

3. Create a method that returns only **vegan smoothies** using `.filter(...)`.

   **Example Solution:**
   ```java
   // Assuming Smoothie class (defined in Module 3) has an isVegan() method 
   // and its toString() method includes vegan status.
   // import java.util.List; // Already imported
   // import java.util.Arrays; // Already imported
   // import java.util.stream.Collectors; // Already imported

   List<Smoothie> fullMenu = Arrays.asList(
       new Smoothie("Tropical Twist", 4.25, false, "Pineapple", "Mango", "Coconut Milk"),
       new Smoothie("Green Power", 5.50, true, "Spinach", "Kale", "Apple", "Banana"),
       new Smoothie("Berry Bliss", 4.75, false, "Strawberry", "Blueberry", "Yogurt"),
       new Smoothie("Vegan Delight", 6.00, true, "Almond Milk", "Banana", "Peanut Butter", "Dates"),
       new Smoothie("Simple Banana", 3.00, true, "Banana", "Water")
   );

   List<Smoothie> veganSmoothies = fullMenu.stream()
       .filter(Smoothie::isVegan) // Uses the isVegan method reference
       .collect(Collectors.toList());

   System.out.println("\nAvailable Vegan Smoothies:");
   veganSmoothies.forEach(System.out::println);
   // Example output for one smoothie (relies on Smoothie's toString()):
   // Green Power (€5.50, Vegan, Ingredients: [Spinach, Kale, Apple, Banana])
   ```

4. Sort the menu by **price**, then print it.

   **Example Solution:**
   ```java
   // Assuming Smoothie class (defined in Module 3) has a getPrice() method.
   // The Smoothie constructor is:
   // public Smoothie(String name, double price, boolean isVegan, String... ingredients)
   // import java.util.List; // Assumed from previous examples
   // import java.util.Arrays; // Assumed from previous examples
   // import java.util.Comparator; // For Comparator.comparingDouble, already imported in Module 4 example
   // import java.util.stream.Collectors; // Assumed from previous examples

   List<Smoothie> menuToSort = Arrays.asList(
       new Smoothie("Tropical Twist", 4.25, false, "Pineapple", "Mango", "Coconut Milk"),
       new Smoothie("Green Power", 5.50, true, "Spinach", "Kale", "Apple", "Banana"),
       new Smoothie("Berry Bliss", 4.75, false, "Strawberry", "Blueberry", "Yogurt"),
       new Smoothie("Simple Banana", 3.00, true, "Banana", "Water"),
       new Smoothie("Vegan Delight", 6.00, true, "Almond Milk", "Banana", "Peanut Butter", "Dates")
   );

   List<Smoothie> sortedMenuByPrice = menuToSort.stream()
       .sorted(Comparator.comparingDouble(Smoothie::getPrice))
       .collect(Collectors.toList());

   System.out.println("\nMenu sorted by price (ascending):");
   sortedMenuByPrice.forEach(System.out::println);
   // Example output for one smoothie (relies on Smoothie's toString()):
   // Simple Banana (€3.00, Vegan, Ingredients: [Banana, Water])
   // Tropical Twist (€4.25, false, Ingredients: [Pineapple, Mango, Coconut Milk]) 
   // Berry Bliss (€4.75, false, Ingredients: [Strawberry, Blueberry, Yogurt])
   // Green Power (€5.50, Vegan, Ingredients: [Spinach, Kale, Apple, Banana])
   // Vegan Delight (€6.00, Vegan, Ingredients: [Almond Milk, Banana, Peanut Butter, Dates])
   ```

---

## 💬 Summary

| Concept   | Use Case                                | Example                      |
|-----------|------------------------------------------|------------------------------|
| Generics  | Reusable containers for products         | `Basket<Smoothie>`           |
| Streams   | Filtering, summing, sorting collections  | `menu.stream().filter(...)`  |
| Lambdas   | Anonymous functions for logic blocks     | `s -> s.getPrice() < 5.0`    |
