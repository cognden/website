---
title: Selenium‘s PO Pattern
date: 2025-07-31T09:18:28+08:00
tags:
  - testing-tools
showToc: true
TocOpen: true
draft: false
description: This article records my understanding of the Page Object (PO) pattern in Selenium, including what the PO pattern is and corresponding examples.
summary: This article records my understanding of the Page Object (PO) pattern in Selenium, including what the PO pattern is and corresponding examples.
---

> This article records my understanding of Selenium's PO pattern, including what the PO pattern is and corresponding examples.

When I first saw the PO pattern (Page Object Model), I had two questions in mind:

- What is this thing?
- How should I use it?

With these two questions, I searched for information and learned that it is a design pattern that separates the code for locating and operating page elements from the test code to improve the maintainability and reusability of tests.

That is to say, encapsulate the positioning and operation of page elements, then provide a unified upward interface, and then make calls.

Doing this has an advantage. If the page elements change (such as the tag `id` changing, the `html` structure changing, etc.), you only need to adjust the previously encapsulated code without changing the test code.

Here is an example of code without using the PO pattern:

```python
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

@pytest.fixture  
def driver():  
    driver = webdriver.Edge()  
    driver.get("https://example.com/login")  
    yield driver  
    driver.quit()

def test_login_without_po(driver):
    # Directly locate and operate elements in the test case
    # Wait for the username input box to finish loading
    username_input = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "username"))
    )
    username_input.send_keys("test_user")
    
    # Wait for the password input box to finish loading
    password_input = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "password"))
    )
    password_input.send_keys("test_password")
    
    # Wait for the login button to be clickable and click it
    login_button = WebDriverWait(driver, 10).until(
        EC.element_to_be_clickable((By.ID, "login-button"))
    )
    login_button.click()
    
    # Verify the welcome message after successful login
    login_message = WebDriverWait(driver, 10).until(
        EC.visibility_of_element_located((By.ID, "login_message"))
    )
    assert "Welcome" in login_message.text
```

As you can see, a large number of element operation details are mixed with the test logic, which not only violates the **Single Responsibility Principle** but also leads to a large amount of duplicate code. The only difference between them is the page elements.

Just imagine, it is currently only a test for the login function, so the amount of code seems relatively small, but if you want to test the functions of the entire website (such as registration, logout, personal information, etc.), then the positioning and operation of the same page elements will have to be repeated in every test.

Moreover, during the development process, the web page structure will change continuously. Every time there is a change, we need to readjust the test code. It's okay for one or two changes, but it's unbearable when there are a large number of them!

I consider myself a lazy person, so I naturally want to be as lazy as possible.

## Core Concepts of the PO Pattern

Having written this far, it's necessary to introduce the core concepts of the PO pattern to facilitate a better understanding in the following examples.

- Page object
    - Abstract each web page as a class, and encapsulate the elements and operations on the page as attributes and methods of the class.
    - For example, the login page can be abstracted as the `LoginPage` class.
- Separation of concerns between page operations and test cases
    - **Page object**: Responsible for element positioning and interactive operations (such as clicking and inputting).
    - **Test case**: Focus on business logic and call the methods of the page object to complete the test.

## Implementation Example of the PO Pattern

Now let's start the actual operation. According to the above description, by separating page operations from test cases, the project can be divided into:

- **Page object layer**: One class for each page, encapsulating elements and operations.
- **Test case layer**: Call the page object to complete the test.

The project structure is as follows:
```txt
my_project/
├── pages/
│   ├── page_login.py
│   └── ...
└── tests/
    ├── test_login.py
    └── ...
```

### Define `page_login.py`

Suppose we want to test a login interface. We can create a `LoginPage` class to encapsulate the "login" operation.

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class LoginPage:
    def __init__(self, driver):
        """Initialize the LoginPage instance.
        
        Parameters:
            driver (WebDriver): The passed Selenium WebDriver instance,
                              used to interact with the browser.
        """
        self.__driver = driver
        self.__username_input = (By.ID, "username")
        self.__password_input = (By.ID, "password")
        self.__login_button = (By.ID, "login_button")
        self.__login_message = (By.ID, "login_message")

    def login(self, username, password):
        """Log in to the system.

        Parameters:
            username (str): Username
            password (str): Password
        """
        self.__enter_username(username)
        self.__enter_password(password)
        self.__click_login()

    def get_login_message(self):
        """Get the text of the login message.

        Returns:
            str: The text content of the login message.
        """
        return WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__login_message)
        ).text

    def __enter_username(self, username):
        """Enter the specified username in the username input box.

        Parameters:
            username (str): The username to be entered
        """
        WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__username_input)
        ).send_keys(username)

    def __enter_password(self, password):
        """Enter the specified password in the password input box.

        Parameters:
            password (str): The password to be entered
        """
        WebDriverWait(self.__driver, 10).until(
            EC.visibility_of_element_located(self.__password_input)
        ).send_keys(password)

    def __click_login(self):
        """Wait for the login button to be clickable and perform the click operation."""
        WebDriverWait(self.__driver, 10).until(
            EC.element_to_be_clickable(self.__login_button)
        ).click()
