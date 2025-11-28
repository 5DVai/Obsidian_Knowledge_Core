# Utility Functions for Message Parsing and Tool Call Handling
**Source:** Decepticon\src\utils\message.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `message.py` module provides a set of utility functions designed to parse and handle messages and tool calls within a software system. These functions are crucial for transforming raw tool call data into user-friendly messages, extracting meaningful content from messages, and managing tool call status updates. The module is particularly valuable in systems where human, AI, and tool-generated messages need to be processed and displayed in a coherent and standardized manner.

The functions in this module are reusable and can be integrated into various enterprise applications that require message parsing and tool call management. They offer a structured approach to handling different message types and tool call scenarios, ensuring that the system can communicate effectively with users and other components.

## Key Concepts & Principles
- **Tool Name Parsing:** Converting tool names from snake_case to a more readable Title Case format.
- **Tool Call Parsing:** Transforming tool call data into concise, user-friendly command strings.
- **Message Type Identification:** Differentiating between human, AI, and tool messages.
- **Content Extraction:** Safely extracting and formatting message content.
- **Tool Call Status Messaging:** Generating status messages for ongoing tool calls.

## Detailed Technical Analysis

### Tool Name and Call Parsing
The functions `parse_tool_name` and `parse_tool_call` are designed to handle tool names and calls. They convert technical identifiers into human-readable formats, which is essential for user interfaces and logging systems. The `parse_tool_call` function, in particular, organizes tool call arguments into a structured command format, prioritizing certain parameters for clarity.

### Message Type and Content Handling
Functions like `get_message_type` and `extract_message_content` are responsible for identifying the type of message and extracting its content. These functions ensure that messages are processed correctly, regardless of their source, and that their content is safely extracted and formatted, with optional markup escaping for display purposes.

### Tool Call Status and Extraction
The `get_tool_call_status_message` function provides real-time status updates for tool calls, which can be used in UI elements like spinners. The `extract_tool_calls` function standardizes the extraction of tool call data from messages, facilitating further processing or logging.

## Enterprise Q&A Bank

1. **Q:** How does the module handle tool names with prefixes?
   **A:** The `parse_tool_name` function specifically checks for the "transfer_to_" prefix and formats the name accordingly.

2. **Q:** What happens if a tool call has no arguments?
   **A:** The `parse_tool_call` function will return the tool name followed by ellipses if no arguments are present.

3. **Q:** How are message types determined?
   **A:** The `get_message_type` function checks the instance type of the message against known classes like `HumanMessage`, `AIMessage`, and `ToolMessage`.

4. **Q:** Can the content extraction handle non-string message content?
   **A:** Yes, `extract_message_content` can process lists and dictionaries within messages, extracting text elements where possible.

5. **Q:** What is the purpose of escaping markup in message content?
   **A:** Escaping markup ensures that special characters are displayed correctly in environments that support rich text formatting.

## Actionable Takeaways

- Ensure tool names are consistently formatted for readability.
- Prioritize key parameters when generating tool call command strings.
- Implement message type checks to route messages appropriately.
- Use content extraction functions to safely handle diverse message formats.
- Provide real-time status updates for tool calls to enhance user experience.

