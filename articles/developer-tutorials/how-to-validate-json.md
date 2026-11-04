---
title: "How to Validate JSON and Fix Common Errors"
description: "Find JSON syntax errors, understand why validation fails, and learn a practical workflow for fixing invalid JSON."
slug: "how-to-validate-json"
publishedAt: "2026-10-27"
updatedAt: "2026-10-27"
author: "iamnoobs"
category: "Developer Tutorials"
tags:
  - "JSON"
  - "validation"
relatedTools:
  - title: "JSON Validator"
    url: "/json-validator"
relatedArticles:
  - "how-to-format-json"
draft: false
---

# How to Validate JSON and Fix Common Errors

JSON is widely used for API responses, configuration files, application data, and communication between software systems. However, even a small syntax mistake can prevent an application from reading a JSON document.

**JSON validation checks whether a document follows the JSON syntax rules.** A validator helps identify problems such as missing commas, incorrect quotation marks, trailing commas, and mismatched brackets.

In this guide, you'll learn what JSON validation checks, how to validate JSON step by step, and how to fix common syntax errors. You can also use the [IamNoobs JSON Validator](/json-validator) to check your JSON data.

## What Does JSON Validation Check?

JSON validation determines whether a JSON document follows the syntax defined by the JSON standard.

A JSON parser reads the input and checks whether its structure is valid. If the document contains an unexpected character, missing separator, or invalid value, the parser reports an error instead of successfully parsing the document.

### Example of valid JSON

```json
{
  "name": "Amit",
  "age": 30,
  "active": true,
  "skills": ["C", "Python"]
}
```

This document is valid because it uses double-quoted property names, valid JSON values, correctly separated properties, and matching braces and brackets.

### Example of invalid JSON

```json
{
  "name": "Amit",
  "age": 30,
}
```

The trailing comma after `30` makes this document invalid under standard JSON syntax.

Correct version:

```json
{
  "name": "Amit",
  "age": 30
}
```

A JSON validator can help identify the syntax problem so you can correct it.

### What does a JSON validator check?

A standard JSON syntax validator checks several aspects of a document:

- **Quotation marks:** Property names and string values use double quotes.
- **Separators:** Commas appear between properties and array elements where required.
- **Brackets and braces:** Objects and arrays are opened and closed correctly.
- **Valid values:** Values use supported JSON types.
- **Escape sequences:** Special characters inside strings are escaped correctly.
- **Document structure:** The input represents a complete JSON value rather than arbitrary text.

### JSON syntax validation vs. schema validation

These are two different checks.

| Check | What it verifies | Example |
|---|---|---|
| JSON syntax validation | Whether the document follows JSON syntax | No trailing commas or unquoted keys |
| JSON Schema validation | Whether the data matches a defined structure and set of constraints | A required `name` field exists and `age` is a number |
| Business-rule validation | Whether values satisfy application-specific rules | An order quantity must be greater than zero |

For example, this is valid JSON:

```json
{
  "name": "Amit",
  "age": "thirty"
}
```

A syntax validator accepts it. However, a JSON Schema requiring `age` to be a number would reject it.

**Important:** Valid JSON is not automatically correct for your application. Syntax validation is the first step; schema and business-rule checks may also be necessary.

## How to Validate JSON

You can validate JSON using an online tool, a code editor, Python, or JavaScript. Choose the method that best fits your workflow.

### Method 1: Use an online JSON validator

An online validator is useful when you need to check JSON quickly without installing software.

Follow these steps:

1. Open the [IamNoobs JSON Validator](/json-validator).
2. Copy your JSON from an API response, configuration file, or application log.
3. Paste the JSON into the input field.
4. Start validation using the available controls.
5. Review the result and any reported error message.
6. Correct the invalid syntax and validate the document again.

If the validator identifies a line or column number, use that information to locate the problem. The actual mistake may appear just before the reported position.

The exact interface and error details depend on the current implementation of the tool.

### Method 2: Validate JSON using Python

Python's built-in `json` module can parse JSON and report syntax errors without requiring additional packages.

Create a file named `validate_json.py`:

