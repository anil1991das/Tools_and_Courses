---
title: "How to Validate JSON with JSON Schema"
description: "Learn how JSON Schema validation works, define required fields and data types, and catch invalid API data with practical examples."
slug: "how-to-validate-json-schema"
publishedAt: "2026-10-28"
updatedAt: "2026-10-28"
author: "iamnoobs"
category: "Developer Tutorials"
tags:
  - "JSON"
  - "JSON Schema"
  - "validation"
  - "API testing"

relatedTools:
  - title: "JSON Schema Validator"
    url: "/json-schema-validator"
relatedArticles:
  - "how-to-validate-json"
  - "how-to-format-json"
draft: false
---

# How to Validate JSON with JSON Schema

JSON Schema validation checks whether JSON data follows a predefined structure and set of rules. Unlike basic JSON syntax validation, which checks whether the data is valid JSON, schema validation can verify required fields, data types, string lengths, numeric limits, and allowed values.

For example, an API may require every user record to contain a name and an integer age greater than or equal to zero. JSON Schema lets you define these requirements and check whether a JSON object satisfies them.

In this guide, you'll learn how JSON Schema works, how to create a schema, and how to identify common validation errors. You can also try the [IamNoobs JSON Schema Validator](/json-schema-validator) with the examples below.

## What Is JSON Schema Validation?

JSON Schema is a way to describe the expected structure and constraints of JSON data. A schema defines what properties an object should contain, what types those properties should have, and which values are acceptable.

Consider this JSON data:

```json
{
  "name": "John",
  "age": 30
}
```

The data is syntactically valid JSON. However, syntax validation alone cannot determine whether `name` must be a string, whether `age` must be an integer, or whether both properties are required.

A JSON Schema can define those rules:

```json
{
  "type": "object",
  "required": ["name", "age"],
  "properties": {
    "name": {
      "type": "string"
    },
    "age": {
      "type": "integer",
      "minimum": 0
    }
  }
}
```

This schema specifies that:

- The root value must be an object.
- The object must contain `name` and `age`.
- `name` must be a string.
- `age` must be an integer greater than or equal to zero.

The example data satisfies these rules.

### JSON validation vs. JSON Schema validation

| Feature | JSON syntax validation | JSON Schema validation |
|---|---|---|
| Checks JSON syntax | Yes | A schema validator must parse its JSON inputs |
| Checks property data types | No | Yes, when defined in the schema |
| Checks required fields | No | Yes, using `required` |
| Checks numeric ranges | No | Yes, using constraints such as `minimum` |
| Checks allowed values | No | Yes, using constraints such as `enum` |
| Defines application-specific data structure | No | Yes, within the supported schema features |

For example, this is valid JSON:

```json
{
  "name": "John",
  "age": "thirty"
}
```

But it fails the schema above because `age` is a string instead of an integer.

**The key distinction:** JSON syntax validation checks whether the document can be parsed. JSON Schema validation checks whether the parsed data meets defined requirements.

## How to Validate JSON with JSON Schema

You can validate JSON against a schema using an online tool or a JSON Schema library in your application.

### Step 1: Define the expected data

Suppose you are building an API that accepts user information. Each record must contain a name, an age, and an account status.

Valid example data:

```json
{
  "name": "Priya",
  "age": 28,
  "active": true
}
```

Before creating the schema, decide which properties are required, what types they must use, and what additional restrictions apply.

### Step 2: Create the JSON Schema

Use this schema to describe the expected user record:

```json
{
  "type": "object",
  "required": ["name", "age", "active"],
  "properties": {
    "name": {
      "type": "string",
      "minLength": 1
    },
    "age": {
      "type": "integer",
      "minimum": 0,
      "maximum": 120
    },
    "active": {
      "type": "boolean"
    }
  }
}
```

This schema defines three required properties.

- `name` must be a non-empty string.
- `age` must be an integer between 0 and 120, inclusive.
- `active` must be either `true` or `false`.

The `required` keyword checks whether the properties exist. The constraints under `properties` specify the rules for their values.

### Step 3: Validate the data

Open the [IamNoobs JSON Schema Validator](/json-schema-validator).

The tool provides separate fields for JSON data and JSON Schema. Enter the user object in the JSON Data field and the schema in the JSON Schema field, then select **Validate**.

If the data meets the supported rules, validation should succeed. If a property has the wrong type or violates a constraint, the validator can report the mismatch.

