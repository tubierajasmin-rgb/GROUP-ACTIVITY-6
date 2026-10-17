import java.util.Scanner;

abstract class PizzaBase {
    protected String pizzaType;

    public PizzaBase(String pizzaType) {
        this.pizzaType = pizzaType;
    }

    public String getPizzaType() {
        return this.pizzaType;
    }

    public void setPizzaType(String pizzaType) {
        this.pizzaType = pizzaType;
    }

    public abstract double calculatePrice();
}

interface Discountable {
    double getDiscountRate();
}

class PIZZA extends PizzaBase implements Discountable {
    private String toppings;
    private String discount;
    private int diameter;
    private int howMany;
    private double price;

    public PIZZA() {
        super("Regular Pizza");
    }

    public PIZZA(String pizzaType) {
        super(pizzaType);
    }

    public String getToppings() {
        return toppings;
    }

    public void setToppings(String toppings) {
        this.toppings = toppings;
    }

    public String getDiscount() {
        return discount;
    }

    public void setDiscount(String discount) {
        this.discount = discount;
    }

    public int getDiameter() {
        return diameter;
    }

    public void setDiameter(int diameter) {
        if (diameter > 0) {
            this.diameter = diameter;
        }
    }

    public int getHowMany() {
        return howMany;
    }

    public void setHowMany(int howMany) {
        if (howMany > 0) {
            this.howMany = howMany;
        }
    }

    public double getPrice() {
        return price;
    }

    public double getDiameterInCm() {
        return this.diameter * 2.54;
    }

    public double getToppingPrice() {
        if (toppings == null) return 0.0;
        switch (toppings.trim().toLowerCase()) {
            case "pepperoni":
                return 100.00;
            case "ham and cheese":
                return 150.00;
            case "hawaiian":
                return 200.00;
            default:
                return 0.00;
        }
    }

    @Override
    public double getDiscountRate() {
        if (discount == null) return 0.0;
        switch (discount.trim().toLowerCase()) {
            case "senior citizen":
                return 0.20;
            case "vipcard":
                return 0.12;
            case "no discount":
            default:
                return 0.00;
        }
    }

    @Override
    public double calculatePrice() {
        double sizeCharge = getDiameterInCm() * 5.00;
        double baseUnitCost = getToppingPrice() + sizeCharge;
        double subtotal = baseUnitCost * this.howMany;

        double totalDiscount = subtotal * getDiscountRate();
        this.price = subtotal - totalDiscount;
        return this.price;
    }
}

class SPECIAL_PIZZA extends PIZZA {
    private double specialPizzaCharge = 75.00;

    public SPECIAL_PIZZA() {
        super("Special Pizza");
    }

    public double getSpecialPizzaCharge() {
        return specialPizzaCharge;
    }

    public void setSpecialPizzaCharge(double specialPizzaCharge) {
        this.specialPizzaCharge = specialPizzaCharge;
    }

    @Override
    public double calculatePrice() {
        double sizeCharge = getDiameterInCm() * 5.00;
        double baseUnitCost = getToppingPrice() + sizeCharge + this.specialPizzaCharge;
        double subtotal = baseUnitCost * getHowMany();

        double totalDiscount = subtotal * getDiscountRate();
        double finalPrice = subtotal - totalDiscount;

        return finalPrice;
    }
}

class BILL_ORDER {
    public double OUT(PIZZA pie) {
        double amountDue = pie.calculatePrice();

        System.out.println("\n--- ORDER DETAILS ---");
        System.out.println("Pizza Type: " + pie.getPizzaType());
        System.out.println("Toppings: " + pie.getToppings());
        System.out.printf("Diameter: %.2f cm\n", pie.getDiameterInCm());
        System.out.println("Quantity: " + pie.getHowMany());
        System.out.println("Discount: " + pie.getDiscount());
        System.out.printf("Discount Rate: %.0f%%\n", pie.getDiscountRate() * 100);

        if (pie instanceof SPECIAL_PIZZA) {
            SPECIAL_PIZZA sp = (SPECIAL_PIZZA) pie;
            System.out.printf("Specialty Surcharge: PHP %.2f\n", sp.getSpecialPizzaCharge());
        }

        System.out.printf("Amount Due: PHP %.2f\n", amountDue);
        return amountDue;
    }
}

