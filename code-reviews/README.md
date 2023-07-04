# Orcasound: Code Review Guide

<table>
  <tr>
   <td>Editor:
   </td>
   <td>Vikramsingh Rathod (rathodvikram44@gmail.com)
   </td>
  </tr>
  <tr>
   <td>Team:
   </td>
   <td>Orcasound
   </td>
  </tr>
  <tr>
   <td>Status:
   </td>
   <td>In Review
   </td>
  </tr>
  <tr>
   <td>Github
   </td>
   <td>https://github.com/orcasound
   </td>
  </tr>
</table>

## Introduction

Code reviews are a critical aspect of every development process, ensuring the quality, reliability, and maintainability of our software. This document serves as a comprehensive guide to conducting code reviews at Orcasound, specifically tailored for backend code development. However, many of the practices and principles outlined here can be applied to front-end code as well. By following the practices shared in this document, we can collaborate effectively, catch bugs early, and continuously improve our skills.

## Purpose & Benefit

1. **Ensure and Improve Code Quality:** Code reviews are an essential practice for ensuring the overall quality of the codebase. By reviewing code, we can identify potential issues, spot bugs, and improve the overall robustness of the software. It's an opportunity to catch mistakes or oversights that may have been missed by the developer during development.
2. **Bug Detection and Early Prevention:** Code reviews play a crucial role in catching bugs before they cause havoc in production. By thoroughly examining the code, we can uncover logic errors, address edge cases, and fix any incorrect implementations. This early detection allows us to address these issues promptly, saving us the headache of dealing with bugs down the line. Trust me, there's nothing worse than spending Friday evening deciphering cryptic error messages.
3. **More Eyes = More Inputs:** The beauty of code reviews is that we get to benefit from the collective expertise and knowledge of our team members. Each person brings their unique perspectives and experiences, which can lead to valuable feedback and alternative solutions. While not every suggestion may be applicable or relevant, having different sets of eyes on the code often uncovers potential improvements and helps us produce higher-quality code. For example, someone with performance optimization expertise might suggest more efficient algorithms, while another team member might focus on improving code readability through better variable naming.

## Type of Code reviews

1. **Dependencies or Vulnerability Upgrades**: When it comes to code reviews for upgrades, we're dealing with a straightforward yet crucial task. We need to carefully examine the upgraded versions of libraries and dependencies. Checking the release notes is essential to understand any deprecated functionality that might affect our code. It's also important to ensure that no unexpected changes, such as additional parameters, have been introduced that could impact the way our code initializes or operates. While these reviews may seem simple, it's always wise to perform a small round of testing to catch any potential issues. I've witnessed a recent upgrade of a dependency at my current organization causing a 30-minute downtime, all because it was assumed as a routine upgrade! So let's stay vigilant and avoid any surprises that could leave us scratching our heads.

2. **New Features**: These code reviews are particularly important and commonly encountered. Before diving into the code, it's essential to have a clear understanding of the feature being introduced, which should be outlined in the PR description. Taking a high-level look at the code helps us grasp the overall implementation approach. From there, we can gradually dig deeper into the codebase, examining the different modules and ensuring they align with our requirements. It’s good to ensure that such reviews are tested thoroughly.
3. **Bug fixes**: Bug fixes are all about finding and resolving those pesky issues that hinder our code's performance. When reviewing bug fixes, it's helpful to reproduce the bug to understand its root cause. By doing so, we can devise a high-level fix before diving into the code review. While developers often approach bug fixes with similar strategies, there's always the chance to learn from alternative approaches. However, before giving the final approval, it's crucial to conduct sufficient testing to ensure that the bug is truly resolved and won't resurface in unexpected ways.

## Checklist for Code Reviews

#### Error/Exception Handling

It's crucial to handle errors and exceptions properly to ensure the stability of the application. Always check if variables obtained from external sources or computed internally have valid values.