```

### Define `test_login.py`

Now create `test_login.py` to test the login function.

```python
import pytest
from selenium import webdriver
from pages.page_login import LoginPage

@pytest.fixture
def driver():
    """Create and configure an Edge WebDriver instance for testing the login function.

    Returns:
        WebDriver: The configured Edge WebDriver instance
    """
    driver = webdriver.Edge()
    driver.get("https://example.com/login")
    yield driver
    driver.quit()

def test_login(driver):
    """Test the login function.

    Parameters:
        driver (WebDriver): The Selenium WebDriver instance for browser automation operations.
    """
    login_page = LoginPage(driver)
    login_page.login("test_user", "test_password")
    assert "Welcome" in login_page.get_login_message(), "Login failed, welcome message not found"
```

## Further Encapsulation: `base.py`

Is this example perfect at this point? No, the operations on page elements can be further encapsulated by extracting the common operations.

You might find this unclear and wonder, "Yes, it can be further encapsulated, but why?"

The answer lies in maintainability. Imagine that in a program, we are going to use the string `"passwd"` frequently. Should we use a variable to store it or use it directly?

Of course, we choose to define a variable to store it. In this way, if there is a need in the future, we can change it in one place and the entire program will automatically use the new content.

Here, it's the same. By extracting the same operations, if there is a better implementation method in the future, we can only change it in one place without changing other code.

So we add a base layer to store common operations.

The updated project structure is as follows:
```txt
my_project/
├── bases/        # Newly added
│   ├── base.py   # Store common operations
│   └── ...
├── pages/
│   ├── page_login.py
│   └── ...
└── tests/
    ├── test_login.py
    └── ...
```

### Define `base.py`

Define the `Base` class to store element searching and some common operation methods (such as clicking and inputting text).

```python
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class Base:
    def __init__(self, driver):
        """Initialize the Base class.

        Parameters:
            driver (WebDriver): The WebDriver instance used to interact with the browser.
        """
        self.driver = driver

    def base_find_element(self, locator, timeout=10):
        """Wait for the specified element to become visible on the page.

        Parameters:
            locator (tuple): A tuple used to locate the element, usually consisting of By and the positioning value.
            timeout (int, optional): The maximum time (in seconds) to wait for the element. Defaults to 10 seconds.

        Returns:
            WebElement: The visible element that has been waited for.
        """
        return WebDriverWait(self.driver, timeout=timeout).until(
            EC.visibility_of_element_located(locator)
        )

    def base_click(self, locator):
        """Perform a click operation on the element.

        Parameters:
            locator (tuple): A tuple for locating the element, containing the positioning method and the positioning value.
        """
        self.base_find_element(locator).click()

    def base_send_keys(self, locator, text):
        """Enter text into the located element.

        Parameters:
            locator (tuple): A tuple for locating the element, usually containing the positioning method and the positioning value.
            text (str): The text content to be entered.
        """
        element = self.base_find_element(locator)
        element.clear()
        element.send_keys(text)
```

### Modify `page_login.py`

Currently, only `page_login.py` needs to be modified, and there is no need to touch the test cases because we have provided a unified call interface for the test cases. As long as we don't change this, we can. This is the charm of encapsulation.

```python
from selenium.webdriver.common.by import By
from bases.base import Base

class LoginPage:
    def __init__(self, driver):
        """Initialize the LoginPage instance.
        
        Parameters:
            driver (WebDriver): The passed Selenium WebDriver instance,
                              used to interact with the browser.
        """
        self.__base = Base(driver)
        self.__username_input = (By.ID, "username")
        self.__password_input = (By.ID, "password")
        self.__login_button = (By.ID, "login_button")
        self.__login_message = (By.ID, "login_message")

    def login(self, username, password):
        """Log in to the system

        Parameters:
            username (str): Username
            password (str): Password
        """
        self.__enter_username(username)
        self.__enter_password(password)
        self.__click_login()

    def get_login_message(self) -> str:
        """Get the text of the login message.

        Returns:
            str: The text content of the login message.
        """
        return self.__base.base_find_element(self.__login_message).text

    def __enter_username(self, username):
        """Enter the specified username in the username input box.

        Parameters:
            username (str): The username to be entered
        """
        self.__base.base_send_keys(self.__username_input, username)

    def __enter_password(self, password):
        """Enter the specified password in the password input box.

        Parameters:
            password (str): The password to be entered
        """
        self.__base.base_send_keys(self.__password_input, password)

    def __click_login(self):
        """Wait for the login button to be clickable and perform the click operation."""
        self.__base.base_click(self.__login_button)
```

## Summary

The PO pattern makes the test code clearer, more maintainable, and reusable by encapsulating page elements and operations as objects.

- **Improve code reusability**: Multiple test cases can share the same page object.
- **Enhance maintainability**: Only need to update the page object when the page changes.
- **Improve readability**: Test cases are closer to natural language, such as `login_page.enter_username("test")`.

According to the PO pattern, the project structure can be divided into three layers:

- **Base layer**: Encapsulate common methods.
- **Page object layer**: One class for each page, encapsulating elements and operations.
- **Test case layer**: Call the page object to complete the test.