# AutoHeal Selenium Framework

A Java-based auto-healing framework for Selenium WebDriver that automatically updates failed XPath locators and adjusts page load wait times. Similar to Healenium but without AI dependencies.

## Features

1. **Automatic XPath Healing**: When an XPath fails, the framework automatically tries alternative locators based on element attributes (ID, Name, Class, Text, etc.)

2. **Automatic Wait Time Adjustment**: Tracks page load times and automatically adjusts explicit wait timeouts based on historical performance

3. **Compatible with Existing Projects**: Works seamlessly with existing Selenium Java TestNG Maven projects

4. **No AI Dependencies**: Pure Java implementation without any AI/ML libraries

## Requirements

- Java 8
- Selenium 3.14.0
- TestNG 7.5
- Maven 3.x

## Installation

1. Clone or download this project
2. Build the project:
   ```bash
   mvn clean install
   ```

## Usage

### Basic Usage

```java
import com.automation.heal.HealingWebDriver;
import com.automation.heal.DriverFactory;
import org.openqa.selenium.By;

// Create a healing driver
HealingWebDriver driver = DriverFactory.createChromeDriver();
driver.get("https://example.com");

// Use XPath - framework will auto-heal if it fails
WebElement element = driver.findElement(By.xpath("//input[@id='search']"));
```

### With Explicit Waits

```java
import com.automation.heal.HealingWebDriverWait;

// Create wait with auto-adjusted timeout
HealingWebDriverWait wait = new HealingWebDriverWait(driver, 
    driver.getPageLoadTracker().getCurrentWaitTime());

// Wait for element with auto-healing
WebElement element = wait.untilElementVisible(By.xpath("//input[@name='q']"));
```

### Wrapping Existing WebDriver

```java
import org.openqa.selenium.chrome.ChromeDriver;
import com.automation.heal.DriverFactory;

WebDriver existingDriver = new ChromeDriver();
HealingWebDriver healingDriver = DriverFactory.wrapDriver(existingDriver);
```

### Configuration

```java
// Enable/disable healing
driver.setHealingEnabled(true);

// Set maximum healing attempts
driver.setMaxHealingAttempts(3);

// Set default wait time
driver.getPageLoadTracker().setDefaultWaitTime(30);
```

## How It Works

### XPath Healing

When an XPath fails, the framework:

1. Extracts attributes from the original XPath (ID, Name, Class, Text, Tag)
2. Generates alternative XPaths based on these attributes
3. Tries each alternative in priority order:
   - ID-based XPath
   - Name-based XPath
   - Class-based XPath (exact and contains)
   - Text-based XPath
   - Combination of attributes
4. Also tries alternative locator strategies (By.id, By.name, By.className, etc.)

### Wait Time Adjustment

The framework:

1. Records page load times using Navigation Timing API
2. Tracks explicit wait times
3. Calculates average and maximum load times
4. Automatically adjusts wait timeouts (max + 20% buffer)
5. Maintains bounds (min: 5s, max: 60s)

## Integration with TestNG

Add the listener to your `testng.xml`:

```xml
<listeners>
    <listener class-name="com.automation.heal.HealingTestListener"/>
</listeners>
```

Or use `@Listeners` annotation:

```java
@Listeners(HealingTestListener.class)
public class MyTestClass {
    // ...
}
```

## Project Structure

```
src/
├── main/
│   └── java/
│       └── com/
│           └── automation/
│               └── heal/
│                   ├── XPathHealer.java          # XPath healing engine
│                   ├── PageLoadTracker.java       # Page load time tracking
│                   ├── HealingWebDriver.java      # Custom WebDriver wrapper
│                   ├── HealingWebDriverWait.java  # Extended WebDriverWait
│                   ├── DriverFactory.java         # Driver factory
│                   └── HealingTestListener.java   # TestNG listener
└── test/
    └── java/
        └── com/
            └── automation/
                └── heal/
                    └── ExampleTest.java          # Example test class
```

## Running Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=ExampleTest
```

## Limitations

- XPath healing works best when element attributes (ID, Name, Class) are stable
- Healing may not work if the DOM structure changes significantly
- Wait time adjustment is based on historical data (last 10 samples)

## Best Practices

1. Use stable element attributes (ID, Name) when possible
2. Let the framework track page load times before setting custom wait times
3. Review healed XPaths in logs to update your test code
4. Use explicit waits with `HealingWebDriverWait` for better reliability

## License

This project is provided as-is for automation testing purposes.


