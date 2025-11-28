# Utility Functions for Web and Text Processing
**Source:** rogue\utils.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `rogue\utils.py` file contains a collection of utility functions designed to facilitate various web and text processing tasks. These functions are valuable for software engineers working on web scraping, network operations, and text analysis. The utilities include hostname comparison, subdomain enumeration, image encoding, network activity monitoring, and token counting using OpenAI's tokenizer. Each function is crafted to address specific needs, making them reusable components in larger systems.

The functions in this file are particularly useful for developers dealing with web-based applications and services. They provide essential operations such as verifying URL hostnames, discovering subdomains, capturing and encoding webpage screenshots, ensuring network stability, and analyzing text data. These utilities can be integrated into enterprise-grade applications to enhance functionality and efficiency.

## Key Concepts & Principles
- **Hostname Comparison:** Ensures two URLs belong to the same domain.
- **Subdomain Enumeration:** Identifies accessible subdomains for a given domain.
- **Base64 Image Encoding:** Converts images to a base64 string for easy transmission.
- **Network Idle Detection:** Monitors network activity to determine idle states.
- **Token Counting:** Analyzes text to determine the number of tokens using a specified model.

## Detailed Technical Analysis

### Hostname Comparison
The `check_hostname` function uses Python's `urlparse` to extract and compare the hostnames of two URLs. This is crucial for validating domain consistency in web applications, ensuring that operations are performed within the same domain context.

### Subdomain Enumeration
`enumerate_subdomains` reads a list of potential subdomains from a file and checks their validity by attempting HTTP requests. This function is useful for security assessments and web crawling, as it helps identify accessible subdomains.

### Base64 Image Encoding
The `get_base64_image` function captures a screenshot of a webpage using Playwright and encodes it in base64. This is particularly useful for embedding images in JSON payloads or transmitting them over text-based protocols.

### Network Idle Detection
`wait_for_network_idle` leverages Playwright's `wait_for_load_state` to pause execution until network activity ceases. This ensures that subsequent operations are performed when the network is stable, reducing the likelihood of errors due to incomplete loading.

### Token Counting
The `count_tokens` function utilizes OpenAI's tokenizer to count tokens in a text. This is essential for applications that need to manage input size constraints or analyze text complexity.

## Enterprise Q&A Bank

1. **Q:** How can I ensure two URLs belong to the same domain?
   **A:** Use the `check_hostname` function to compare their hostnames.

2. **Q:** What is the best way to find subdomains for a given domain?
   **A:** Use the `enumerate_subdomains` function, which tests common subdomain names for accessibility.

3. **Q:** How can I convert a webpage screenshot to a base64 string?
   **A:** Use the `get_base64_image` function to capture and encode the screenshot.

4. **Q:** How do I wait for network activity to become idle in a web application?
   **A:** Use the `wait_for_network_idle` function to pause execution until the network is idle.

5. **Q:** How can I count the number of tokens in a text using OpenAI's tokenizer?
   **A:** Use the `count_tokens` function, specifying the text and model for tokenization.

## Actionable Takeaways
- Use `check_hostname` to validate domain consistency in web operations.
- Employ `enumerate_subdomains` for security assessments and web crawling.
- Utilize `get_base64_image` for embedding images in text-based data exchanges.
- Implement `wait_for_network_idle` to ensure network stability before proceeding with operations.
- Leverage `count_tokens` to manage text input sizes and analyze complexity.

---
**Raw Content:**
```python
import os
import requests
import pathlib
import time
import base64
from urllib.parse import urlparse
import tiktoken

def check_hostname(url_start: str, url_to_check: str) -> bool:
    """
    Check if two URLs have the same hostname.
    
    Args:
        url_start: First URL to compare
        url_to_check: Second URL to compare
        
    Returns:
        bool: True if hostnames match, False otherwise
    """
    url_start_hostname = urlparse(url_start).netloc
    url_to_check_hostname = urlparse(url_to_check).netloc
    return url_start_hostname == url_to_check_hostname

def enumerate_subdomains(url: str) -> list:
    """
    Find valid subdomains for a given domain by testing common subdomain names.
    
    Args:
        url: Base URL to check subdomains for
        
    Returns:
        list: List of valid subdomain URLs that returned HTTP 200
    """ 
    # Extract the root domain from the URL
    parsed = urlparse(url)
    hostname = parsed.netloc
    # Remove any www. prefix if present
    if hostname.startswith('www.'):
        hostname = hostname[4:]
    # Split on dots and take last two parts to get root domain
    parts = hostname.split('.')
    if len(parts) > 2:
        hostname = '.'.join(parts[-2:])

    subdomains_path = pathlib.Path(__file__).parent / "lists" / "subdomains.txt"
    with open(subdomains_path, "r") as f:
        subdomains = f.read().splitlines()

    valid_domains = []
    for subdomain in subdomains:
        url = f"https://{subdomain}.{hostname}"
        try:
            response = requests.get(url, timeout=5)
            if response.status_code == 200:
                print(f"[Info] Found a valid subdomain: {url}")
                valid_domains.append(url)
        except:
            continue

    return valid_domains

def get_base64_image(page) -> str:
    """
    Take a screenshot of the page and return it as a base64 encoded string.
    
    Args:
        page: Playwright page object
        
    Returns:
        str: Base64 encoded screenshot image
    """
    screenshot_path = "temp/temp_screenshot.png"
    page.screenshot(path=screenshot_path)
    with open(screenshot_path, "rb") as image_file:
        base64_image = base64.b64encode(image_file.read()).decode("utf-8")
    return base64_image

def wait_for_network_idle(page, timeout: int = 4000) -> None:
    """
    Wait for network activity to become idle.
    
    Args:
        page: Playwright page object
        timeout: Maximum time to wait in milliseconds (default: 4000)
    """
    try:
        page.wait_for_load_state('networkidle', timeout=timeout)
    except Exception as e:
        # If timeout occurs, give a small delay anyway
        time.sleep(1)  # Fallback delay

def count_tokens(text, model: str = "gpt-4o") -> int:
    """
    Count the number of tokens in a text string using OpenAI's tokenizer.
    
    Args:
        text: The text to tokenize (string or list of dicts with content key)
        model: The model to use for tokenization (default: gpt-4o)
        
    Returns:
        int: The number of tokens in the text
    """
    if isinstance(text, list):
        text = " ".join(str(item.get("content", "")) for item in text)
    
    encoder = tiktoken.encoding_for_model("gpt-4o")
    tokens = encoder.encode(text)
    return len(tokens) 
```