```
// Incorrect code without null check
function processUserInput(inputValue) {
  // Assume inputValue is obtained from an external source
  const result = inputValue.toUpperCase(); // Throws TypeError if inputValue is null or undefined
  return result;
}
```

In the code above, if inputValue is null or undefined, it will throw a TypeError and cause the application to crash. To prevent this, a null check should be added:

```
// Corrected code with null check
function processUserInput(inputValue) {
  if (inputValue !== null && inputValue !== undefined) {
    const result = inputValue.toUpperCase();
    return result;
  } else {
    return '';
  }
}
```

By adding a null check, we ensure that even if inputValue is null or undefined, the code gracefully handles the situation and returns an empty string instead of crashing. A similar approach should also be followed to ensure that an exception thrown should be caught and handled gracefully.

#### Functionality test

Testing the functionality of the code is of utmost importance. Ensure that thorough test cases, including unit tests and end-to-end tests, are written. If your codebase incorporates SonarQube, strive for code coverage above 80% to ensure adequate testing. By having comprehensive tests in place, we can catch any regressions or unexpected behavior that may arise due to changes in the code.

```
// Example of a unit test for a sorting function
function testSortArray() {
  const inputArray = [3, 1, 4, 2];
  const expectedOutput = [1, 2, 3, 4];
  const result = sortArray(inputArray);
  if (!arraysEqual(result, expectedOutput)) {
    throw new Error('Sorting failed!');
  }
}

// Example of an end-to-end test for an API endpoint
function testApiEndpoint() {
  const response = client.get('/api/data');
  if (response.status !== 200) {
    throw new Error('API request failed!');
  }
  const data = response.json();
  if (!data.hasOwnProperty('id')) {
    throw new Error('Missing "id" field in response!');
  }
}
```

With tests, such as the ones shown above, we can verify that the code behaves as expected and prevents regressions. Adequate test coverage ensures that critical paths and edge cases are covered, reducing the likelihood of undetected bugs.

#### Correctness and completeness

Code correctness pertains to the accuracy and precision of the code in achieving its intended functionality. During a code review, it is important to examine whether the code correctly implements the desired logic, adheres to the requirements, and produces the expected output.

For example, in a factorial calculation function, ensure that it accurately calculates the factorial for different input values.

Code completeness relates to whether the code is comprehensive and includes all the necessary components or functionality. It ensures that the code does not have any unnecessary or redundant parts, and all essential aspects are properly implemented.

For instance, in a review of an API endpoint implementation, you would assess if all the required parameters are properly handled, if error handling is in place, if necessary validations are performed, and if the response format aligns with the defined API specifications.

Ensure that the code exports only what is necessary and maintain consistent variable naming and types throughout the codebase.

#### Edge cases

When performing a code review, it's crucial to consider and look out for edge cases. Edge cases are specific scenarios or inputs that fall outside the typical range or behavior.

Let me explain this part with some real life examples -

**Database Retreival**: Consider situations where the database may retrieve multiple records instead of a single expected result. Review the code to verify if it handles such cases appropriately. For example, if a query is expected to return a single user profile, but it mistakenly retrieves multiple profiles, the code should handle this situation gracefully.

**Conditionals**: Review if and switch statements to ensure that all possible cases are accounted for and that there are no unnecessary default statements or conditions triggered unintentionally.

<span style="text-decoration:underline;">Scenario</span>: A switch statement is used to handle different types of user roles in an application. <span style="text-decoration:underline;">Edge Case</span>: A new user role is introduced, but the code doesn't include a corresponding case in the switch statement.

**Input Validation:** Validate and review how the code handles different types of input data, including edge cases such as empty values, invalid characters, or unexpected data formats. For instance, let's consider a scenario where a function expects a number as input, but we mistakenly pass a string instead. The function is designed to perform mathematical operations specifically on numbers. However, due to the absence of input validation, the function fails to handle this unexpected input properly.

```
function squareNumber(number) {
  return number * number;
}

const input = "5";
const result = squareNumber(input);
console.log(result); // Outputs: NaN
```

#### Code readability

