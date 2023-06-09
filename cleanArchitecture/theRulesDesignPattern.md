# The Rules Design Pattern
--------------------------
Supporting code that has a lot of conditional logic and duplication can be quite hard to integrate new rules as the code can be difficult to understand and to digest what is going on. This sort of code often has comments explaining what the different pieces of conditional logic are doing. The problems only gets worse as you have to add more conditions over time.

[Michael Whelan: The Rules Design Pattern](https://www.michael-whelan.net/rules-design-pattern/)

How to refactor code in Galaxy using this pattern?

### Before
~~~c#
public class DiscountCalculator
{
    public decimal CalculateDiscountPercentage(Customer customer)
    {
        decimal discount = 0;
        if (customer.DateOfBirth < DateTime.Now.AddYears(-65))
        {
            // senior discount 5%
            discount = .05m;
        }

        if (customer.DateOfBirth.Day == DateTime.Today.Day &&
            customer.DateOfBirth.Month == DateTime.Today.Month)
        {
            // birthday 10%
            discount = Math.Max(discount, .10m);
        }

        if (customer.DateOfFirstPurchase.HasValue)
        {
            if (customer.DateOfFirstPurchase.Value < DateTime.Now.AddYears(-1))
            {
                // after 1 year, loyal customers get 10%
                discount = Math.Max(discount, .10m);
                if (customer.DateOfFirstPurchase.Value < DateTime.Now.AddYears(-5))
                {
                    // after 5 years, 12%
                    discount = Math.Max(discount, .12m);
                    if (customer.DateOfFirstPurchase.Value < DateTime.Now.AddYears(-10))
                    {
                        // after 10 years, 20%
                        discount = Math.Max(discount, .2m);
                    }
                }

                if (customer.DateOfBirth.Day == DateTime.Today.Day &&
                    customer.DateOfBirth.Month == DateTime.Today.Month)
                {
                    // birthday additional 10%
                    discount += .10m;
                }
            }
        }
        else
        {
            // first time purchase discount of 15%
            discount = Math.Max(discount, .15m);
        }
        if (customer.IsVeteran)
        {
            // veterans get 10%
            discount = Math.Max(discount, .10m);
        }

        return discount;
    }
}
~~~

### After
![](https://www.michael-whelan.net/images/rules-pattern.png)
~~~C#
public interface IDiscountRule {
    decimal CalculateCustomerDiscount(Customer customer);
}
~~~
~~~C#
public class BirthdayDiscountRule : IDiscountRule {
    public decimal CalculateCustomerDiscount(Customer customer) {
        return customer.IsBirthday() ? 0.10m : 0;
    }
}
~~~

~~~C#
public class LoyalCustomerRule : IDiscountRule
{
    private readonly int _yearsAsCustomer;
    private readonly decimal _discount;
    private readonly DateTime _date;

    public LoyalCustomerRule(int yearsAsCustomer, decimal discount, DateTime? date = null)
    {
        _yearsAsCustomer = yearsAsCustomer;
        _discount = discount;
        _date = date.ToValueOrDefault();
    }

    public decimal CalculateCustomerDiscount(Customer customer)
    {
        if (customer.HasBeenLoyalForYears(_yearsAsCustomer, _date))
        {
            var birthdayRule = new BirthdayDiscountRule();

            return _discount + birthdayRule.CalculateCustomerDiscount(customer);
        }
        return 0;
    }
}
~~~

~~~c#
public class RulesDiscountCalculator : IDiscountCalculator
{
    List<IDiscountRule> _rules = new List<IDiscountRule>();

    public RulesDiscountCalculator()
    {
        _rules.Add(new BirthdayDiscountRule());
        _rules.Add(new SeniorDiscountRule());
        _rules.Add(new VeteranDiscountRule());
        _rules.Add(new LoyalCustomerRule(1, 0.10m));
        _rules.Add(new LoyalCustomerRule(5, 0.12m));
        _rules.Add(new LoyalCustomerRule(10, 0.20m));
        _rules.Add(new NewCustomerRule());
    }

    public decimal CalculateDiscountPercentage(Customer customer)
    {
        decimal discount = 0;

        foreach (var rule in _rules)
        {
            discount = Math.Max(rule.CalculateCustomerDiscount(customer), discount);
        }

        return discount;
    }
}
~~~


Pipeline Implementation Idea from Tomcat:

[Pipeline.java](https://github.com/vonzhou/HowTomcatWorks/blob/master/src/main/java/org/apache/catalina/Pipeline.java), [Valve.java](https://github.com/vonzhou/HowTomcatWorks/blob/master/src/main/java/org/apache/catalina/Valve.java), [ValveContext.java](https://github.com/vonzhou/HowTomcatWorks/blob/master/src/main/java/org/apache/catalina/ValveContext.java)

[SimplePipeline.java](https://github.com/vonzhou/HowTomcatWorks/blob/master/src/main/java/ex05/pyrmont/core/SimplePipeline.java)

[Spring Security Architecture](https://docs.spring.io/spring-security/reference/servlet/architecture.html)
![SecurityFilterChain](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/securityfilterchain.png)
~~~java
public interface Pipeline {
    public Valve getBasic();
    public void setBasic(Valve valve);
                           
    public void addValve(Valve valve);
    public void removeValve(Valve valve);
    public Valve[] getValves();
                           
    public void invoke(Request request, Response response);
}
~~~
~~~java
public interface Valve {
    public void invoke(Request request, Response response, ValveContext valveContext);
}
~~~
~~~java
public interface ValveContext {
    public void invokeNext(Request request, Response response);
}
~~~