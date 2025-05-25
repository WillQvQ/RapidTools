# RapidTools

**RapidTools** is introduced in the article [_"Investigating Effective LLM-based In-Context Tool Use: What Matters and How to Improve"_](https://journal.hep.com.cn/fcs/EN/10.1007/s11704-025-41365-6), accepted by *Frontiers of Computer Science (FCS)*.

You can download the dataset from [🤗 Hugging Face](https://huggingface.co/datasets/WillQvQ/RapidTools/tree/main).  
> RapidTools uses keyword-based filtering to retain appropriate data. Due to minor changes in the filtering rules, version 1.0.1 includes slightly more data than what was reported in the paper.

## Dataset Structure

### `data_with_response.jsonl` (203,472 instances)

> 199,548 instances that passed the check with *check_response_code=0*

- `_id`: Unique identifier  
- `query`: The user query  
- `candidate_tools`: The names of 4 candidate tools  
- `tool_name`: The tool selected for invocation  
- `tool_parameters`: Parameters passed to the tool  
- `tool_response`: The response from the tool call  
- `check_response_code`: Indicator of the response status (0, 1, or 2)  
- `check_response_type`: Specific type of error detected (if applicable)

### `tools_description.json` (4,793 tool documents)

- `name`: Tool name in the format "category.package.tool"  
- `parameters`: Specification of tool parameters  
- `package_description`: Description of the package to which the tool belongs  
- `tool_description`: Description of the tool itself  

## Response Filtering Logic

The `check_response_code` field provides a coarse classification of response quality based on the presence of error-related keywords.

- **0**: `tool_response` contains no error keywords.  
- **1**: `tool_response` contains keywords from `SYSTEM_ERRORS` or `PARAMETER_ERRORS`, but the response is relatively long and may have been misclassified.  
- **2**: `tool_response` contains keywords from `PARAMETER_ERRORS` only.  
- **(Note)**: Responses containing keywords from `SYSTEM_ERRORS` are excluded entirely.

### Keywords

```python
SYSTEM_ERRORS = [
    # Network and connection issues
    "The API is unreachable", 
    "timed out",
    "Server Error",
    "bad request",
    "Too many requests",
    # Permission and authentication issues
    "This endpoint is disabled for your subscription",
    "Unauthorized",
    "Expired API key",
    "not subscribed",
    "You have exceeded",
    "run out of",
    "upgrading your API",
    "You have not specified",
    # Internal system errors
    "maximum recursion depth exceeded",
    "Something went wrong",
    "FAIL",
    "<title>Error</title>",
    # API configuration issues
    "API doesn't exists",
    "'API doesn't exists'",
    "no longer supported",
    "Unknown Webhook",
]

PARAMETER_ERRORS = [
    # Parameter issues: problems related to invocation parameters such as value, format, and logic
    "not found",
    "Not found", 
    "not available",
    "not exist",
    "No object found",
    "No word matches the criteria",
    "no results containing",
    "Unable to find",
    "No valid",
    "not valid",
    "not a valid",
    "Invalid url",
    "Invalid days",
    "Unknown sport",
    "Parameter 'type' is not in",
    "InvalidSearchValueException",
    "InvalidJsonSearchException",
    "parameterErrors",
    "'success':False",
    '"successfull": false',
]
```

## Usage Recommendations

1. For non-expert users, it is recommended to use only data with `check_response_code = 0`.
2. The original four candidate tools are for reference only. When training models, additional candidate tools can be introduced via retrieval-based methods.
3. For in-context tool use, the default method for constructing tool documentation is: let `description = package_description + '\n' + tool_description` , then put **name**, **parameters**, and **description** into a dictionary.
4. Tool responses are generally not required for training. If training with tool responses is desired, users must design their own compression strategy for the response field.

## Changelog

### [v1.0.1]
- Refined keyword filtering logic for improved data quality.
- Includes slightly more data than the version reported in the paper due to minor adjustments in the filtering rules.
- *Note: This is the currently available version.*

### [v1.0.0]
- Initial release, aligned with the dataset used in the FCS publication.
- *The original data export script for v1.0.0 was unfortunately lost, and this version cannot be exactly reproduced.*
- *This version is no longer maintained or publicly available.*

> This dataset is released under a non-commercial academic license. For commercial use, please contact the author.