```python
import json

input_file = "input.json"

try:
    with open(input_file, "r", encoding="utf-8") as file:
        data = json.load(file)

    print("Valid JSON")
    print(f"Root data type: {type(data).__name__}")

except json.JSONDecodeError as error:
    print("Invalid JSON")
    print(f"Message: {error.msg}")
    print(f"Line: {error.lineno}")
    print(f"Column: {error.colno}")

except OSError as error:
    print(f"File error: {error}")
```

Run the script:

```bash
python validate_json.py
```

If `input.json` contains valid JSON, the program reports that validation succeeded and displays the root data type.

If the file contains invalid JSON, Python reports the parser's error message and its line and column.

For example, if the input contains a trailing comma, the error will help you identify the location where parsing failed.

This script checks JSON syntax. It does not automatically verify your application's expected fields, data types, or business rules.

### Method 3: Validate JSON using JavaScript

JavaScript provides the built-in `JSON.parse()` method for parsing JSON strings.

```javascript
const jsonText = `{
  "name": "Amit",
  "age": 30,
  "active": true
}`;

try {
  const data = JSON.parse(jsonText);

  console.log("Valid JSON");
  console.log(data);
} catch (error) {
  console.error("Invalid JSON:", error.message);
}
```

If the JSON is valid, `JSON.parse()` returns the corresponding JavaScript value.

If the input contains a syntax error, it throws an exception that you can catch and handle.

You can also use this approach to validate a string received from an API or another input source before processing it further.

Remember that `JSON.parse()` checks syntax, not whether the parsed data satisfies your application's requirements.

### Method 4: Validate JSON in Visual Studio Code

Visual Studio Code can help identify syntax errors when working with `.json` files.

1. Open the JSON file in Visual Studio Code.
2. Ensure the editor recognizes the file as JSON.
3. Look for diagnostic indicators, such as red underlines or error markers.
4. Open the Problems panel to inspect reported issues.
5. Correct the syntax and save the file.

Editor diagnostics depend on the file's language mode, configuration, and installed extensions.

For a quick manual check, you can also use the Command Palette to format the document. Formatting usually requires valid JSON, so parsing errors may prevent the operation from completing.

See the [How to Format JSON guide](/articles/how-to-format-json) for more formatting techniques.

## Common JSON Errors and How to Fix Them

Most JSON syntax errors result from a small number of common mistakes. The following examples show invalid input, the reason it fails, and the corrected version.

### 1. Trailing commas

A trailing comma appears after the last property in an object or the last element in an array.

Invalid JSON:

```json
{
  "name": "Amit",
  "age": 30,
}
```

Why it fails: Standard JSON does not allow a comma after the final property.

Correct JSON:

```json
{
  "name": "Amit",
  "age": 30
}
```

**How to fix it:** Remove the comma immediately before the closing brace or bracket.

### 2. Missing commas

Commas separate properties in objects and elements in arrays.

Invalid JSON:

```json
{
  "name": "Amit"
  "age": 30
}
```

Why it fails: The parser encounters the `age` property before finding the required comma.

Correct JSON:

```json
{
  "name": "Amit",
  "age": 30
}
```

**How to fix it:** Add commas between properties or array elements, but not after the final item.

### 3. Single quotes instead of double quotes

JSON requires double quotes around property names and string values.

Invalid JSON:

```json
{
  'name': 'Amit'
}
```

Correct JSON:

```json
{
  "name": "Amit"
}
```

This error commonly occurs when copying a JavaScript object literal into a JSON file.

**How to fix it:** Replace single-quoted strings with double-quoted strings and escape any embedded double quotes.

### 4. Unquoted property names

Unlike many JavaScript object literals, JSON does not allow unquoted property names.

Invalid JSON:

```json
{
  name: "Amit",
  age: 30
}
```

Correct JSON:

```json
{
  "name": "Amit",
  "age": 30
}
```

**How to fix it:** Enclose every property name in double quotes.

### 5. Mismatched braces and brackets

Objects use `{}` and arrays use `[]`. Every opening delimiter must have a matching closing delimiter in the correct order.

Invalid JSON:

```json
{
  "users": [
    {
      "name": "Amit"
    }
}
```

The `users` array is missing its closing bracket.

Correct JSON:

```json
{
  "users": [
    {
      "name": "Amit"
    }
  ]
}
```

**How to fix it:** Check the nesting of arrays and objects. In deeply nested documents, correct indentation can make missing delimiters easier to identify.

### 6. Invalid string escaping

