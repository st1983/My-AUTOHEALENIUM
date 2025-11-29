# Source Code Update Guide

## Overview

The auto-healing framework can automatically update failed XPath locators in your source code files with their auto-healed versions.

## Features

- **Automatic Updates**: When an XPath fails and gets healed, it can be automatically updated in source files
- **Batch Updates**: Update all source files at once after test execution
- **Selective Updates**: Update specific source files
- **Safe Updates**: Only updates XPath strings, preserves code structure

## Usage

### Enable Automatic Source Code Updates

```java
HealingWebDriver driver = DriverFactory.createChromeDriver();

// Enable automatic source code updates
driver.setAutoUpdateSourceCode(true);

// Or use utility method
HealingUtils.enableAutoUpdateSourceCode(driver);
```

### Update Source Files After Test Execution

```java
@AfterMethod
public void tearDown() {
    if (driver != null) {
        // Update all source files with healed XPaths
        int updatedFiles = driver.updateSourceCodeFiles();
        logger.info("Updated {} source files", updatedFiles);
        
        driver.quit();
    }
}
```

### Update Specific Source File

```java
// Update a specific source file
boolean updated = driver.getXPathHealer()
    .updateSourceCodeFile("src/test/java/com/example/MyTest.java");
```

### Manual Update

```java
// Manually record and update an XPath
driver.getXPathHealer().getSourceCodeUpdater()
    .recordXPathUpdate(
        "//input[@id='old-id']", 
        "//input[@name='new-name']"
    );

// Apply updates to source files
driver.updateSourceCodeFiles();
```

## How It Works

1. **During Test Execution**:
   - When an XPath fails and gets healed, the update is recorded
   - Original XPath and healed XPath are stored

2. **After Test Execution**:
   - Framework scans Java source files
   - Finds XPath patterns: `By.xpath("original")` or `"original"` strings
   - Replaces failed XPaths with healed versions
   - Saves updated files

3. **Pattern Matching**:
   - `By.xpath("//input[@id='old']")` → `By.xpath("//input[@name='new']")`
   - `"//input[@id='old']"` → `"//input[@name='new']"`

## Configuration

### Set Source Root Directory

```java
// Default is "src"
HealingWebDriver driver = new HealingWebDriver(chromeDriver, "src/main/java");
```

### Disable Automatic Updates

```java
driver.setAutoUpdateSourceCode(false);
// Or
HealingUtils.disableAutoUpdateSourceCode(driver);
```

## Example

```java
public class MyTest {
    private HealingWebDriver driver;
    
    @BeforeMethod
    public void setUp() {
        driver = DriverFactory.createChromeDriver();
        driver.setAutoUpdateSourceCode(true); // Enable auto-updates
    }
    
    @Test
    public void testWithAutoUpdate() {
        // If this XPath fails, it will be healed and updated in source
        driver.findElement(By.xpath("//input[@id='search']"));
    }
    
    @AfterMethod
    public void tearDown() {
        // Update source files with all healed XPaths
        int filesUpdated = driver.updateSourceCodeFiles();
        logger.info("Updated {} source files", filesUpdated);
        driver.quit();
    }
}
```

## Important Notes

1. **Backup**: Always commit your code before enabling auto-updates
2. **Review**: Review changes before committing updated files
3. **Selective**: You can enable/disable updates per test or globally
4. **Pattern Matching**: Only updates XPath strings, not other code

## Safety Features

- Only updates XPath patterns in source files
- Preserves code structure and formatting
- Logs all updates for review
- Can be enabled/disabled as needed