One of the key aspects of code reviews is ensuring that the code is easy to read, understand, and maintain. This involves paying attention to various elements such as clear function descriptions, well-documented input parameters and return values, and proper handling of exceptions or errors.

```
/**
 * Divides two numbers.
 * @param {number} dividend - The dividend.
 * @param {number} divisor - The divisor.
 * @returns {number} The result of the division.
 * @throws {Error} Throws an error if the divisor is zero.
 */
function divideNumbers(dividend, divisor) {
  // Function implementation
}
```

Readable and Multi Line Code >>> Complex Single liner

```
//Single Line complex
const result = arr.reduce((sum, x) => sum + (x > 5 ? x * 2 : 0), 0);

// Multi line understandable
const filteredArray = arr.filter((x) => x > 5);
const mappedArray = filteredArray.map((x) => x * 2);
const result = mappedArray.reduce((sum, x) => sum + x, 0);
```

#### Performance

When reviewing code, it's important to think about performance and how we can make the code more efficient in terms of both time and memory usage. One area where we often find optimization opportunities is when sorting data. Instead of reinventing the wheel and writing our own sorting algorithm, it's usually better to utilize the built-in sorting functions that come with the programming language or libraries. These built-in functions are designed to be highly optimized and can greatly enhance the speed and efficiency of the sorting process.

#### Security and Vulnerabilities

Prioritizing security and vulnerability aspects in code reviews is crucial, alongside the focus on functionality. Instances of SQL injection, hacking, and data leakage highlight the importance of robust security measures. Failing to close data streams after use can result in resource leaks, performance degradation, denial of service attacks, and compromised sensitive information, impacting availability, stability, and security. Validating and sanitizing user input in SQL queries, using prepared statements or parameterized queries, is essential to prevent SQL injection attacks and ensure the integrity of the application.

```
const fs = require('fs');

function readDataFromFile(filePath) {
  const stream = fs.createReadStream(filePath);

  // Reading data from the stream...
  stream.on('data', (data) => {
    console.log(data);
  });

  // Forgot to close the stream.
}

readDataFromFile('data.txt');
```

#### Logging

During code reviews, it is optional but highly advantageous to prioritize the implementation of logging and metrics. Logging plays a vital role in effective debugging and monitoring of the application, offering valuable insights into its behavior. These insights prove invaluable, particularly when troubleshooting issues in live environments, despite the minor cost associated with logging.

## Some best practices

- Utilize the detail section to provide additional information and context during code reviews.
- Conduct a call or walkthrough for extensive code bases to ensure thorough understanding.
- Maintain consistent indentation throughout the codebase, adhering to established style guidelines, and use a .prettierrc file to enforce consistent code formatting rules. Inconsistent indentation can hinder code readability and maintainability.
- Maintain a constructive approach during code reviews, focusing on improvements rather than criticism.
- Don't hesitate to review code written by senior developers; everyone benefits from thorough review.
- Resolve any disagreements or concerns raised during the code review process.
- Manage time effectively by performing reviews promptly and allocating dedicated time slots. Avoid spending more than 60 minutes on a single code review to prevent fatigue and confusion.
- For large code changes, consider breaking them into smaller pull requests (PRs) for easier review. At a time a PR of 400 lines max is good to review.
- Follow up on addressed changes and ensure they are implemented correctly.
- Utilize GitHub labels or tags to categorize and organize code reviews effectively.
- Embrace the opportunity to learn from the code review process.

## How to improve

- **Retrospectives**: Conduct regular retrospectives to reflect on the team's code review process. Identify areas for improvement, discuss challenges faced during code reviews, and brainstorm ideas to enhance the efficiency and effectiveness of the process.
- **Code review sessions**: Organize dedicated code review sessions where team members can come together to review each other's code. These sessions foster collaboration, knowledge sharing, and collective learning
- **Tech sharing** - Encourage technical sharing sessions where team members can present interesting code snippets, share knowledge about new tools or technologies, and discuss coding techniques.