Quotation marks and certain control characters must be escaped inside JSON strings.

Invalid JSON:

```json
{
  "message": "He said "Hello""
}
```

Correct JSON:

```json
{
  "message": "He said \"Hello\""
}
```

A backslash escapes the embedded double quotation marks.

JSON also supports escape sequences such as `\n` for a newline and `\t` for a tab. Unescaped control characters inside strings can make a document invalid.

**How to fix it:** Escape quotation marks and backslashes correctly. If you're generating JSON programmatically, use a JSON serializer rather than manually constructing strings.

### 7. Unsupported values such as `undefined` and `NaN`

JSON supports strings, numbers, objects, arrays, booleans, and `null`. It does not support JavaScript's `undefined`, `NaN`, or `Infinity` as JSON values.

Invalid JSON:

```json
{
  "name": undefined,
  "score": NaN
}
```

A valid alternative, when missing values are intended, is:

```json
{
  "name": null,
  "score": null
}
```

**How to fix it:** Replace unsupported values with appropriate JSON values or omit properties when that matches the intended data model. Do not substitute `null` automatically if it would change the meaning of the data.

### 8. Comments inside JSON

Standard JSON does not support comments.

Invalid JSON:

```json
{
  // User information
  "name": "Amit"
}
```

Correct JSON:

```json
{
  "name": "Amit"
}
```

Some configuration formats support comments, but those formats are not strict JSON.

**How to fix it:** Remove comments before validating standard JSON, or use a format that explicitly supports comments when required.

### 9. Extra text around the JSON

A parser expects JSON data, not explanatory text or Markdown formatting.

Invalid input:

```text
Here is the API response:
{"status":"success","count":3}
```

The JSON object is valid, but the complete input is not a valid JSON document because it includes a sentence before the object.

Correct input:

```json
{
  "status": "success",
  "count": 3
}
```

**How to fix it:** Remove surrounding explanations, Markdown code fences, and unrelated log output before validating.

### 10. Multiple JSON objects without an enclosing structure

A standard JSON document contains one top-level JSON value. Two adjacent objects do not form one valid JSON document.

Invalid input:

```json
{"id": 1}
{"id": 2}
```

If you intend to represent a collection, use an array:

```json
[
  {
    "id": 1
  },
  {
    "id": 2
  }
]
```

**How to fix it:** Wrap multiple records in an array or use an appropriate streaming format, such as JSON Lines, when each line is intended to contain a separate JSON value. JSON Lines is a different format from a single standard JSON document.

### 11. Valid syntax but incorrect data types

Consider:

```json
{
  "name": "Amit",
  "age": "thirty"
}
```

This is valid JSON because `"thirty"` is a string. However, an application expecting `age` to be a number may reject it.

Corrected data, if the intended age is 30:

```json
{
  "name": "Amit",
  "age": 30
}
```

**How to fix it:** Check the expected schema and data types. A syntax validator alone cannot determine whether a value is appropriate for your application.

## A Step-by-Step Workflow for Fixing JSON Errors

When a validator reports an error, follow a consistent process rather than changing multiple parts of the document at once.

### Step 1: Validate the original input

Paste the complete JSON into a validator or parse it using Python or JavaScript.

Keep a copy of the original input so you can compare it with your corrected version.

### Step 2: Read the error message

A parser may report an unexpected token, missing separator, or invalid character.

Use the reported line and column when available. The position often points to where parsing failed, not necessarily where the mistake began.

### Step 3: Inspect the surrounding lines

Look at the preceding property or array element. Common causes include:

- A missing comma on the previous line.
- An extra comma before a closing delimiter.
- An unescaped quotation mark.
- A missing closing bracket or brace.
- An invalid value copied from JavaScript code.

### Step 4: Fix one error at a time

Correct the suspected problem and validate again.

A single syntax mistake can cause additional error messages because the parser may lose track of the intended structure. Fixing the first error can resolve several reported problems.

### Step 5: Format the corrected JSON

Once the document parses successfully, use a formatter to make the structure easier to inspect.

You can use the [IamNoobs JSON Formatter](/json-formatter) for this step.

### Step 6: Check the data requirements

After syntax validation, verify the expected fields, data types, required properties, and application-specific constraints.

If the data must follow a formal structure, consider using JSON Schema validation.

### Step 7: Test the JSON in its intended application