---
**Raw Content:**
```python
from langchain_core.messages import HumanMessage, AIMessage, ToolMessage
from rich import markup
import json
from typing import Dict, Any, List, Optional

# 도구 이름 
def parse_tool_name(tool_name: str) -> str:
    """Parse tool name simply (no hardcoding)"""
    # 1. Handle transfer_to_ prefix
    if tool_name.startswith("transfer_to_"):
        target_agent = tool_name.replace("transfer_to_", "").replace("_", " ").title()
        return f"Transfer to {target_agent}"
    
    # 2. Convert snake_case to Title Case
    return tool_name.replace("_", " ").title()

# 도구 호출 처리
def parse_tool_call(tool_call_data: dict) -> str:
    """Parse tool call data and generate user-friendly message - 깔끔한 형태로 개선"""
    try:
        tool_name = tool_call_data.get("name", "Unknown Tool")
        tool_args = tool_call_data.get("args", {})
        
        # transfer_to_ 도구인 경우 특별 처리
        if tool_name.startswith("transfer_to_"):
            target_agent = tool_name.replace("transfer_to_", "").replace("_", " ").title()
            return f"Transfer to {target_agent}..."
        
        # 일반 도구 호출인 경우 - 간결한 명령어 형태로
        if tool_args:
            # 주요 매개변수들을 순서대로 정렬해서 명령어 형태로 만들기
            command_parts = [tool_name]
            
            # 일반적인 매개변수 순서: options, target, command, path, etc.
            param_order = ['options', 'target']
            
            # 순서대로 매개변수 추가
            for param in param_order:
                if param in tool_args and tool_args[param]:
                    # 리스트나 배열 형태인 경우 공백으로 연결
                    if isinstance(tool_args[param], list):
                        # 리스트의 각 요소를 공백으로 연결 (예: ['-F', '-sS'] → '-F -sS')
                        value = " ".join(str(opt) for opt in tool_args[param] if opt)
                    else:
                        value = str(tool_args[param]).strip()
                    
                    if value:
                        command_parts.append(value)
            
            # 순서에 없는 나머지 매개변수들도 추가
            for key, value in tool_args.items():
                if key not in param_order and value:
                    # 리스트나 배열 형태인 경우 공백으로 연결
                    if isinstance(value, list):
                        formatted_value = " ".join(str(opt) for opt in value if opt)
                    else:
                        formatted_value = str(value).strip()
                    
                    if formatted_value:
                        command_parts.append(formatted_value)
            
            if len(command_parts) > 1:
                return " ".join(command_parts)
            else:
                return f"{tool_name}..."
        else:
            return f"{tool_name}..."
            
    except Exception as e:
        return f"Tool call... (parsing error: {str(e)})"

# 도구 호출 상태 메시지 (spinner용)
def get_tool_call_status_message(tool_call_data: dict) -> str:
    """Tool call을 위한 상태 메시지 생성 (spinner에 사용)"""
    try:
        tool_name = tool_call_data.get("name", "Unknown Tool")
        
        if tool_name.startswith("transfer_to_"):
            target_agent = tool_name.replace("transfer_to_", "").replace("_", " ").title()
            return f"Transferring to {target_agent}..."
        else:
            display_name = parse_tool_name(tool_name)
            return f"Executing {display_name}..."
    except:
        return "Processing..."

# 에이전트 이름 from namespace
def get_agent_name(namespace):
    """Namespace에서 에이전트 이름 추출"""
    if not namespace:
        return "Unknown"
    
    if len(namespace) > 0:
        namespace_str = namespace[0]
        if ':' in namespace_str:
            return namespace_str.split(':')[0]
    
    return "Unknown"

# 메시지 타입
def get_message_type(message):
    """메시지 타입 파싱"""
    if isinstance(message, HumanMessage):
        return "user"
    elif isinstance(message, AIMessage):
        return "ai"
    elif isinstance(message, ToolMessage):
        return "tool"
    else:
        return None

# 메시지 content 
def extract_message_content(message, escape_markup=True):
    """메시지에서 내용 추출 - 안전한 처리"""
    try:
        if hasattr(message, 'content'):
            content = message.content
        else:
            content = str(message)
        
        if isinstance(content, str):
            result = content.strip() if content else ""
        elif isinstance(content, list):
            text_parts = []
            for item in content:
                if isinstance(item, dict):
                    if item.get('type') == 'text' and 'text' in item:
                        text_parts.append(item['text'])
                    elif 'text' in item:
                        text_parts.append(item['text'])
                elif isinstance(item, str):
                    text_parts.append(item)
            result = "\n".join(text_parts) if text_parts else str(content)
        else:
            result = str(content)
        
        # Rich 마크업 이스케이프 (기본적으로 활성화)
        if escape_markup:
            result = markup.escape(result)
            
        return result
    except Exception as e:
        error_msg = f"Content extraction error: {str(e)}\n{str(message)}"
        # 에러 메시지도 이스케이프
        if escape_markup:
            error_msg = markup.escape(error_msg)
        return error_msg

# Tool calls 추출 함수
def extract_tool_calls(message, event_data: Optional[Dict[str, Any]] = None) -> List[Dict[str, Any]]:
    """
    AI 메시지에서 tool calls 정보를 추출합니다.
    다양한 소스에서 tool calls를 찾아 표준화된 형식으로 반환합니다.
    """
    tool_calls = []
    
    # 1. raw_message.tool_calls에서 추출
    if message and hasattr(message, 'tool_calls') and message.tool_calls:
        for tool_call in message.tool_calls:
            tool_calls.append({
                "id": tool_call.get('id', ''),
                "name": tool_call.get('name', 'Unknown Tool'),
                "args": tool_call.get('args', {})
            })
    
    
    return tool_calls
```