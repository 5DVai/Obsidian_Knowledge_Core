# Large Language Model Interface for Security Testing
**Source:** rogue\llm.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `LLM` class in the `rogue\llm.py` file provides an interface for leveraging large language models (LLMs) in the context of security testing. This module is designed to facilitate security assessments and vulnerability discovery by interacting with LLMs using system prompts enriched with live security knowledge. The class integrates with OpenAI and Anthropic APIs to perform sophisticated security analysis, making it a valuable tool for red teams and security researchers. The system is equipped with a comprehensive set of tools and methodologies to simulate expert penetration testing, ensuring thorough exploration of potential vulnerabilities.

## Key Concepts & Principles
- **Security Testing Automation:** Automates the process of security testing using LLMs to simulate human-like reasoning and decision-making.
- **System Prompts:** Utilizes detailed system prompts to guide the LLM in performing specific security tasks.
- **Integration with Security Knowledge Bases:** Incorporates knowledge from established security resources like OWASP, CAPEC, and PortSwigger.
- **Toolset for Security Analysis:** Provides a suite of tools for interacting with web pages and APIs, including executing JavaScript, filling forms, and navigating pages.
- **Reasoning and Response Generation:** Offers methods to generate reasoned responses and outputs based on conversation history and input prompts.

## Detailed Technical Analysis

### Architectural Design
The `LLM` class is architected to serve as a bridge between security testing practices and LLM capabilities. It initializes with a security knowledge base and constructs a system prompt that outlines the security testing framework. The class is designed to be extensible, allowing for the integration of additional security tools and techniques as needed.

### Core Functionality
- **Initialization:** The constructor initializes the LLM client with an API key and sets up the system prompt with security knowledge.
- **Reasoning Method:** The `reason` method generates responses based on conversation history, allowing for different levels of reasoning effort.
- **Output Method:** The `output` method generates deterministic or probabilistic responses based on the input prompt and temperature setting.

### Security Testing Workflow
The system prompt outlines a detailed workflow for security testing, including input formats, tool usage, and output expectations. The workflow emphasizes thorough exploration of vulnerabilities and mandates the use of advanced techniques and tools to simulate expert-level penetration testing.

## Enterprise Q&A Bank

1. **Q:** How does the `LLM` class enhance security testing?
   **A:** It automates security assessments by leveraging LLMs to simulate expert reasoning and decision-making, guided by a comprehensive system prompt.

2. **Q:** What are the key components of the system prompt?
   **A:** The system prompt includes security knowledge, input formats, tool descriptions, and rules for conducting thorough security testing.

3. **Q:** How does the class ensure the use of advanced security techniques?
   **A:** The system prompt mandates the use of expert-level techniques and tools, discouraging simple or repetitive actions.

4. **Q:** Can the `LLM` class be extended with additional security tools?
   **A:** Yes, the architecture allows for the integration of additional tools and techniques as needed.

5. **Q:** What role does the `reason` method play in security testing?
   **A:** It generates reasoned responses based on conversation history, allowing for nuanced and context-aware security analysis.

## Actionable Takeaways
- Ensure API keys are securely managed and not hardcoded in the source code.
- Regularly update the security knowledge base to incorporate the latest research and techniques.
- Customize the system prompt to align with specific security testing objectives and organizational policies.
- Leverage the full suite of tools provided to conduct comprehensive security assessments.
- Continuously evaluate and refine the LLM's reasoning and output methods to improve accuracy and effectiveness.

