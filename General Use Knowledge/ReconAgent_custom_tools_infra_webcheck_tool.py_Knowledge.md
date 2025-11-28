# Web Security Checker Tool
**Source:** ReconAgent\custom_tools\infra\webcheck_tool.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The Web Security Checker Tool is a sophisticated utility designed to perform comprehensive security checks on web domains. It leverages a variety of endpoints to assess different aspects of web security, including HTTP security, DNS configurations, infrastructure details, and potential threat intelligence. This tool is essential for enterprises aiming to maintain robust web security postures by identifying vulnerabilities and areas for improvement.

The tool is built on a modular architecture, allowing for easy extension and customization. It uses a combination of environment configurations and dynamic endpoint selection to adapt to different security assessment needs. The tool's logging and session management capabilities ensure reliable and traceable operations, making it suitable for enterprise-grade security assessments.

## Key Concepts & Principles
- **Modular Architecture:** The tool is designed with a modular approach, allowing for easy extension and customization of security checks.
- **Dynamic Endpoint Configuration:** Endpoints can be dynamically configured via environment variables, enabling flexible security assessments.
- **Robust Logging:** Comprehensive logging is implemented to ensure traceability and debugging capabilities.
- **Retry Strategy:** A retry mechanism is in place for handling transient network issues, enhancing the tool's reliability.
- **Domain Validation:** The tool includes robust domain validation logic to ensure accurate and reliable security checks.

## Detailed Technical Analysis

### Architectural Patterns
The tool employs a modular architecture, encapsulating different functionalities into methods and properties. This design pattern facilitates maintainability and scalability, allowing new security checks to be added with minimal impact on existing functionality.

### Core Logic
- **Session Management:** The tool sets up a session with a retry strategy using `requests.Session` and `HTTPAdapter`. This ensures that transient network errors are handled gracefully.
- **Endpoint Configuration:** Endpoints are categorized and can be dynamically selected based on environment variables. This flexibility allows the tool to adapt to different security assessment scenarios.
- **Domain and API Base Validation:** The tool includes methods for validating domain names and API base URLs, ensuring that inputs are correctly formatted and valid.

### Error Handling
The tool implements comprehensive error handling, including specific exceptions for timeouts, connection errors, and HTTP errors. This ensures that the tool can provide meaningful feedback even in the face of network issues.

## Enterprise Q&A Bank

1. **Q:** How does the tool ensure reliable network communication?
   **A:** The tool uses a session with a retry strategy, configured to handle common HTTP status codes that indicate transient issues.

2. **Q:** Can the tool be extended to include additional security checks?
   **A:** Yes, the modular architecture allows for easy addition of new endpoints and categories.

3. **Q:** How does the tool validate domain inputs?
   **A:** The tool uses regular expressions and logical checks to ensure that domain inputs are correctly formatted and valid.

4. **Q:** What logging capabilities does the tool provide?
   **A:** The tool implements comprehensive logging with different levels (INFO, WARNING, ERROR) to ensure traceability and facilitate debugging.

5. **Q:** How does the tool handle configuration?
   **A:** Configuration is managed through environment variables, allowing for dynamic adjustment of endpoints and other settings.

## Actionable Takeaways
- Ensure that environment variables are correctly set for API base and endpoint configurations.
- Regularly update the endpoint categories to include the latest security checks.
- Utilize the logging output to monitor tool performance and troubleshoot issues.
- Validate domain inputs before running security checks to avoid unnecessary errors.
- Leverage the tool's modular architecture to extend its capabilities as new security threats emerge.

