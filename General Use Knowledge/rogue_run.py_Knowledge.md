# Rogue - LLM Powered Security Scanner
**Source:** rogue\run.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `rogue\run.py` script is a command-line interface for an AI-powered web application security testing tool named "Rogue". This tool leverages large language models (LLMs) to perform automated penetration testing, offering features such as intelligent vulnerability discovery, advanced payload generation, context-aware testing, and automated exploit verification. The script is designed to be flexible, allowing users to specify various parameters for the security scan, including the target URL, the number of testing plans, and the model to use. It also supports additional features like subdomain enumeration and scope expansion.

The script is valuable for its integration of AI capabilities into security testing, providing a sophisticated approach to identifying vulnerabilities in web applications. It encapsulates a reusable pattern for building command-line tools that interface with AI models, making it a potential reference for developing similar applications in other domains.

## Key Concepts & Principles
- **AI-Powered Security Testing:** Utilizes LLMs to enhance traditional security testing methods.
- **Command-Line Interface (CLI):** Provides a flexible and user-friendly way to interact with the security scanner.
- **Iterative Planning:** Allows for dynamic adjustment of testing plans based on the complexity of the target.
- **Scope Expansion:** Enables comprehensive testing by including discovered URLs and subdomains.
- **Environment Configuration:** Requires setting an API key for accessing AI services.

## Detailed Technical Analysis

### Command-Line Argument Parsing
The script uses Python's `argparse` module to define a comprehensive set of command-line arguments. This allows users to customize the behavior of the security scanner extensively. Key arguments include:
- `-u/--url`: Specifies the target URL for the security scan.
- `-p/--num-plans`: Determines the number of security testing plans to generate.
- `-i/--max-iterations`: Sets the maximum number of iterations per plan.
- `-m/--model`: Chooses the LLM model to use for the scan.

### AI Integration
The script checks for the presence of an `OPENAI_API_KEY` environment variable, which is necessary for accessing the AI services. This integration highlights the importance of secure and configurable access to external APIs in enterprise applications.

### Security Testing Workflow
The script initializes an `Agent` object with the specified parameters and executes the `run` method to start the security scan. This encapsulation of the scanning logic within an agent class demonstrates a clean separation of concerns and promotes reusability.

## Enterprise Q&A Bank

1. **Q:** How does the script ensure secure access to AI services?
   **A:** It requires an `OPENAI_API_KEY` environment variable, ensuring that API keys are not hardcoded and can be managed securely.

2. **Q:** What is the purpose of iterative planning in this context?
   **A:** Iterative planning allows the tool to adapt the number of testing plans based on the complexity of the target, optimizing resource usage and scan effectiveness.

3. **Q:** How can the script be extended to support additional security checks?
   **A:** Developers can modify the `Agent` class to include new security checks or integrate additional AI models for enhanced analysis.

4. **Q:** What are the benefits of using a CLI for this tool?
   **A:** A CLI provides flexibility, ease of automation, and the ability to integrate with other tools and scripts in a DevOps pipeline.

5. **Q:** How does the script handle errors during the scan?
   **A:** It includes exception handling to manage interruptions and failures gracefully, providing informative messages to the user.

## Actionable Takeaways
- Ensure the `OPENAI_API_KEY` is securely set in the environment before running the script.
- Use the CLI to customize scans according to specific security requirements.
- Leverage the script's flexibility to integrate it into automated security testing workflows.
- Consider extending the `Agent` class to incorporate additional security features or AI models.
- Regularly update the AI models and security checks to keep up with emerging threats.