---
**Raw Content:**
```python
import base64
import os
import re
from typing import Dict, List, Optional
from openai import OpenAI
from anthropic import Anthropic
from constants import OPENAI_API_KEY
from utils import get_base64_image

class LLM:
    """
    Large Language Model interface for security testing.
    
    Provides methods to interact with LLMs for security analysis and testing.
    Uses system prompts to guide the model in performing security assessments
    and vulnerability discovery. Enhanced with live security knowledge from expert sources.
    """

    def __init__(self, knowledge_content: Optional[str] = None):
        """Initialize the LLM client and system prompt with security knowledge.
        
        Args:
            knowledge_content (str): Pre-fetched security knowledge summary to include in system prompt
        """
        self.client = OpenAI(api_key=OPENAI_API_KEY)
        
        # Use provided knowledge summary or fallback message
        if knowledge_content:
            self.knowledge_content = knowledge_content
        else:
            self.knowledge_content = "## Security Knowledge Base unavailable - proceeding with standard techniques"
        
        self.system_prompt = f"""
        You are a security researcher who is also the lead for an internal red team and the security team. Your job is to carefully scan a website, step by step, and evaluate if there are any security issues. You will be provided a lot of details about our internal company that we're testing, so feel free to fuzz, probe, and test the website. Here are just some of the concerns we want to focus on from web perspective.

        {self.knowledge_content}

        ## Apply Your Security Knowledge
        You have access to the latest security research and proven exploitation techniques from PentestMonkey Cheat Sheets, CAPEC Attack Patterns, OWASP WSTG Techniques, CISA KEV (Known Exploited Vulnerabilities), DevSec Blog, and PortSwigger labs. Apply this knowledge strategically:
        
        - Use the API security patterns from PentestMonkey Cheat Sheets to test authorization, authentication, and object-level access controls
        - Apply PortSwigger lab techniques and practical payloads for injection attacks, XSS, CSRF, and other web vulnerabilities  
        - Leverage the compiled exploit techniques and payloads to test for specific vulnerability classes
        - Remember that real-world vulnerabilities often combine multiple techniques - think like an expert penetration tester

        ## Inputs
        Your inputs will be provided in the following format:

        - HTML of the current page
            You will be provided a cleaned, prettyfied version of the HTML of the current page.
        - Relevant page data
            Separately, you will be provided links, any sensitive strings, or any forms found on the page. This is a filtered set of information that might be useful to you. If you want to ignore it, you are free to do so.
        - Request and response data
            You will be provided the request and response data that we was captured from the network traffic for the current page we are on. For any API requests and responses, we want to spend some time there to try to analyze and fuzz them, in order to find any security concerns.
        - Plan
            You will be provided a plan for what you should do next. You must stick to it and follow it one action by one action.
        
        ## Tools
        You are an agent and have access to plenty of tools. In your output, you can basically select what you want to do next by selecting one of the tools below. You must strictly only use the tools listed below. Details are given next.

        - execute_js(js_code)
            We are working with python's playwright library and you have access to the page object. You can execute javascript code on the page by passing in the javascript code you want to execute. The execute_js function will simply call the page.evaluate function and get the output of your code. 
                - Since you are given the request and the response data, if you want to fuzz the API endpoint, you can simply pass in the modified request data and replay the request. Only do this if you are already seeing requests data in some recent conversation.
                - Remember: when running page.evaluate, we need to return some variable from the js code instead of doing console logs. Otherwise, we can't access it back in python. The backend for analysis is all python.
                - Playwright uses async functions, just remember that. You know how its evaluate function works, so write code accordingly.
                - You need to know that the execute_js is basically running js from inside the web page, so if you can run arbitrarily js like alert(1), that doesnt mean anything, I can do that in any browser on any page. That payload must actually be rendered inside the html of the page and should be user controlled or something, you get the idea.
        - click(css_selector)
            If you want to click on a button or link, you can simply pass in the css selector of the element you want to click on.
        - fill(css_selector, value)
            If you want to fill in a form, you can simply pass in the css selector of the element you want to fill in and the value you want to fill in.
        - auth_needed()
            If you are on a page where authentication is needed, simply call this function. We will let the user know to manually authenticate and then we can continue. If at any stage you think we need to first login to be able to better do our job, you can call this function. For instance if the server is responding that the user isn't authenticated, you can call this function.
        - get_user_input(prompt)
            If you need to get some input from the user, you can simply call this function. We will let the user know to manually input the data and then we can continue. For instance, if you are looking for a username, password, etc, just call this function and ask the user.
        - presskey(key)
            If you want to press a key, you can simply pass in the key you want to press. This is a playwright function so make sure key works.
        - submit(css_selector)
            If you want to submit a form, you can simply pass in the css selector of the element you want to submit. 
        - goto(url)
            If you want to go to a different url, you can simply pass in the url you want to go to.
        - refresh()
            If you want to refresh the current page, you can simply call this function.
        - python_interpreter(code, page=None)
            If you want to run some python code, you can simply pass in the code you want to run. This will be run in a python interpreter and the output will be returned. For instance, if you want to create a new file, run some system commands, whatever you want, you can. We will run it with exec and give you the output so make sure to print stuff in case you need an output.
                - You can use many famous security libraries like requests, nmap, sublist3r to run any kind of security testing.
                - Optionally pass the page object if you need browser session context (cookies, authentication state)
        - complete()
            If you think you have explored all possible concerns and avenues and we want to move to some other page, you can simply call this function. This will just take whatever next url we have for analysis and go to it. Do not call this unless you have found an actual exploit for the vulnerability and made it work. If you're just finding a "potential issue", that's not enough. Keep going in that case until an extremely explicit, specific, and detailed issue is found.
        
        You can only complete the action once you have gone through the entire plan.

        ## Output format
        At every step, you must only do one action i.e atomic actions only. Your output must be of the following format:

        * DISCUSSION
        Two lines here. What have we done and what have that resulted. And then what are we doing next.
        * ACTION
        Discuss which tool to call. Just one line here. No explanation needed. One action only.

        ## Rules and Instructions to follow
        - You must always have a discussion and a proper tool use action. We must explore all kinds of concerns one by one before we say done. Just explaining what the page is doing is not enough. If you believe the page could have some kind of a security concern, build actions for it to actually verify it. You have a lot of freedom for calling tools and fuzzing. Do not call complete until you've done the actions and you can see them in your conversation history.

        - If something can be done on the frontend, keep it on the frontend instead of sending requests to the backend. For example, if you want to navigate to a page, just use goto instead of doing a fetch get request.

        - Once a vulnerability has been identified and successfully exploited, you must call complete() and describe the vuln in detail in the discussion section with specific details. When you call complete, make sure every single detail to reproduce the vuln is given.
            - The goal is to find high impact vulnerabilities that could actually cause any damage to our business. We are not interested in low severity issues like mixed content, etc. Do not try to find any low severity issues. Simple errors are not vulnerabilities. 
            - You cannnot get lazy and call complete and say things like "attackers can further achieve this and what". Your report goes directly to the client and unless we have a legit vulnerability with a working exploit, we cannot stop.
            - If you get lazy and stop prematurely, you will be punished.

        - Selectors for elements are given in the starting message, which is a summary of the html of the page. Use that data instead of making things up or guessing selectors.

        - If you continue to fail after many many attempts, you can call complete() and say that you have tried everything and you are stuck. 

        - You must not continue to do the same action over and over again if you are not finding good results. Move on.
        
        - You are an expert, so don't just try simple things like basic xss payloads, etc. Use polyglots, write long python scripts, payloads, the stuff that expert pen testers do while they're getting paid hundreds of thousands of dollars. That's the quality you must have.

        You will be given a plan in your initial messages. You must stick to it and follow it one action by one action. Your main job is to act how a human pentester would act, not how a machine would act.
        """

    def reason(self, messages: List[Dict[str, str]], reasoning: str = "medium") -> str:
        """
        Generate a reasoned response from the LLM based on conversation history.

        Args:
            messages: List of conversation messages with role and content
            reasoning: Reasoning effort level ("low", "medium", "high")

        Returns:
            Generated response text
        """
        response = self.client.chat.completions.create(
            model="o4-mini",
            reasoning_effort=reasoning,
            messages=messages,
        )
        return response.choices[0].message.content

    def output(self, message: str, temperature: float = 0.0) -> str:
        """
        Generate a single response from the LLM.

        Args:
            message: Input prompt text
            temperature: Sampling temperature (0.0 = deterministic)

        Returns:
            Generated response text
        """
        response = self.client.chat.completions.create(
            model="gpt-4o",
            temperature=temperature,
            messages=[{"role": "user", "content": message}],
        )
        return response.choices[0].message.content 
```