# OOADP

## Part B Programs

### 1. Adaptor (Structural)

#### Question

**Adaptor (Structural):** To establish the 1st Decathlon store in Mauritius, you go along
with Mr. Satya Nadella, an expert in finding 3rd-party partners. For e.g. a 3rd-party 
TaxCalculator system to cater to the specifics of Sales and VAT (Value-added services Tax)
tax calculations in different countries. He finds a 3rd-party Tax-Calculator system called
‘MauriTax’ in Port Louis. The problem is, the APIs used by ‘MauriTax’ for tax-calculation
is fixed & cannot be changed. The ‘MauriTax’ APIs are incompatible with ‘Decathlon
POS’. How will you use the Adaptor Pattern to design & implement?

#### Program

```java
// TaxCalculator.java
public interface TaxCalculator {
    public double calculateTax( int quantity, double price );
}
// MauriTax.java
public class MauriTax {
    public double mauriTax( double price, int quantity ) {
        return 0.14 * price * (double) quantity;
    }
}
// GST.java
public class GST implements TaxCalculator {
    public double calculateTax( int quantity, double price ) {
        return 0.18 * price * (double) quantity;
    }
}
// MauriTaxAdaptor.java
public class MauriTaxAdaptor implements TaxCalculator {
    private MauriTax mt = new MauriTax();

    public double calculateTax( int quantity, double price ) {
        return mt.mauriTax(price, quantity);
    }
}
// Item.java
public class Item {
    private String name;
    private int quantity;
    private double price;

    public Item( String name, int quantity, double price ) {
        this.name = name;
        this.quantity = quantity;
        this.price = price;
    }

    public double calculateTax( TaxCalculator tc ) {
        return tc.calculateTax(this.quantity, this.price);
    }

    public double calculateTotal( TaxCalculator tc ) {
        return this.quantity * this.price + this.calculateTax(tc);
    }
}
// ShoppingCart.java
import java.util.ArrayList;

public class ShoppingCart {
    private ArrayList<Item> items;
    private TaxCalculator tc;

    public ShoppingCart( TaxCalculator tc ) {
        this.items = new ArrayList<Item>();
        this.tc = tc;
    }

    public void setTaxCalculator( TaxCalculator tc ) { this.tc = tc; }
    public TaxCalculator getTaxCalculator() { return this.tc; }

    public void setItems( ArrayList<Item> items ) { this.items = items; }
    public ArrayList<Item> getItems() { return this.items; }

    public void addItem( String name, int quantity, double price ) {
        items.add( new Item( name, quantity, price ) );
    }

    public double calculateTax() {
        double tax = 0.0;
        for( Item item : items ) {
            tax += item.calculateTax(this.tc);
        }
        return tax;
    }

    public double checkout() {
        double total = 0.0;
        for( Item item: items ) {
            total += item.calculateTotal(this.tc);
        }
        return total;
    }
}
// Client.java
public class Client {
    public static void main( String[] args ) {
        ShoppingCart sc = new ShoppingCart(new GST());
        System.out.println( " ===== Using GST ===== ");
        System.out.println( "Tax with 0 items: " + sc.calculateTax() );
        System.out.println( "Total with 0 items: " + sc.checkout() );
        sc.addItem( "Cycle", 1, 15000.0 );
        sc.addItem( "GBA", 2, 10000.0 );
        System.out.println( "Tax with 2 items: " + sc.calculateTax() );
        System.out.println( "Total with 2 items: " + sc.checkout() );

        sc.setTaxCalculator(new MauriTaxAdaptor());
        System.out.println( " ===== Using MauriTaxAdaptor ===== ");
        System.out.println( "Tax with 0 items: " + sc.calculateTax() );
        System.out.println( "Total with 0 items: " + sc.checkout() );
        System.out.println( "Tax with 2 items: " + sc.calculateTax() );
        System.out.println( "Total with 2 items: " + sc.checkout() );
    }
}
```

### 2. Strategy (Behavioural)

#### Question

**Strategy (Behavioural):** How will you use the Strategy Pattern to tackle the limitations
of traditional Object Oriented Design highlighted in PART A? The design must handle
varying price-schemes having different pricing algorithms. Design & implement.