### Step 4: Fix validation errors

Try changing the age to a string:

```json
{
  "name": "Priya",
  "age": "28",
  "active": true
}
```

The data is valid JSON, but it violates the schema because `"28"` is a string rather than an integer.

Correct the value:

```json
{
  "name": "Priya",
  "age": 28,
  "active": true
}
```

Validate the corrected object again.

**Tip:** Fix one reported issue at a time, especially when the data contains multiple nested objects or arrays.

## Example: Validate an API Response

Imagine an API returns product information that your application uses to display a product catalog.

You want each product to have an identifier, a name, a price, and an availability status.

### Example JSON data

```json
{
  "id": "P101",
  "name": "Wireless Mouse",
  "price": 499,
  "available": true
}
```

### Example JSON Schema

```json
{
  "type": "object",
  "required": ["id", "name", "price", "available"],
  "properties": {
    "id": {
      "type": "string",
      "minLength": 1
    },
    "name": {
      "type": "string",
      "minLength": 1
    },
    "price": {
      "type": "number",
      "minimum": 0
    },
    "available": {
      "type": "boolean"
    }
  }
}
```

This schema ensures that the product has all four properties, that the identifier and name are non-empty strings, that the price is a non-negative number, and that availability is a boolean.

### Test an invalid product

Now suppose the API returns this object:

```json
{
  "id": "P102",
  "name": "Keyboard",
  "price": -200,
  "available": "yes"
}
```

There are two validation problems:

1. `price` is negative, which violates the `minimum: 0` constraint.
2. `available` is a string, but the schema requires a boolean.

A corrected version might be:

```json
{
  "id": "P102",
  "name": "Keyboard",
  "price": 1200,
  "available": true
}
```

The example demonstrates how schema validation can catch data problems before an application relies on the values.

Note that the schema does not restrict additional properties by default. If your application must reject fields that are not explicitly defined, add an appropriate restriction supported by your chosen JSON Schema version and validator.

## Common JSON Schema Validation Errors

Understanding common validation failures helps you fix data without repeatedly changing the wrong part of a document.

### 1. Missing required properties

If the schema contains:

```json
{
  "type": "object",
  "required": ["name", "email"]
}
```

This object fails validation:

```json
{
  "name": "Amit"
}
```

The `email` property is missing.

**How to fix it:** Add the missing property with an appropriate value, or revise the schema if the property should be optional.

### 2. Incorrect data types

A schema may require an integer:

```json
{
  "type": "integer"
}
```

The following value does not satisfy that requirement:

```json
"age": "30"
```

Use a numeric value instead:

```json
"age": 30
```

Remember that JSON distinguishes strings, numbers, booleans, arrays, objects, and `null`. A value that looks like a number is not necessarily a number if it is enclosed in quotation marks.

### 3. Values outside an allowed range

A schema can define numeric boundaries:

```json
{
  "type": "number",
  "minimum": 1,
  "maximum": 100
}
```

A value of `150` violates the maximum constraint.

**How to fix it:** Check the application's allowed range and correct the input or adjust the schema if its limits are inappropriate.

### 4. Empty strings

A string property may exist but contain no characters.

For example:

```json
{
  "name": ""
}
```

A schema with `"minLength": 1` rejects this value.

**How to fix it:** Provide a non-empty value or remove the minimum-length restriction if empty strings are acceptable.

### 5. Invalid enum values

The `enum` keyword restricts a value to a defined list.

```json
{
  "type": "string",
  "enum": ["pending", "approved", "rejected"]
}
```

A value of `"completed"` fails validation because it is not one of the permitted strings.

**How to fix it:** Use an allowed value or update the permitted list if the application's requirements have changed.

### 6. Confusing missing properties with null values

Consider this schema:

```json
{
  "type": "object",
  "required": ["email"],
  "properties": {
    "email": {
      "type": "string"
    }
  }
}
```

This object fails validation because the property is missing:

```json
{}
```

This object also fails, but for a different reason:

```json
{
  "email": null
}
```

The second object contains the required property, but its value has the wrong type.

If `null` is an acceptable value, the schema must explicitly permit it using the syntax supported by the relevant JSON Schema version.

### 7. Incorrect nested structures

Suppose a schema requires an address object with a string-valued city:

```json
{
  "type": "object",
  "properties": {
    "address": {
      "type": "object",
      "required": ["city"],
      "properties": {
        "city": {
          "type": "string"
        }
      }
    }
  }
}
```

