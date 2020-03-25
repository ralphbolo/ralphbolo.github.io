---
layout: post
title:  "Explicit Coding with Java Optionals"
date:   2020-03-24 21:12:39 -0400
categories: Java
---

Working on legacy software I often notice a lot of null checks in the codebase to prevent NullPointerExceptions. In java 8, the Optional class was introduced to tackle this exact issue and in my opinion makes for more explicit code, fewer bugs and clearer test cases.

To help illustrate a use case, pretend we are working on software that tracks the inventory in the warehouse. We have a new requirement to determine the total discount from a list of products.  

```java
public class Product {
  private Discount discount;

  public Product(Discount discount) {
    this.discount = discount;
  }

  public Discount getDiscount() {
    return discount;
  }
}
```

```java
public class Discount {
  private BigDecimal amount;
  private Date date;

  public Discount(BigDecimal amount, Date date) {
    this.amount = amount;
    this.date = date;
  }

  public BigDecimal getAmount() {
    return amount;
  }

  public Date getDate() {
    return date;
  }
}

```

Now as a developer by reading the code, it's not explicit that discounts can be null. So I might code up something like below because I assumed that there will always be a discount (Very BAD!).
 
```java
// Get total discount
BigDecimal totalDiscount =  products.stream()
  .map(Product::getDiscount)
  .map(Discount::getAmount)
  .reduce(BigDecimal.ZERO, BigDecimal::add);
```

We can refactor the Product class to have an optional Discount.

```java
class Product {
  private Optional<Discount> discount;

  public Product(Discount discount) {
    this.discount = Optional.ofNullable(discount);
  }

  public Optional<Discount> getDiscount() {
    return discount;
  }
}
```

```java
// Get total discount
BigDecimal totalDiscount =  products.stream()
  .map(Product::getDiscount)
  .filter(Optional::isPresent)
  .map(Optional::get)
  .map(Discount::getAmount)
  .reduce(BigDecimal.ZERO, BigDecimal::add);
```

Now it's explicit that getDiscount() returns an Optional. The API and its intent are a lot clearer and lead to fewer bugs. 