#### Program

```java
// Customer.java
public abstract class Customer {
    protected String name;
    protected int age;
    protected static double discount;
    public abstract double getDiscount();
    public abstract void setDiscount( double discount );
}
// RegularCustomer.java
public class RegularCustomer extends Customer {
    static {
        RegularCustomer.discount = 12.0;
    }

    public double getDiscount() { return RegularCustomer.discount; }
    public void setDiscount( double discount ) { RegularCustomer.discount = discount; }

    public RegularCustomer( String name, int age ) {
        this.name = name;
        this.age = age;
    }
}
// FirstTimeCustomer.java
public class FirstTimeCustomer extends Customer {
    static {
        FirstTimeCustomer.discount = 15.0;
    }

    public double getDiscount() { return FirstTimeCustomer.discount; }
    public void setDiscount( double discount ) { FirstTimeCustomer.discount = discount; }

    public FirstTimeCustomer( String name, int age ) {
        this.name = name;
        this.age = age;
    }
}
// SeniorCitizen.java
public class SeniorCitizen extends Customer {
    static {
        SeniorCitizen.discount = 10.0;
    }
    public double getDiscount() { return SeniorCitizen.discount; }
    public void setDiscount( double discount ) { SeniorCitizen.discount = discount; }

    public SeniorCitizen( String name, int age ) {
        this.name = name;
        this.age = age;
    }
}
// StoreLevelDiscount.java
public abstract class StoreLevelDiscount {
    protected double minimumPurchase;
    protected double discountAmount;

    public double getMinimumPurchase() { return this.minimumPurchase; }
    public void setMinimumPurchase( double minimumPurchase ) { this.minimumPurchase = minimumPurchase; }

    public double getDiscountAmount() { return this.discountAmount; }
    public void setDiscountAmount( double discountAmount ) { this.discountAmount = discountAmount; }

    public double applyDiscount( double price ) {
        if( price > minimumPurchase ) {
            return price - discountAmount;
        }
        return price;
    }
}
// DayOneStoreLevelDiscount.java
public class DayOneStoreLevelDiscount extends StoreLevelDiscount {
    public DayOneStoreLevelDiscount() {
        this.minimumPurchase = 2000.0;
        this.discountAmount = 100.0;
    }
}
// DayTwoStoreLevelDiscount.java
public class DayTwoStoreLevelDiscount extends StoreLevelDiscount {
    public DayTwoStoreLevelDiscount() {
        this.minimumPurchase = 2500.0;
        this.discountAmount = 200.0;
    }
}
// Client.java
public class Client {
    public static void main( String[] args ) {
        performPayment(1000.0, "Regular Customer", new RegularCustomer("A", 1), new DayOneStoreLevelDiscount());
        performPayment(3000.0, "Regular Customer", new RegularCustomer("B", 3), new DayTwoStoreLevelDiscount());
        performPayment(1000.0, "Senior Citizen", new SeniorCitizen("A", 1), new DayOneStoreLevelDiscount());
        performPayment(3000.0, "Senior Citizen", new SeniorCitizen("B", 3), new DayTwoStoreLevelDiscount());
        performPayment(1000.0, "First Time Customer", new FirstTimeCustomer("A", 1), new DayOneStoreLevelDiscount());
        performPayment(3000.0, "First Time Customer", new FirstTimeCustomer("B", 3), new DayTwoStoreLevelDiscount());
    }

    public static void performPayment( double price, String customerType, Customer customer, StoreLevelDiscount storeLevelDiscount ) {
        price = storeLevelDiscount.applyDiscount(price);
        System.out.println( customerType + " discount = " + customer.getDiscount() + "%" );
        price = price * ( 1 - customer.getDiscount() / 100.0 );
        System.out.println( "Discount price = " + price );
    }
}
```

### 1. Adaptor (Structural)

#### Question

