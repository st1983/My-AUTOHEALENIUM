# Integration Guide

## Integrating AutoHeal into Existing Selenium TestNG Projects

### Step 1: Add Dependency

Add this project as a dependency in your `pom.xml`:

```xml
<dependency>
    <groupId>com.automation</groupId>
    <artifactId>autoheal-selenium</artifactId>
    <version>1.0.0</version>
</dependency>
```

Or if you want to use it as a local dependency:

```xml
<dependency>
    <groupId>com.automation</groupId>
    <artifactId>autoheal-selenium</artifactId>
    <version>1.0.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/lib/autoheal-selenium-1.0.0.jar</systemPath>
</dependency>
```

### Step 2: Update Your Base Test Class

Replace your WebDriver initialization:

**Before:**
```java
WebDriver driver = new ChromeDriver();
```

**After:**
```java
import com.automation.heal.HealingWebDriver;
import com.automation.heal.DriverFactory;

HealingWebDriver driver = DriverFactory.createChromeDriver();
```

### Step 3: Update Explicit Waits

**Before:**
```java
WebDriverWait wait = new WebDriverWait(driver, 30);
WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(By.xpath("//input[@id='search']")));
```

**After:**
```java
import com.automation.heal.HealingWebDriverWait;

HealingWebDriverWait wait = new HealingWebDriverWait(driver, 
    driver.getPageLoadTracker().getCurrentWaitTime());
WebElement element = wait.untilElementVisible(By.xpath("//input[@id='search']"));
```

### Step 4: Wrap Existing WebDriver (Optional)

If you have complex driver setup, you can wrap your existing driver:

```java
WebDriver existingDriver = // your existing setup
HealingWebDriver healingDriver = DriverFactory.wrapDriver(existingDriver);
```

### Step 5: Add TestNG Listener (Optional)

Add to your `testng.xml`:

```xml
<listeners>
    <listener class-name="com.automation.heal.HealingTestListener"/>
</listeners>
```

Or use annotation:

```java
@Listeners(HealingTestListener.class)
public class YourTestClass {
    // ...
}
```

## Minimal Changes Required

The framework is designed to be a drop-in replacement. You only need to:

1. Change `WebDriver` to `HealingWebDriver`
2. Use `DriverFactory` to create drivers
3. Optionally use `HealingWebDriverWait` for better wait handling

All your existing `findElement()`, `findElements()`, and other WebDriver methods will work as before, but with auto-healing enabled.

## Configuration

You can configure healing behavior:

```java
// Disable healing if needed
driver.setHealingEnabled(false);

// Adjust healing attempts
driver.setMaxHealingAttempts(5);

// Set custom default wait time
driver.getPageLoadTracker().setDefaultWaitTime(45);
```

## Benefits

- **Zero code changes** for most existing tests
- **Automatic XPath healing** when locators fail
- **Automatic wait adjustment** based on page performance
- **Backward compatible** with all Selenium WebDriver methods


