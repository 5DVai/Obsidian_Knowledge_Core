# HTML Parsing and Extraction Utility
**Source:** rogue\parser.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `HTMLParser` class in `rogue\parser.py` is a comprehensive utility designed to parse HTML content and extract various elements such as URLs, forms, scripts, technologies, meta information, JavaScript libraries, and potential API endpoints. This utility is particularly valuable for web scraping, data extraction, and analysis tasks where understanding the structure and components of a web page is crucial. The parser leverages the BeautifulSoup library to navigate and manipulate HTML documents, providing a robust framework for extracting meaningful data from web pages.

The utility's design emphasizes reusability and extensibility, making it suitable for enterprise applications that require consistent and reliable HTML parsing capabilities. By focusing on extracting domain-specific information, such as URLs from the same domain and technology indicators, the parser aligns with common business needs in web analytics, SEO, and competitive analysis.

## Key Concepts & Principles
- **HTML Parsing:** Utilizing BeautifulSoup to traverse and extract data from HTML documents.
- **Data Extraction:** Identifying and retrieving specific elements like URLs, forms, and scripts.
- **Domain-Specific Filtering:** Ensuring extracted URLs and scripts are relevant to the base domain.
- **Technology Detection:** Recognizing CMS, frameworks, and libraries used in web pages.
- **Reusability:** Designing functions to be modular and applicable across different web pages.

## Detailed Technical Analysis

### Initialization and Base URL Handling
The `HTMLParser` class initializes with a base URL, which is crucial for resolving relative URLs found within the HTML content. This approach ensures that all extracted URLs are complete and accurate, facilitating further processing or analysis.

### Pretty Printing
The `pretty_print` method formats the extracted data into a human-readable string. This feature is particularly useful for debugging and presenting the parsed information in a structured manner.

### URL Extraction
The `_extract_urls` method focuses on retrieving anchor tags' URLs that belong to the same domain as the base URL. This domain-specific filtering is achieved by comparing the main domain parts, ensuring relevance and reducing noise from external links.

### Form and Script Extraction
The parser extracts forms and scripts, capturing details such as form actions, methods, and input fields, as well as script sources and types. This information is vital for understanding the interactive elements of a web page and potential client-side logic.

### Technology and Meta Information Detection
By analyzing meta tags and common patterns in HTML content, the parser identifies technologies like CMS and JavaScript frameworks. This capability provides insights into the technological stack of a website, which can inform strategic decisions in areas like technology adoption and competitive analysis.

### JavaScript Libraries and Endpoints
The utility identifies JavaScript libraries by examining script sources and detects potential API endpoints through patterns in script content. These features are essential for developers looking to integrate or interact with web services.

## Enterprise Q&A Bank

1. **Q:** How does the parser ensure URLs are from the same domain?
   **A:** It compares the main domain parts of the base URL and extracted URLs, filtering out those that do not match.

2. **Q:** What libraries does the parser use for HTML parsing?
   **A:** The parser uses BeautifulSoup, a popular library for parsing HTML and XML documents.

3. **Q:** Can the parser detect the use of specific JavaScript frameworks?
   **A:** Yes, it identifies frameworks like React, Angular, and Vue.js by analyzing script sources and HTML content.

4. **Q:** How does the parser handle relative URLs in forms and scripts?
   **A:** It resolves them using the base URL, ensuring all URLs are absolute and accurate.

5. **Q:** What is the significance of extracting meta information?
   **A:** Meta information provides insights into the page's metadata, which can include SEO-related data and technology indicators.

6. **Q:** How does the parser differentiate between inline and external scripts?
   **A:** It checks for the presence of a `src` attribute; inline scripts are identified by their content.

7. **Q:** What patterns does the parser use to detect API endpoints?
   **A:** It uses regex patterns to find common AJAX and fetch call structures within script content.

8. **Q:** Is the parser capable of extracting non-standard HTML elements?
   **A:** While it focuses on standard elements, it can be extended to handle custom elements by modifying the extraction methods.

9. **Q:** How does the parser contribute to SEO analysis?
   **A:** By extracting URLs, meta information, and technology indicators, it provides data that can be used to assess a site's SEO strategy.

10. **Q:** Can the parser be integrated into larger web scraping frameworks?
    **A:** Yes, its modular design allows for easy integration into broader scraping and data analysis workflows.

## Actionable Takeaways
- Ensure the base URL is correctly set to resolve relative URLs accurately.
- Use the `pretty_print` method for debugging and presenting parsed data.
- Extend extraction methods to handle additional HTML elements or custom tags as needed.
- Leverage the parser's technology detection capabilities for competitive analysis and technology stack assessments.
- Integrate the parser into automated workflows for continuous web monitoring and data extraction.