This data fails validation:

```json
{
  "address": {
    "city": 123
  }
}
```

The city value is numeric instead of a string.

**How to fix it:** Follow the schema's nested structure and check the expected type at each level.

### 8. Assuming every JSON Schema feature is supported

JSON Schema has multiple versions and many keywords. A lightweight validator may support common checks without implementing every feature in the specification.

**How to fix it:** Check the tool's documented support before relying on advanced schema features. For complex applications, use a maintained library that supports the required JSON Schema version and keywords.

## Try the IamNoobs JSON Schema Validator

The [IamNoobs JSON Schema Validator](/json-schema-validator) lets you validate JSON data against a schema using separate input fields for the data and its rules.

The tool supports common validation features, including:

- Object, array, string, number, integer, and boolean types.
- Required properties.
- Nested objects and arrays.
- Minimum and maximum values.
- Minimum and maximum string lengths.
- Enumerated allowed values.

It is designed for common validation tasks and is not a complete implementation of every JSON Schema specification feature.

### Quick test

Enter this JSON data:

```json
{
  "name": "John",
  "age": 30
}
```

Then enter this schema:

```json
{
  "type": "object",
  "required": ["name", "age"],
  "properties": {
    "name": {
      "type": "string"
    },
    "age": {
      "type": "integer",
      "minimum": 0
    }
  }
}
```

Select **Validate** to check the data against the schema.

Next, change `"age": 30` to `"age": "thirty"` and validate again. The data remains valid JSON, but it no longer satisfies the schema.

This simple test demonstrates the difference between parsing JSON and checking it against application-specific rules.

For a broader check of JSON syntax before schema validation, see the [IamNoobs JSON Validator](/json-validator). To make valid JSON easier to read, use the [JSON Formatter](/json-formatter).

## Related Guides

Continue learning about JSON with these guides:

- [How to Validate JSON and Fix Common Errors](/articles/how-to-validate-json) — understand syntax errors such as trailing commas, missing quotes, and mismatched braces.
- [How to Format JSON: A Practical Guide](/articles/how-to-format-json) — make JSON easier to read with indentation and consistent spacing.
- [How to Convert JSON to CSV](/articles/how-to-convert-json-to-csv) — transform JSON records into rows and columns for spreadsheets.

## Frequently Asked Questions

### What is JSON Schema used for?

JSON Schema describes the expected structure and constraints of JSON data. It can define required properties, data types, numeric ranges, string lengths, allowed values, and nested structures.

### Is JSON Schema validation the same as JSON validation?

No. Basic JSON validation checks whether a document follows JSON syntax. JSON Schema validation checks whether the data also meets a predefined set of structural and value constraints.

### Can JSON Schema validate required fields?

Yes. The `required` keyword specifies which property names must exist in an object. The schema can separately define the expected types and constraints for those properties.

### Can I validate an API response with JSON Schema?

Yes. You can validate an API response against a schema that describes the expected response structure. This is useful for testing API contracts and detecting unexpected data types or missing properties.

### Does JSON Schema validation modify my JSON?

Validation checks data against rules; it does not inherently modify the input. If data needs to be converted or normalized, perform that operation separately and validate the resulting data again.

### Can JSON Schema check numeric ranges?

Yes. Keywords such as `minimum` and `maximum` can restrict numeric values. Other constraints can be used for strings, arrays, and objects, depending on the schema version and validator support.

### Does the IamNoobs JSON Schema Validator support the complete specification?

No. The IamNoobs tool is a lightweight validator that supports common features such as types, required properties, nested structures, minimum and maximum values, string lengths, and enumerated values. For full JSON Schema Draft 7 or later compliance, use a dedicated library such as Ajv and verify support for the specific version you require.

### Is JSON Schema validation enough to secure an API?

No. Schema validation helps enforce data structure and input constraints, but it does not replace authentication, authorization, rate limiting, safe database queries, output encoding, or other security controls. APIs need a broader security strategy.

## Conclusion

JSON Schema validation helps ensure that JSON data follows the structure and rules expected by an application. By defining required fields, data types, and constraints, you can identify invalid API responses and incorrect input before they cause problems downstream.

Start with a small schema, test both valid and invalid examples, and confirm that your validator supports the keywords your application needs.

Try the [IamNoobs JSON Schema Validator](/json-schema-validator) to validate a JSON object against a schema and understand how structural validation works.