A document may pass syntax and schema validation but still fail at runtime because of incorrect values, missing configuration, or application-specific assumptions.

Test the corrected data in the application or workflow that consumes it.

## Validate JSON with the IamNoobs Tool

The [IamNoobs JSON Validator](/json-validator) provides a convenient way to check JSON syntax without writing a script.

It can be useful when debugging API responses, checking configuration files, preparing test data, or verifying JSON copied from documentation.

### Example: Find and fix a syntax error

Start with this invalid JSON:

```json
{
  "status": "success",
  "data": {
    "userId": 101,
    "name": "Amit",
  }
}
```

The trailing comma after `"Amit"` makes the document invalid.

Correct version:

```json
{
  "status": "success",
  "data": {
    "userId": 101,
    "name": "Amit"
  }
}
```

### Steps to use the validator

1. Open the [IamNoobs JSON Validator](/json-validator).
2. Paste the JSON you want to check.
3. Run validation using the available controls.
4. Review the validation result or error message.
5. Correct the reported syntax problem.
6. Validate the document again to confirm that it parses successfully.

The exact controls and error reporting depend on the current implementation of the tool.

### What to check after validation

- **Syntax:** The document parses without errors.
- **Structure:** Arrays and objects have the intended nesting.
- **Data types:** Strings, numbers, booleans, and `null` are used correctly.
- **Required fields:** The document contains the properties expected by the application.
- **Business rules:** Values satisfy the application's requirements.
- **Formatting:** The JSON is readable and maintainable.

If the data contains credentials, personal information, or other confidential material, check the tool's data-handling practices before submitting it. Local validation may be preferable for sensitive information.

## Frequently Asked Questions

### 1. What is JSON validation?

JSON validation checks whether a document follows JSON syntax rules. It identifies problems such as missing commas, incorrect quotation marks, invalid values, and mismatched braces or brackets.

### 2. How do I validate JSON online for free?

Open an online JSON validator, paste your JSON, and run the validation operation. You can use the [IamNoobs JSON Validator](/json-validator) to check compatible JSON input in your browser.

### 3. Why is my JSON invalid even though it looks correct?

Small syntax mistakes can be difficult to notice, particularly in large documents. Common causes include trailing commas, missing separators, incorrect string escaping, and extra text around the JSON. Use a parser or validator to identify the problem.

### 4. What does an unexpected token error mean?

An unexpected token error means the parser encountered a character or value that was not valid at that position in the document. Inspect the reported location and the preceding characters to identify the likely cause.

### 5. Can JSON be valid but still contain errors?

Yes. JSON can be syntactically valid but contain incorrect values, missing required fields, or data types that do not match an application's requirements. Schema validation and business-rule checks help identify these problems.

### 6. What is the difference between a JSON validator and a JSON formatter?

A validator checks whether JSON syntax is valid. A formatter rearranges valid JSON with consistent indentation and spacing. Some tools combine both functions.

See the [How to Format JSON guide](/articles/how-to-format-json) for more information.

### 7. How do I validate JSON in Python?

Use the built-in `json.load()` function to parse a JSON file or `json.loads()` to parse a string. Catch `json.JSONDecodeError` to handle invalid input and inspect the reported error location.

### 8. Does JSON support comments?

No. Standard JSON does not support single-line or multiline comments. If a file contains comments, remove them before parsing it as standard JSON or use a configuration format that explicitly supports comments.

### 9. Can a JSON validator fix errors automatically?

Some tools provide suggestions or automatic corrections, but not every syntax error can be repaired safely without understanding the intended data. Review any automatic changes before using the corrected document.

### 10. Is online JSON validation safe?

That depends on the tool's implementation and privacy practices. For sensitive data, verify whether input is processed locally or sent to a server, and use an appropriate local validator when necessary.

## Conclusion

JSON validation helps ensure that your data follows the syntax required by JSON parsers. Most common errors, such as trailing commas, missing quotation marks, and mismatched delimiters, can be corrected by carefully inspecting the reported error location.

For a reliable workflow, validate the input, fix syntax errors one at a time, format the corrected document, and check the data against your application's requirements.

Use the [IamNoobs JSON Validator](/json-validator) to check your JSON, and the [JSON Formatter](/json-formatter) to make valid JSON easier to read and maintain.
