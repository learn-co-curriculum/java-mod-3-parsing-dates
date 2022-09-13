# Parsing and Formatting Dates

## Learning Goals

- Explain how to parse a date from a `String` object.
- Learn about the `DateTimeFormatter` class and formatting dates.

## Parsing a Date from a String

In the last lesson, we saw how we could construct `LocalDate`, `LocalTime`,
and `LocalDateTime` instances using the static methods `now()` or `of()`. But
what if a user enters a date, and we want to take a `String` object and turn
it into a `LocalDate`?

This is where parsing comes to the rescue! We can get a `LocalDate` object by
parsing a `String` object! The `LocalDate` class uses the default format of
yyyy-MM-dd where the four-digit year, two-digit month, and two-digit day is
separated by hyphens (-). The `parse()` method will take a character sequence
argument and obtain an instance of a `LocalDate` based off of the text.

Let's go back to our `Person` class and modify the `setBirthday()` method to
take in a `String` and see how the `parse()` method works:

```java
import java.time.LocalDate;

public class Person {
    private String name;
    private LocalDate birthday;
    public Person() {
        this.name = "";
        this.birthday = LocalDate.now();
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public LocalDate getBirthday() {
        return birthday;
    }
    public void setBirthday(String birthday) {
        this.birthday = LocalDate.parse(birthday);
    }

    public static void main(String[] args) {
        Person ben = new Person();
        ben.setName("Ben Wyatt");
        ben.setBirthday("1974-11-14");
        System.out.println(ben.getBirthday());
    }
}
```

If we were to run the code above, it would output the following:

```plaintext
1974-11-14
```

In the example above, we feed in the exact default format for the `LocalDate`
class. But what if the format we are trying to parse is different from the
default format? Let's introduce a new class to help us out!

## Java `DateTimeFormatter` Class

The `DateTimeFormatter` class is in the `java.time.format` package and acts as
a formatter for printing and helping parse date-time objects. The
`DateTimeFormatter` class allows us to define different patterns for formatting
and printing out date-time objects. Consider the table below of some common
symbols used in creating a date-time object pattern:

| Symbol | Meaning          | Presentation | Examples            |
|--------|------------------|--------------|---------------------|
| y      | year of era      | year         | 2004; 04            |
| M/L    | month of year    | number/text  | 7; 07; Jul; July; J |
| d      | day of month     | number       | 10                  |
| E      | day of the week  | text         | Tue; Tuesday; T     |
| H      | hour of day      | number       | 0                   |
| m      | minute of hour   | number       | 30                  |
| s      | second of minute | number       | 55                  |
| n      | nano of second   | number       | 987654321           |

There are many other symbols that we could use to create a format for dates, but
the ones listed above are the ones we will see more frequently. To see a full
list of the pattern symbols, please see the [Java 11 DateTimeFormatter Class](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/time/format/DateTimeFormatter.html)
under the _Patterns for Formatting and Parsing_ section.

### Formatting a Date

Let's actually start talking about how to create a date format using the
`DateTimeFormatter` class now! The one way to construct a `DateTimeFormatter`
that we will use over and over again is to use the static `toPattern()` method.
The `toPattern()` method will take in a `String` pattern as a parameter and
return a `DateTimeFormatter` object:

```java
import java.time.format.DateTimeFormatter;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy");
    }
}
```

In the example above, the `toPattern()` method created a `DateTimeFormatter`
object based on the symbols we defined above. The `formatter` will now format
dates using the pattern "MM/dd/yyyy". Let's look at another example of where we
might define a `DateTimeFormatter` object to also format a time:

```java
import java.time.format.DateTimeFormatter;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm:ss");
    }
}
```

Now that we have defined a `DateTimeFormatter` with a date-time pattern we like,
let's put it to use and format some `LocalDateTime` objects! We'll make use of
the `DateTimeFormatter` class' `format()` method. The `format()` method will
take in a date-time object, like `LocalDateTime`, and then return a formatted
`String`:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm:ss");
        
        LocalDateTime dateTime = LocalDateTime.now();
        String formattedDate = formatter.format(dateTime);
        System.out.println(formattedDate);    // <-- May print something like this: 08/18/2022 16:21:38

        dateTime = LocalDateTime.of(1955, 11, 5, 13, 0, 0);
        formattedDate = formatter.format(dateTime);
        System.out.println(formattedDate);    // <-- Will print 11/05/1955 13:00:00
    }
}
```

We could even format `LocalTime` and `LocalDate` objects too using the
`format()` method:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDate;
import java.time.LocalTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("EEE LLL dd, yy");
        String formattedDate = formatter.format(LocalDate.now());
        System.out.println(formattedDate);    // <-- May print something like this: Thu Aug 18, 22

        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss:nnnnnn");
        String formattedTime = formatter.format(LocalTime.now());
        System.out.println(formattedTime);    // <-- May print something like this: 16:21:38:154360
    }
}
```

### Back to Parsing

Now the question from above still stands: What if the format we are trying to
parse is different from the `LocalDate` class' default format? Luckily for us,
there is another `parse()` method that will take in the character sequence and
a `DateTimeFormatter` argument.

Let's revisit the `setBirthday()` method again in the `Person` class. Only this
time, let's say we expect the birthday to be in the format "MM/dd/yyyy":

```java
public void setBirthday(String birthday) {
    this.birthday = LocalDate.parse(birthday, DateTimeFormatter.ofPattern("MM/dd/yyyy"));
}
```

The `parse()` method, in this case, will obtain an instance of `LocalDate` from
the `birthday` text using the `DateTimeFormatter`. The text will first be
formatted using the `DateTimeFormatter` specified and will then return a date.
Let's tie this all together now:

```java
public static void main(String[] args) {
    Person ben = new Person();
    ben.setName("Ben Wyatt");
    ben.setBirthday("11/14/1974");
    System.out.println(ben.getBirthday());
}
```

Since we are using the `parse()` method that takes in both the `String` and the
`DateTimeFormatter` arguments, the above code will compile and return the same
output as before:

```plaintext
1974-11-14
```

The `parse()` method is not limited to the `LocalDate` class. It also exists
in the `LocalTime` and `LocalDateTime` classes:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("EEE LLL dd, yy HH:mm:ss");
        LocalDateTime dateTime = LocalDateTime.parse("Thu Aug 18, 22 16:21:38", dateTimeFormatter);
        System.out.println(dateTime);    // <-- Will print 2022-08-18T16:21:38

        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH:mm");
        LocalTime time = LocalTime.parse("16:21", timeFormatter);
        System.out.println(time);    // <-- Will print 16:21
    }
}
```

## Resources

- [Java 11 DateTimeFormatter Class](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/time/format/DateTimeFormatter.html)