**Adaptor (Structural):** To establish the 1st Decathlon store in Mauritius, you go along
with Mr. Satya Nadella, an expert in finding 3rd-party partners. For e.g. a 3rd-party 
TaxCalculator system to cater to the specifics of Sales and VAT (Value-added services Tax)
tax calculations in different countries. He finds a 3rd-party Tax-Calculator system called
‘MauriTax’ in Port Louis. The problem is, the APIs used by ‘MauriTax’ for tax-calculation
is fixed & cannot be changed. The ‘MauriTax’ APIs are incompatible with ‘Decathlon
POS’. How will you use the Adaptor Pattern to design & implement?

#### Program

```java
// CalcTax.java
public interface CalcTax {
	double taxAmount(String item, int qty, double price);
}
// NewMauritiusTax.java
public class NewMauritiusTax {
	double calcTax(int qntty, double price) {
		return price*qntty*(0.25f);
	}
}
// MyMauritiusTax.java
public class MyMauritiusTax implements CalcTax {
	NewMauritiusTax mt = new NewMauritiusTax();
	double tax_amount=0.0;
	@Override
	public double taxAmount(String item, int qty, double price) {
		tax_amount = mt.calcTax(qty, price);
		return tax_amount;
	}
}
// SwedenTax.java
public class SwedenTax implements CalcTax {
	double tax_amount=0.0;
	public double taxAmount(String item, int qty, double price) {
		tax_amount = price*qty*0.05f;
		return tax_amount;
	}
}
// Item.java
public class Item {
	String name;
	double price=0.0;
	int qty=0;
	double billAmount=0.0;
	CalcTax ct;
	Item(String name, double price, CalcTax ct){
		this.name = name;
		this.price = price;
		this.ct = ct;
	}
	void setTax(CalcTax ct) {
		this.ct = ct;
	}
	void setQunatity(int qty) {
		this.qty = qty;
	}
	void printPrice() {
		double tax = ct.taxAmount(name, qty, price);
		
		billAmount = price*qty+tax;
		System.out.println("Tax = "+tax);
		System.out.println("Item "+ name + " Quantity "+qty+
		" Unit price "+price+ " Total Amount "+billAmount);
	}
}
// TaxAdapterDemo.java
public class TaxAdapterDemo {
	public static void main(String[] args) {
		SwedenTax st = new SwedenTax();
		Item i1 = new Item("Btwin Roackroder 340", 13999.0, st);
		i1.setQunatity(3);
		i1.printPrice();
		i1.setTax(new MyMauritiusTax());
		i1.printPrice();
	}
}
```

### 4. Bridge (Structural)

#### Question

**Bridge (Structural):** You get a call from Ms.Masaba Gupta of Bangalore Decathlon
office that there is a policy decision made globally to introduce discount slabs for a whole
month twice in a year. The discount month will be in January and July after reviewing the
sales made from Feb to June (first five months) and Aug to December (last five months)
respectively. It is decided to provide four slabs of discounts in 2017, namely, 30%, 25%,
20% and 15%, based on the sports item purchased. For e.g. all tennis rackets could have
a 20% discount while cricket bats could only have a 15% discount. All exercise tread-mills
could be given a 30% discount while boxing-gloves could have a 25% discount. Point to
be noted here is that, the slabs of discount may not remain the same in 2018. It is likely to
vary year after year. The ‘Decathlon POS’ software system classifies its customers as
Senior-Citizens (60 and above), First-Time Customers, Regular Customers as of now.
There is a very high possibility that the Customer Type hierarchy will vary, depending
upon the sales-pattern. For e.g. there could be the need to introduce new categories based
on the customer gender.
Use the Bridge Pattern to design & implement, so that both the Customer
Type hierarchy of classes as well as the Discount Percentage hierarchy of
classes can both vary independently? That is, they are not tied to each
other.

#### Program