---
**Raw Content:**
```python
#!/usr/bin/env python3

from crewai.tools import BaseTool
import requests, json, os, time, logging, re
from urllib.parse import urlparse
from typing import Dict, List, Tuple
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

class WebCheckTool(BaseTool):
    name: str = "Web Security Checker"
    description: str = "Perform comprehensive security checks on web targets using web-check API"

    @property
    def API_BASE(self) -> str:
        return os.getenv("API_BASE", "http://localhost:3000")
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        object.__setattr__(self, 'logger', self._setup_logging())
        object.__setattr__(self, 'session', self._setup_session())
    
    def _setup_logging(self) -> logging.Logger:
        logger = logging.getLogger(f"{__name__}.{self.__class__.__name__}")
        if not logger.handlers:
            handler = logging.StreamHandler()
            formatter = logging.Formatter(
                '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
            )
            handler.setFormatter(formatter)
            logger.addHandler(handler)
            logger.setLevel(logging.INFO)
        return logger
    
    def _setup_session(self) -> requests.Session:
        session = requests.Session()
        retry_strategy = Retry(
            total=3,
            backoff_factor=1,
            status_forcelist=[429, 500, 502, 503, 504],
        )
        adapter = HTTPAdapter(max_retries=retry_strategy)
        session.mount("http://", adapter)
        session.mount("https://", adapter)
        return session
    
    @property
    def ENDPOINT_CATEGORIES(self) -> Dict[str, List[Tuple[str, str, bool]]]:
        return {
            "SECURITY": [
                ("http-security", "http-security", False),
                ("hsts", "hsts", False),
                ("ssl", "ssl", False),
                ("tls-cipher-suites", "tls-cipher-suites", False),
                ("tls-security-config", "tls-security-config", False),
                ("firewall", "firewall", False),
            ],
            "DNS_DOMAIN": [
                ("dns", "dns", False),
                ("dnssec", "dnssec", False),
                ("dns-server", "dns-server", False),
                ("whois", "whois", False),
                ("txt-records", "txt-records", False),
            ],
            "INFRASTRUCTURE": [
                ("headers", "headers", False),
                ("ports", "ports", False),
                ("tech-stack", "tech-stack", False),
                ("server-info", "server-info", False),
                ("location", "location", False),
            ],
            "CONTENT_DISCOVERY": [
                ("robots-txt", "robots-txt", False),
                ("sitemap", "sitemap", False),
                ("security-txt", "security-txt", False),
                ("linked-pages", "linked-pages", False),
                ("social-tags", "social-tags", False),
            ],
            "THREAT_INTEL": [
                ("cookies", "cookies", False),
                ("threats", "threats", False),
                ("block-lists", "block-lists", False),
                ("archives", "archives", False),
            ]
        }

    @property
    def API_ENDPOINTS(self) -> List[Tuple[str, str, bool]]:
        endpoints_config = os.getenv("WEBCHECK_ENDPOINTS", "all")

        if endpoints_config.lower() == "all":
            all_endpoints = []
            for category_endpoints in self.ENDPOINT_CATEGORIES.values():
                all_endpoints.extend(category_endpoints)
            return all_endpoints
        elif endpoints_config.lower() in [cat.lower() for cat in self.ENDPOINT_CATEGORIES.keys()]:
            category_key = endpoints_config.upper()
            return self.ENDPOINT_CATEGORIES.get(category_key, [])
        else:
            endpoint_list = []
            for endpoint in endpoints_config.split(","):
                endpoint = endpoint.strip()
                if endpoint:
                    endpoint_list.append((endpoint, endpoint, False))
            return endpoint_list if endpoint_list else self.ENDPOINT_CATEGORIES["SECURITY"]

    def _validate_domain(self, domain: str) -> bool:
        if not domain or not isinstance(domain, str):
            return False
        
        domain = domain.strip().lower()
        
        if not re.match(r'^[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?(\.[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?)*$', domain):
            return False
        
        if len(domain) > 255:
            return False
            
        parts = domain.split('.')
        if len(parts) < 2:
            return False
            
        return True
    
    def _validate_api_base(self) -> bool:
        if not self.API_BASE:
            self.logger.error("API_BASE environment variable not set")
            return False
        
        try:
            parsed = urlparse(self.API_BASE)
            if not parsed.scheme or not parsed.netloc:
                self.logger.error(f"Invalid API_BASE URL format: {self.API_BASE}")
                return False
        except Exception as e:
            self.logger.error(f"Error parsing API_BASE URL: {e}")
            return False
            
        return True

    def query_api_endpoint(self, key: str, endpoint: str, url_param: str) -> Dict:
        if not self._validate_api_base():
            return {"error": "Invalid or missing API_BASE configuration"}

        url = f"{self.API_BASE}/api/{endpoint}?url={url_param}"
        timeout = int(os.getenv("WEBCHECK_TIMEOUT", "45"))

        try:
            self.logger.debug(f"Querying endpoint {key}: {url}")
            resp = self.session.get(url, timeout=timeout)
            resp.raise_for_status()

            try:
                data = resp.json()
            except json.JSONDecodeError as e:
                self.logger.warning(f"Failed to parse JSON for {key}: {e}")
                return {"error": f"Invalid JSON response: {str(e)}"}

            if endpoint == "archives" and isinstance(data, dict):
                data.pop("scans", None)
            if endpoint == "linked-pages" and isinstance(data, dict):
                if "pages" in data and len(data["pages"]) > 50:
                    data["pages"] = data["pages"][:50]

            self.logger.debug(f"Successfully retrieved data for {key}")
            return data

        except requests.exceptions.Timeout:
            error_msg = f"Timeout occurred for {key} endpoint (timeout: {timeout}s)"
            self.logger.warning(error_msg)
            return {"error": error_msg, "timeout": timeout}
        except requests.exceptions.ConnectionError:
            error_msg = f"Connection error for {key} endpoint"
            self.logger.warning(error_msg)
            return {"error": error_msg}
        except requests.exceptions.HTTPError as e:
            status_code = e.response.status_code if e.response else "unknown"
            error_msg = f"HTTP error {status_code} for {key} endpoint"
            self.logger.warning(error_msg)
            return {"error": error_msg, "status_code": status_code}
        except Exception as e:
            error_msg = f"Unexpected error for {key} endpoint: {str(e)}"
            self.logger.error(error_msg)
            return {"error": error_msg}

    def run_security_checks(self, domain: str) -> Dict:
        if not self._validate_domain(domain):
            raise ValueError(f"Invalid domain format: {domain}")
        
        domain = domain.strip().lower()
        target_full = f"https://{domain}"
        results = {}
        
        self.logger.info(f"Starting security checks for domain: {domain}")
        
        total_checks = len(self.API_ENDPOINTS)
        for i, (key, endpoint, needs_https) in enumerate(self.API_ENDPOINTS, 1):
            self.logger.info(f"Running check {i}/{total_checks}: {key}")
            
            param = target_full if needs_https else domain
            result = self.query_api_endpoint(key, endpoint, param)
            results[key] = result
            
            if "error" in result:
                self.logger.warning(f"Check {key} failed: {result['error']}")
            else:
                self.logger.debug(f"Check {key} completed successfully")
            
            if i < total_checks:
                time.sleep(0.5)
        
        success_count = sum(1 for r in results.values() if "error" not in r)
        self.logger.info(f"Security checks completed: {success_count}/{total_checks} successful")
        
        return results

    def analyze_results_by_category(self, results: Dict) -> Dict:
        analysis = {
            "category_summary": {},
            "security_insights": [],
            "recon_opportunities": [],
            "critical_findings": []
        }

        for category, endpoints in self.ENDPOINT_CATEGORIES.items():
            category_results = {}
            successful = 0
            failed = 0

            for key, endpoint, _ in endpoints:
                if key in results:
                    category_results[key] = results[key]
                    if "error" not in results[key]:
                        successful += 1
                    else:
                        failed += 1

            analysis["category_summary"][category] = {
                "successful": successful,
                "failed": failed,
                "total": len(endpoints),
                "success_rate": (successful / len(endpoints)) * 100 if endpoints else 0,
                "results": category_results
            }

        security_results = analysis["category_summary"].get("SECURITY", {}).get("results", {})
        if "http-security" in security_results and "error" not in security_results["http-security"]:
            http_sec = security_results["http-security"]
            missing_headers = []
            if not http_sec.get("strictTransportPolicy", True):
                missing_headers.append("HSTS")
            if not http_sec.get("contentSecurityPolicy", True):
                missing_headers.append("CSP")
            if not http_sec.get("xContentTypeOptions", True):
                missing_headers.append("X-Content-Type-Options")

            if missing_headers:
                analysis["security_insights"].append({
                    "type": "Missing Security Headers",
                    "severity": "Medium",
                    "details": f"Missing: {', '.join(missing_headers)}"
                })

        if "ssl" in security_results and "error" not in security_results["ssl"]:
            ssl_data = security_results["ssl"]
            if isinstance(ssl_data, dict):
                analysis["security_insights"].append({
                    "type": "SSL Certificate Info",
                    "severity": "Info",
                    "details": f"Issuer: {ssl_data.get('issuer', 'Unknown')}"
                })

        content_results = analysis["category_summary"].get("CONTENT_DISCOVERY", {}).get("results", {})

        if "robots-txt" in content_results and "error" not in content_results["robots-txt"]:
            analysis["recon_opportunities"].append({
                "type": "robots.txt Analysis",
                "details": "robots.txt file found - analyze for hidden directories"
            })

        if "sitemap" in content_results and "error" not in content_results["sitemap"]:
            analysis["recon_opportunities"].append({
                "type": "Sitemap Discovery",
                "details": "Sitemap available - enumerate additional endpoints"
            })

        infra_results = analysis["category_summary"].get("INFRASTRUCTURE", {}).get("results", {})

        if "tech-stack" in infra_results and "error" not in infra_results["tech-stack"]:
            tech_data = infra_results["tech-stack"]
            if isinstance(tech_data, dict) and tech_data:
                analysis["recon_opportunities"].append({
                    "type": "Technology Stack",
                    "details": "Technology fingerprints available for targeted research"
                })

        return analysis

    def save_results(self, domain: str, results: Dict) -> str:
        results_root = os.getenv("RESULT_DIR")
        if not results_root:
            raise ValueError("RESULT_DIR environment variable not set")
        
        phase6_dir = os.path.join(results_root, "phase6")
        
        try:
            os.makedirs(phase6_dir, exist_ok=True)
        except OSError as e:
            raise ValueError(f"Failed to create phase6 directory: {e}")
        
        timestamp = int(time.time())
        output_file = os.path.join(phase6_dir, "webcheck.json")
        
        analysis = self.analyze_results_by_category(results)
        enhanced_summary = {
            "total_checks": len(results),
            "successful_checks": sum(1 for r in results.values() if "error" not in r),
            "failed_checks": sum(1 for r in results.values() if "error" in r),
            "endpoints_used": list(results.keys()),
            "categories_analyzed": list(analysis["category_summary"].keys()),
            "security_insights_count": len(analysis["security_insights"]),
            "recon_opportunities_count": len(analysis["recon_opportunities"])
        }

        try:
            with open(output_file, 'w', encoding='utf-8') as f:
                json.dump({
                    "domain": domain,
                    "timestamp": timestamp,
                    "results": results,
                    "analysis": analysis,
                    "summary": enhanced_summary
                }, f, indent=2, ensure_ascii=False)

            self.logger.info(f"Enhanced results saved to: {output_file}")
            return output_file
            
        except (OSError, IOError) as e:
            raise ValueError(f"Failed to save results file: {e}")

    def _run(self, domain: str) -> str:
        try:
            if not domain:
                raise ValueError("Domain parameter is required")
            
            domain = domain.strip()
            if domain.startswith(('http://', 'https://')):
                parsed = urlparse(domain)
                domain = parsed.netloc or parsed.path
            
            self.logger.info(f"Starting web security check for domain: {domain}")
            
            results = self.run_security_checks(domain)
            output_file = self.save_results(domain, results)

            analysis = self.analyze_results_by_category(results)
            success_count = sum(1 for r in results.values() if "error" not in r)
            total_count = len(results)
            error_count = total_count - success_count

            summary_parts = [
                f"Enhanced WebCheck analysis completed for {domain}:",
                f"• {success_count}/{total_count} endpoints successful",
                f"• {len(analysis['category_summary'])} categories analyzed",
                f"• {len(analysis['security_insights'])} security insights found",
                f"• {len(analysis['recon_opportunities'])} reconnaissance opportunities identified"
            ]

            if error_count > 0:
                summary_parts.append(f"• {error_count} endpoints failed")

            summary_parts.extend([
                f"• Results saved to {output_file}",
                f"• Categories: {', '.join(analysis['category_summary'].keys())}"
            ])

            summary = "\n".join(summary_parts)

            self.logger.info("Enhanced WebCheck analysis completed successfully")
            return summary
            
        except ValueError as e:
            error_msg = f"Validation error for {domain}: {str(e)}"
            self.logger.error(error_msg)
            return error_msg
        except Exception as e:
            error_msg = f"Web security check failed for {domain}: {str(e)}"
            self.logger.error(error_msg, exc_info=True)
            return error_msg 
```