# URL Fetcher Tool: Enterprise-Grade URL Collection
**Source:** ReconAgent\custom_tools\url_enum\fetcher_tool.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `fetcher_tool.py` script is a specialized utility designed to collect and normalize URLs for a given domain using the `waymore` tool. It is part of a larger suite of tools under the ReconAgent project, aimed at facilitating comprehensive URL enumeration. This script exemplifies a reusable utility function that can be integrated into larger systems for web scraping, data collection, or cybersecurity reconnaissance.

The script's value lies in its robust handling of URL collection, normalization, and error management. It provides a structured approach to gathering URLs, ensuring that the data is clean and usable for further processing. The tool's integration with environment variables and its use of subprocess management highlight its adaptability and potential for enterprise-level applications.

## Key Concepts & Principles
- **URL Normalization:** Ensures collected URLs are in a consistent format, removing unnecessary parts like default ports.
- **Error Handling:** Implements try-except blocks to manage subprocess timeouts and other exceptions gracefully.
- **Environment Configuration:** Utilizes environment variables for dynamic configuration, enhancing flexibility.
- **File Management:** Efficiently manages file operations to store and retrieve URL data.

## Detailed Technical Analysis

### URL Collection and Normalization
The core function, `fetch_urls`, orchestrates the URL collection process. It uses the `waymore` tool to gather URLs and then normalizes them by:
- Stripping whitespace and filtering out incomplete URLs.
- Removing redundant port information (e.g., `:80/` in HTTP URLs).
- Filtering out URLs with ellipses or suspicious patterns.

### Error Management
The script includes robust error handling:
- **Timeout Management:** Uses `subprocess.TimeoutExpired` to handle long-running processes.
- **General Exception Handling:** Catches and raises runtime errors with descriptive messages, aiding in debugging and reliability.

### Integration with Environment Variables
The `URLFetcherTool` class leverages environment variables to determine the output directory, showcasing a pattern for flexible configuration in enterprise applications.

## Enterprise Q&A Bank

1. **Q:** How does the script ensure URLs are valid and normalized?
   **A:** It strips whitespace, removes default ports, and filters out URLs with ellipses or suspicious patterns.

2. **Q:** What happens if the URL collection process takes too long?
   **A:** The script raises a `RuntimeError` if the subprocess exceeds the specified timeout.

3. **Q:** How does the tool handle missing environment configurations?
   **A:** It checks for the `RESULT_DIR` environment variable and returns an error message if not set.

4. **Q:** Can this tool be integrated into larger systems?
   **A:** Yes, its modular design and use of environment variables make it suitable for integration into larger systems.

5. **Q:** What is the role of the `waymore` tool in this script?
   **A:** `waymore` is used to perform the initial URL collection from the specified domain.

## Actionable Takeaways
- Ensure environment variables are correctly set before running the tool.
- Use the script's error messages to diagnose and resolve issues quickly.
- Consider extending the normalization logic to handle additional URL patterns.
- Integrate the tool into larger systems for automated URL collection and analysis.
- Regularly update the `waymore` tool to leverage improvements and new features.

---
**Raw Content:**
```python
#!/usr/bin/env python3
from crewai.tools import BaseTool
import os, re, subprocess
from typing import List

def fetch_urls(domain: str, output_root: str) -> List[str]:
    """Collect URLs using waymore."""
    domain_dir = os.path.join(output_root, domain, "phase1")
    save_path = os.path.join(domain_dir, "all_urls.txt")
    raw_path = os.path.join(domain_dir, "raw_urls.txt")

    os.makedirs(domain_dir, exist_ok=True)

    try:
        subprocess.run(["waymore", "-i", domain, "-mode", "U", "-oU", save_path], capture_output=True, text=True, timeout=3600)

        with open(save_path, "r", encoding="utf-8") as f:
            raw_urls = f.readlines()

        with open(raw_path, "w", encoding="utf-8") as f:
            f.writelines(raw_urls)

        normalized_urls = []
        for url in raw_urls:
            url = url.strip()
            if not url:
                continue

            if '...' in url:
                continue
            if re.search(r'/\.\.\./|/\.\.$', url):
                continue
            if re.search(r'[a-f0-9]{10,}\.\.\.', url):
                continue

            if url.startswith("http://") and ":80/" in url:
                url = url.replace(":80/", "/", 1)

            normalized_urls.append(url)

        with open(save_path, "w", encoding="utf-8") as f:
            f.write("\n".join(normalized_urls) + "\n")

        with open(save_path, "r", encoding="utf-8") as fp:
            urls = [line.strip() for line in fp if line.strip()]

        return urls

    except subprocess.TimeoutExpired:
        raise RuntimeError("URL collection timed out")
    except Exception as e:
        raise RuntimeError(f"URL collection failed: {e}")


class URLFetcherTool(BaseTool):
    name: str = "URL Fetcher"
    description: str = "Collect archived URLs for a given domain"

    def _run(self, domain: str) -> str:
        """Execute URL fetching for the specified domain."""
        result_dir = os.getenv("RESULT_DIR")
        if not result_dir:
            return "RESULT_DIR environment variable not set"

        output_root = os.path.dirname(result_dir)

        try:
            urls = fetch_urls(domain, output_root)
            count = len(urls)
            return f"{count} URLs collected for {domain}"
        except Exception as e:
            return f"Error collecting URLs for {domain}: {str(e)}"
```