---
**Raw Content:**
```python
import argparse
import os
from agent import Agent

def main():
    banner = """
    ╔══════════════════════════════════════════════════════════════════════╗
    ║                                                                      ║
    ║                  Rogue - LLM Powered Security Scanner                ║
    ║                                                                      ║
    ║           Automated Penetration Testing with LLM Intelligence        ║
    ║                                                                      ║
    ║     [+] Intelligent vulnerability discovery                          ║
    ║     [+] Advanced payload generation                                  ║
    ║     [+] Context-aware testing                                        ║
    ║     [+] Automated exploit verification                               ║
    ║                                                                      ║
    ║                -- Happy hunting, use responsibly! --                 ║
    ║                                                                      ║
    ╚══════════════════════════════════════════════════════════════════════╝
    """
    
    parser = argparse.ArgumentParser(
        description='AI-Powered Web Application Security Testing Agent',
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog='''...
    )
    
    parser.add_argument('-u', '--url', 
                        required=True,
                        help='Target URL to test')

    parser.add_argument('-e', '--expand',
                        action='store_true',
                        default=False,
                        help='Expand testing to discovered URLs')
    
    parser.add_argument('-s', '--subdomains',
                        action='store_true',
                        default=False,
                        help='Perform subdomain enumeration')

    parser.add_argument('-m', '--model',
                        choices=['o3-mini', 'o1-preview', 'o4-mini'],
                        default='o4-mini',
                        help='LLM model to use (default: o4-mini)')
    
    parser.add_argument('-o', '--output',
                        default='security_results',
                        help='Output directory for results (default: security_results)')
    
    parser.add_argument('-i', '--max-iterations',
                        type=int,
                        default=10,
                        help='Maximum iterations per plan of attack (default: 10)')

    parser.add_argument('-p', '--num-plans',
                        type=int,
                        default=1,
                        help='Number of security testing plans to generate per page. Uses iterative planning: fixed plans are divided into 3 batches (33%% each), unlimited plans (-1) generate 5 plans per batch with adaptive learning. Default: 10')

    parser.add_argument('--disable-baseline-checks', 
                        action='store_true', 
                        help='Disable OWASP Top 10 baseline security checks')
    
    parser.add_argument('--max-plans', 
                        type=int, 
                        default=None,
                        help='Maximum number of plans to generate (default: unlimited)')

    parser.add_argument('--disable-rag',
                        action='store_true',
                        default=True,
                        help='Disable RAG knowledge fetching for faster startup')

    parser.add_argument('--disable-iterative',
                        action='store_true',
                        default=False,
                        help='Disable iterative planning and generate all plans at once (legacy mode)')

    parser.add_argument('--additional-instructions',
                        type=str,
                        default='',
                        help='Additional instructions for the security testing agent')

    args = parser.parse_args()

    # Validation
    if not args.url:
        parser.error("URL is required. Use -u or --url to specify the target URL.")
    
    if not args.url.startswith(('http://', 'https://')):
        parser.error("URL must start with http:// or https://")

    print(banner)
    
    print(f"[*] Starting security scan...")
    print(f"[*] Target URL: {args.url}")
    print(f"[*] Using model: {args.model}")
    
    if args.num_plans == -1:
        print(f"[*] Plans per page: Unlimited (15-25+ comprehensive tests with contextual CVE intelligence)")
    elif args.max_plans:
        print(f"[*] Plans per page: {args.max_plans}")
    else:
        print(f"[*] Plans per page: {args.num_plans} (or dynamic based on page complexity)")
    
    print(f"[*] Max iterations per plan: {args.max_iterations}")
    print(f"[*] Results will be saved to: {args.output}")
    
    # Check if OpenAI API key is set
    if not os.getenv('OPENAI_API_KEY'):
        print("\n[Error] OPENAI_API_KEY environment variable is not set!")
        print("Please set your OpenAI API key:")
        print("export OPENAI_API_KEY='your-api-key-here'")
        return
    
    # Create agent with options - combining both parameter sets
    agent = Agent(
        starting_url=args.url,
        expand_scope=args.expand,
        enumerate_subdomains=args.subdomains,
        model=args.model,
        output_dir=args.output,
        max_iterations=args.max_iterations,
        num_plans=args.num_plans,
        enable_baseline_checks=not args.disable_baseline_checks,
        max_plans=args.max_plans,
        disable_rag=args.disable_rag,
        disable_iterative=args.disable_iterative,
        additional_instructions=args.additional_instructions
    )
    
    # Run the scan
    try:
        agent.run()
        print(f"\n[✅] Scan completed successfully!")
        print(f"[*] Results saved to: {args.output}")
    except KeyboardInterrupt:
        print("\n[*] Scan interrupted by user")
    except Exception as e:
        print(f"\n[❌] Scan failed: {e}")

if __name__ == "__main__":
    main() 
```