public class TAKE_ORDER {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.println("PIZZA ORDERING SYSTEM");

        int pizzaChoice = 0;
        while (pizzaChoice != 1 && pizzaChoice != 2) {
            System.out.println("\nEnter Pizza Type:");
            System.out.println("1. Regular Pizza");
            System.out.println("2. Special Pizza");
            System.out.print("Choice: ");
            String input = sc.nextLine().trim();
            try {
                pizzaChoice = Integer.parseInt(input);
                if (pizzaChoice != 1 && pizzaChoice != 2) {
                    System.out.println("Invalid choice. Please choose 1 or 2.");
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid input. Please enter 1 or 2.");
            }
        }

        PIZZA pizzaOrder = (pizzaChoice == 1) ? new PIZZA() : new SPECIAL_PIZZA();

        while (true) {
            System.out.print("Enter Toppings (Pepperoni, Ham and Cheese, Hawaiian): ");
            String topping = sc.nextLine().trim();
            if (topping.equalsIgnoreCase("Pepperoni")) {
                pizzaOrder.setToppings("Pepperoni");
                break;
            } else if (topping.equalsIgnoreCase("Ham and Cheese")) {
                pizzaOrder.setToppings("Ham and Cheese");
                break;
            } else if (topping.equalsIgnoreCase("Hawaiian")) {
                pizzaOrder.setToppings("Hawaiian");
                break;
            } else {
                System.out.println("Invalid topping. Acceptable options: Pepperoni, Ham and Cheese, Hawaiian.");
            }
        }

        int diameter = 0;
        while (diameter <= 0) {
            System.out.print("Enter Diameter in inches: ");
            String input = sc.nextLine().trim();
            try {
                diameter = Integer.parseInt(input);
                if (diameter <= 0) {
                    System.out.println("Diameter must be greater than 0.");
                } else {
                    pizzaOrder.setDiameter(diameter);
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid entry. Please enter a valid positive integer.");
            }
        }

        int quantity = 0;
        while (quantity <= 0) {
            System.out.print("Enter How Many?: ");
            String input = sc.nextLine().trim();
            try {
                quantity = Integer.parseInt(input);
                if (quantity <= 0) {
                    System.out.println("Quantity must be greater than 0.");
                } else {
                    pizzaOrder.setHowMany(quantity);
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid entry. Please enter a valid positive integer.");
            }
        }

        while (true) {
            System.out.print("Enter Type of Discount (Senior Citizen, VIPCard, No Discount): ");
            String discount = sc.nextLine().trim();
            if (discount.equalsIgnoreCase("Senior Citizen")) {
                pizzaOrder.setDiscount("Senior Citizen");
                break;
            } else if (discount.equalsIgnoreCase("VIPCard")) {
                pizzaOrder.setDiscount("VIPCard");
                break;
            } else if (discount.equalsIgnoreCase("No Discount")) {
                pizzaOrder.setDiscount("No Discount");
                break;
            } else {
                System.out.println("Invalid discount. Acceptable options: Senior Citizen, VIPCard, No Discount.");
            }
        }

        BILL_ORDER bill = new BILL_ORDER();
        double amountDue = bill.OUT(pizzaOrder);

        double amountReceived = 0.0;
        while (true) {
            System.out.print("Enter Amount Received: PHP ");
            String input = sc.nextLine().trim();
            try {
                amountReceived = Double.parseDouble(input);
                if (amountReceived <= amountDue) {
                    System.out.println("Amount Received must be greater than or equal to Amount Due.");
                } else {
                    break;
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid input. Please enter a valid monetary amount.");
            }
        }

        double change = amountReceived - amountDue;
        System.out.printf("Change: PHP %.2f\n", change);

        sc.close();
    }
}
