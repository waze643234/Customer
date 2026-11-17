```
using System;
using System.Collections.Generic;

namespace  customer
{
    
    struct Product
    {
        public string Name;
        public int Price;
        public int Quantity;

        public Product(string name, int price, int quantity)
        {
            Name = name;
            Price = price;
            Quantity = quantity;
        }
    }

    
    class Customer
    {
        public int CardNumber;
        public int Money;
        public int ItemsBought;
        public int MoneySpent;

        public Customer(int cardNumber, int money)
        {
            CardNumber = cardNumber;
            Money = money;
            ItemsBought = 0;
            MoneySpent = 0;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Random rnd = new Random();

            
            List<Product> products = new List<Product>();
            products.Add(new Product("Хліб", 25, 10));
            products.Add(new Product("Молоко", 40, 8));
            products.Add(new Product("Сир", 120, 5));
            products.Add(new Product("Ковбаса", 180, 4));
            products.Add(new Product("Шоколад", 55, 12));
            products.Add(new Product("Кава", 250, 3));

            int shopProfit = 0;

            Console.Write("Введіть кількість покупців: ");
            int customerCount = int.Parse(Console.ReadLine());

            
            Customer[] customers = new Customer[customerCount];
            for (int i = 0; i < customerCount; i++)
            {
                int card = rnd.Next(1, 1001); // від 1 до 1000
                int startMoney = rnd.Next(1000, 10001); // від 1000 до 10000
                customers[i] = new Customer(card, startMoney);
                Console.WriteLine($"Покупець #{i + 1}: Картка {card}, Баланс: {startMoney} грн");
            }

            Console.WriteLine("\n--- Початок покупок ---");

            
            bool canContinue = true;
            while (canContinue)
            {
                canContinue = false;

                for (int i = 0; i < customers.Length; i++)
                {
                    
                    int productIndex = FindAvailableProduct(products, customers[i].Money, rnd);

                    if (productIndex != -1)
                    {
                        canContinue = true; 

                        
                        Product p = products[productIndex];
                        
                        p.Quantity--;
                        customers[i].Money -= p.Price;
                        customers[i].MoneySpent += p.Price;
                        customers[i].ItemsBought++;
                        shopProfit += p.Price;

                        products[productIndex] = p; 

                        Console.WriteLine($"Картка {customers[i].CardNumber} купив {p.Name} за {p.Price} грн. Залишок грошей: {customers[i].Money}");
                    }
                }
            }

            Console.WriteLine("\n--- Підсумки роботи магазину ---");
            Console.WriteLine($"Загальний прибуток магазину: {shopProfit} грн");

            PrintWinners(customers);

            Console.ReadLine();
        }

        
        static int FindAvailableProduct(List<Product> products, int customerMoney, Random rnd)
        {
            
            bool hasAny = false;
            for (int i = 0; i < products.Count; i++)
            {
                if (products[i].Quantity > 0 && products[i].Price <= customerMoney)
                {
                    hasAny = true;
                    break;
                }
            }

            if (!hasAny)
            {
                return -1; 
            }

            
            while (true)
            {
                int index = rnd.Next(0, products.Count);
                if (products[index].Quantity > 0 && products[index].Price <= customerMoney)
                {
                    return index;
                }
            }
        }

        
        static void PrintWinners(Customer[] customers)
        {
            if (customers.Length == 0) return;

            Customer maxItemsCustomer = customers[0];
            Customer maxMoneyCustomer = customers[0];

            for (int i = 1; i < customers.Length; i++)
            {
                if (customers[i].ItemsBought > maxItemsCustomer.ItemsBought)
                {
                    maxItemsCustomer = customers[i];
                }

                if (customers[i].MoneySpent > maxMoneyCustomer.MoneySpent)
                {
                    maxMoneyCustomer = customers[i];
                }
            }

            Console.WriteLine($"Найбільше товарів ({maxItemsCustomer.ItemsBought} шт.) купив власник картки: {maxItemsCustomer.CardNumber}");
            Console.WriteLine($"Найбільше грошей ({maxMoneyCustomer.MoneySpent} грн) витратив власник картки: {maxMoneyCustomer.CardNumber}");
        }
    }
}