---
**Raw Content:**
```python
from bs4 import BeautifulSoup
import math
import re
from typing import Dict, List, Tuple, Any
from urllib.parse import urljoin, urlparse
import re

class HTMLParser:
    def __init__(self):
        """
        Initialize the HTML parser with a base URL for resolving relative URLs
        
        Args:
            base_url (str): The base URL of the page being parsed
        """
        self.base_url = None
        
    def pretty_print(self, data: Dict[str, Any]) -> str:
        """
        Pretty print the parsed data as a nicely formatted string
        
        Args:
            data (Dict[str, Any]): The parsed data to format
            
        Returns:
            str: A nicely formatted string representation of the data
        """
        output = []
        output.append("Parsed HTML Data:")
        output.append("================")
        
        if data.get('urls'):
            output.append("\nURLs Found:")
            for url in data['urls']:
                output.append(f"  • {url['text']}: {url['href']}")
                
        if data.get('forms'):
            output.append("\nForms Found:")
            for i, form in enumerate(data['forms'], 1):
                output.append(f"\n  Form #{i}:")
                output.append(f"    Action: {form['action']}")
                output.append(f"    Method: {form['method']}")
                if form['inputs']:
                    output.append("    Inputs:")
                    for input_field in form['inputs']:
                        output.append(f"      - {input_field['name']} ({input_field['type']})")
                        
  
        return "\n".join(output)
        
    def parse(self, html_content: str, url: str) -> Dict[str, Any]:
        """
        Parse HTML content and extract relevant information
        
        Args:
            html_content (str): Raw HTML content to parse
            url (str): The URL of the page being parsed
            
        Returns:
            dict: Dictionary containing extracted information
        """
        soup = BeautifulSoup(html_content, 'html.parser')
        self.base_url = url
        data = {
            'urls': self._extract_urls(soup),
            'forms': self._extract_forms(soup),
            'scripts': self._extract_scripts(soup),
            'technologies': self._extract_technologies(soup),
            'meta_info': self._extract_meta_info(soup),
            'javascript_libraries': self._extract_js_libraries(soup),
            'endpoints': self._extract_endpoints(soup),
        }
        return data

    def _extract_urls(self, soup: BeautifulSoup) -> List[Dict[str, str]]:
        """Extract all unique URLs from anchor tags with their text content that belong to the same domain"""
        from urllib.parse import urlparse
        
        seen_urls = set()
        urls = []
        base_domain = urlparse(self.base_url).netloc.split('.')[-2:]  # Get main domain without subdomains
        
        for a_tag in soup.find_all('a', href=True):
            full_url = urljoin(self.base_url, a_tag['href'])
            
            # Skip if we've seen this URL before
            if full_url in seen_urls:
                continue
                
            parsed_url = urlparse(full_url)
            url_domain = parsed_url.netloc.split('.')[-2:]
            
            # Only include URLs from same domain (including subdomains)
            if url_domain == base_domain:
                url = {
                    'href': full_url,
                    'text': a_tag.get_text(strip=True),
                }
                urls.append(url)
                seen_urls.add(full_url)
                
        return urls
        
    def _extract_forms(self, soup: BeautifulSoup) -> List[Dict[str, Any]]:
        """Extract all unique forms with their full HTML and input fields"""
        seen_forms = set()
        forms = []
        for form in soup.find_all('form'):
            form_html = str(form)
            
            # Skip if we've seen this exact form HTML before
            if form_html in seen_forms:
                continue
                
            form_data = {
                'action': urljoin(self.base_url, form.get('action', '')),
                'method': form.get('method', 'get'),
                'outer_html': form_html,
                'inputs': []
            }
            
            # Extract input fields
            for input_field in form.find_all(['input', 'textarea', 'select']):
                input_data = {
                    'type': input_field.get('type', 'text'),
                    'name': input_field.get('name', ''),
                    'id': input_field.get('id', ''),
                    'required': input_field.has_attr('required'),
                    'outer_html': str(input_field)
                }
                form_data['inputs'].append(input_data)
                
            forms.append(form_data)
            seen_forms.add(form_html)
            
        return forms
        
    def _extract_scripts(self, soup: BeautifulSoup) -> List[Dict[str, str]]:
        """Extract all unique script tags and their sources"""
        seen_scripts = set()
        scripts = []
        base_domain = urlparse(self.base_url).netloc.split('.')[-2:]  # Get main domain without subdomains
        
        for script in soup.find_all('script'):
            src = script.get('src', '')
            if src:
                full_src = urljoin(self.base_url, src)
                parsed_url = urlparse(full_src)
                script_domain = parsed_url.netloc.split('.')[-2:]
                
                # Skip if not from same domain or already seen
                if script_domain != base_domain or full_src in seen_scripts:
                    continue
                    
                script_data = {
                    'src': full_src,
                    'type': script.get('type', 'text/javascript'),
                }
                scripts.append(script_data)
                seen_scripts.add(full_src)
            else:
                # For inline scripts, use content hash to detect duplicates
                content = script.string or ''
                if content in seen_scripts:
                    continue
                script_data = {
                    'src': content,
                    'type': script.get('type', 'text/javascript'),
                }
                scripts.append(script_data)
                seen_scripts.add(content)
                
        return scripts
        
    def _extract_technologies(self, soup: BeautifulSoup) -> List[str]:
        """Extract technology indicators from HTML content"""
        technologies = set()
        
        # Check meta tags for technology indicators
        for meta in soup.find_all('meta'):
            name = meta.get('name', '').lower()
            content = meta.get('content', '').lower()
            
            # Common CMS and framework indicators
            if 'generator' in name and content:
                technologies.add(content)
            elif 'powered-by' in name and content:
                technologies.add(content)
        
        # Check for common technology patterns in HTML
        html_content = str(soup).lower()
        
        # CMS Detection
        if 'wp-content' in html_content or 'wordpress' in html_content:
            technologies.add('WordPress')
        if 'drupal' in html_content:
            technologies.add('Drupal')
        if 'joomla' in html_content:
            technologies.add('Joomla')
        
        # Framework Detection
        if 'react' in html_content:
            technologies.add('React')
        if 'angular' in html_content:
            technologies.add('Angular')
        if 'vue' in html_content:
            technologies.add('Vue.js')
        if 'bootstrap' in html_content:
            technologies.add('Bootstrap')
        
        # Server Technology Detection (from comments or script paths)
        if 'php' in html_content:
            technologies.add('PHP')
        if 'asp.net' in html_content or 'aspx' in html_content:
            technologies.add('ASP.NET')
        if 'jsp' in html_content:
            technologies.add('JSP')
        
        return list(technologies)
    
    def _extract_meta_info(self, soup: BeautifulSoup) -> Dict[str, str]:
        """Extract meta information from HTML head"""
        meta_info = {}
        
        for meta in soup.find_all('meta'):
            name = meta.get('name', '')
            content = meta.get('content', '')
            
            if name and content:
                meta_info[name.lower()] = content
        
        return meta_info
    
    def _extract_js_libraries(self, soup: BeautifulSoup) -> List[str]:
        """Extract JavaScript libraries from script sources"""
        libraries = set()
        
        for script in soup.find_all('script', src=True):
            src = script['src'].lower()
            
            # Common JS libraries
            if 'jquery' in src:
                libraries.add('jQuery')
            elif 'react' in src:
                libraries.add('React')
            elif 'angular' in src:
                libraries.add('Angular')
            elif 'vue' in src:
                libraries.add('Vue.js')
            elif 'bootstrap' in src:
                libraries.add('Bootstrap')
            elif 'lodash' in src:
                libraries.add('Lodash')
            elif 'moment' in src:
                libraries.add('Moment.js')
            elif 'd3' in src:
                libraries.add('D3.js')
        
        return list(libraries)
    
    def _extract_endpoints(self, soup: BeautifulSoup) -> List[str]:
        """Extract potential API endpoints and interesting paths"""
        endpoints = set()
        
        # Extract from form actions
        for form in soup.find_all('form'):
            action = form.get('action', '')
            if action and action != '#':
                endpoints.add(action)
        
        # Extract from AJAX calls in script content
        for script in soup.find_all('script'):
            if script.string:
                content = script.string
                # Look for common AJAX patterns
                ajax_patterns = [
                    r'["\']([^"\']*(?:api|ajax|endpoint)[^"\']*)["\']',
                    r'url\s*:\s*["\']([^"\']+)["\']',
                    r'fetch\s*\(\s*["\']([^"\']+)["\']',
                    r'\.get\s*\(\s*["\']([^"\']+)["\']',
                    r'\.post\s*\(\s*["\']([^"\']+)["\']'
                ]
                
                for pattern in ajax_patterns:
                    matches = re.findall(pattern, content, re.IGNORECASE)
                    endpoints.update(matches)
        
        return list(endpoints)
```