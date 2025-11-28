# Noir JavaScript Scanner Tool
**Source:** ReconAgent\custom_tools\js_analysis\noir_scanner_tool.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The Noir JavaScript Scanner Tool is a specialized utility designed to perform static analysis on JavaScript files using the OWASP NOIR framework. This tool is part of a larger suite aimed at enhancing security by identifying vulnerabilities in JavaScript applications. It automates the scanning process, integrates with existing infrastructure through environment variables, and outputs results in a structured JSON format. The tool is particularly valuable for organizations looking to incorporate security scanning into their CI/CD pipelines, ensuring that JavaScript code is scrutinized for potential security flaws before deployment.

The tool's architecture leverages environment variables to determine the location of JavaScript files and outputs, making it adaptable to various deployment environments. By utilizing the OWASP NOIR framework, it provides a robust analysis of JavaScript applications built on popular frameworks such as Express, Restify, Fastify, Koa, and NestJS. This makes it a versatile tool for enterprises with diverse JavaScript codebases.

## Key Concepts & Principles
- **Static Analysis**: The process of analyzing code for vulnerabilities without executing it.
- **OWASP NOIR**: A framework for AST-based security analysis of JavaScript applications.
- **Environment Variables**: Used to configure the tool's input and output directories.
- **JSON Output**: Ensures that the results are easily consumable by other tools or systems.

## Detailed Technical Analysis
### Environment Configuration
The tool relies on the `RESULT_DIR` environment variable to locate the directory where JavaScript files are stored and where the output should be written. This design allows for flexibility in different deployment scenarios.

### Command Execution
The core functionality is executed via a subprocess call to the NOIR command-line tool. The command is constructed with several options:
- **`-b`**: Specifies the base directory for JavaScript files.
- **`--format json`**: Outputs the results in JSON format.
- **`-o`**: Defines the output file path.
- **`-P`**: Enables passive scanning with a severity level of "low".
- **`-t`**: Targets specific JavaScript frameworks for analysis.

### Error Handling
The tool includes robust error handling to manage scenarios where environment variables are not set or directories do not exist. It returns structured JSON error messages to facilitate debugging and integration with other systems.

## Enterprise Q&A Bank
1. **Q: How does the NoirScannerTool integrate with CI/CD pipelines?**
   A: By using environment variables and JSON output, it can be easily incorporated into automated build and deployment processes.

2. **Q: What JavaScript frameworks does the tool support?**
   A: It supports Express, Restify, Fastify, Koa, and NestJS.

3. **Q: How does the tool handle missing directories or files?**
   A: It returns a JSON response indicating the error, such as "NO_FILES" if the JavaScript folder is not found.

4. **Q: Can the severity level of the scan be adjusted?**
   A: Yes, the tool uses the `-P` option for passive scanning with a default severity level of "low".

5. **Q: What happens if the NOIR scan fails?**
   A: The tool catches exceptions and returns a JSON error message with details of the failure.

## Actionable Takeaways
- Ensure the `RESULT_DIR` environment variable is set correctly before running the tool.
- Verify that the JavaScript files are located in the expected directory structure.
- Integrate the tool's JSON output into security dashboards or alerting systems for real-time monitoring.
- Regularly update the NOIR framework to leverage the latest security analysis capabilities.
- Consider customizing the scan command to adjust severity levels or target specific frameworks as needed.

---
**Raw Content:**
```python
#!/usr/bin/env python3
from crewai.tools import BaseTool
import os, subprocess, json

class NoirScannerTool(BaseTool):
    name: str = "NOIR JavaScript Scanner"
    description: str = "Scan JavaScript files with OWASP NOIR AST-based analysis"

    def _run(self) -> str:
        """Run NOIR scanner on downloaded JavaScript files."""
        try:
            result_dir = os.getenv("RESULT_DIR")
            if not result_dir:
                return json.dumps({"status": "ERROR", "message": "RESULT_DIR environment variable not set"})

            phase4_dir = os.path.join(result_dir, "phase4")
            js_folder = os.path.join(phase4_dir, "js")
            output_file = os.path.join(phase4_dir, "js_noir.json")

            if not os.path.exists(js_folder):
                return json.dumps({
                    "status": "NO_FILES",
                    "message": f"JavaScript folder not found: {js_folder}",
                    "endpoints": 0
                })

            cmd = ["noir", "-b", js_folder, "--format", "json", "-o", output_file, "-P", "--passive-scan-severity", "low", "-t", "js_express,js_restify,js_fastify,js_koa,js_nestjs", "--no-log"]

            subprocess.run(cmd, capture_output=True, text=True)

            return json.dumps({
                "status": "SUCCESS",
                "output_file": output_file
            })
        except Exception as e:
            return json.dumps({"status": "ERROR", "message": f"NOIR scan failed: {str(e)}"})
```