```java
// CalcTax.java
public interface CalcTax {
	double taxAmount(String item, int qty, double price);
}
// NewMauritiusTax.java
public class NewMauritiusTax {
	double calcTax(int qntty, double price) {
		return price*qntty*(0.25f);
	}
}
// MyMauritiusTax.java
public class MyMauritiusTax implements CalcTax {
	NewMauritiusTax mt = new NewMauritiusTax();
	double tax_amount=0.0;
	@Override
	public double taxAmount(String item, int qty, double price) {
		tax_amount = mt.calcTax(qty, price);
		return tax_amount;
	}
}
// SwedenTax.java
public class SwedenTax implements CalcTax {
	double tax_amount=0.0;
	public double taxAmount(String item, int qty, double price) {
		tax_amount = price*qty*0.05f;
		return tax_amount;
	}
}
// Item.java
public class Item {
	String name;
	double price=0.0;
	int qty=0;
	double billAmount=0.0;
	CalcTax ct;
	Item(String name, double price, CalcTax ct){
		this.name = name;
		this.price = price;
		this.ct = ct;
	}
	void setTax(CalcTax ct) {
		this.ct = ct;
	}
	void setQunatity(int qty) {
		this.qty = qty;
	}
	void printPrice() {
		double tax = ct.taxAmount(name, qty, price);
		
		billAmount = price*qty+tax;
		System.out.println("Tax = "+tax);
		System.out.println("Item "+ name + " Quantity "+qty+
		" Unit price "+price+ " Total Amount "+billAmount);
	}
}
// TaxAdapterDemo.java
public class TaxAdapterDemo {
	public static void main(String[] args) {
		SwedenTax st = new SwedenTax();
		Item i1 = new Item("Btwin Roackroder 340", 13999.0, st);
		i1.setQunatity(3);
		i1.printPrice();
		i1.setTax(new MyMauritiusTax());
		i1.printPrice();
	}
}
```

### 1. Adaptor (Structural)

#### Question

**Adaptor (Structural):** To establish the 1st Decathlon store in Mauritius, you go along
with Mr. Satya Nadella, an expert in finding 3rd-party partners. For e.g. a 3rd-party 
TaxCalculator system to cater to the specifics of Sales and VAT (Value-added services Tax)
tax calculations in different countries. He finds a 3rd-party Tax-Calculator system called
‘MauriTax’ in Port Louis. The problem is, the APIs used by ‘MauriTax’ for tax-calculation
is fixed & cannot be changed. The ‘MauriTax’ APIs are incompatible with ‘Decathlon
POS’. How will you use the Adaptor Pattern to design & implement?

#### Program

```java
// CalcTax.java
public interface CalcTax {
	double taxAmount(String item, int qty, double price);
}
// NewMauritiusTax.java
public class NewMauritiusTax {
	double calcTax(int qntty, double price) {
		return price*qntty*(0.25f);
	}
}
// MyMauritiusTax.java
public class MyMauritiusTax implements CalcTax {
	NewMauritiusTax mt = new NewMauritiusTax();
	double tax_amount=0.0;
	@Override
	public double taxAmount(String item, int qty, double price) {
		tax_amount = mt.calcTax(qty, price);
		return tax_amount;
	}
}
// SwedenTax.java
public class SwedenTax implements CalcTax {
	double tax_amount=0.0;
	public double taxAmount(String item, int qty, double price) {
		tax_amount = price*qty*0.05f;
		return tax_amount;
	}
}
// Item.java
public class Item {
	String name;
	double price=0.0;
	int qty=0;
	double billAmount=0.0;
	CalcTax ct;
	Item(String name, double price, CalcTax ct){
		this.name = name;
		this.price = price;
		this.ct = ct;
	}
	void setTax(CalcTax ct) {
		this.ct = ct;
	}
	void setQunatity(int qty) {
		this.qty = qty;
	}
	void printPrice() {
		double tax = ct.taxAmount(name, qty, price);
		
		billAmount = price*qty+tax;
		System.out.println("Tax = "+tax);
		System.out.println("Item "+ name + " Quantity "+qty+
		" Unit price "+price+ " Total Amount "+billAmount);
	}
}
// TaxAdapterDemo.java
public class TaxAdapterDemo {
	public static void main(String[] args) {
		SwedenTax st = new SwedenTax();
		Item i1 = new Item("Btwin Roackroder 340", 13999.0, st);
		i1.setQunatity(3);
		i1.printPrice();
		i1.setTax(new MyMauritiusTax());
		i1.printPrice();
	}
}
```
