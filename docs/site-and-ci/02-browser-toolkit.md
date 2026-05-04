# CI2. Browser-Based Review Toolkit

A full 13-lens review requires source files and a text editor. But for a quick triage or a checkpoint review, a browser-based toolkit can accelerate the process without requiring a local environment setup.

This chapter documents the minimum toolkit you can use in a browser for lightweight review tasks.

## GitHub code search for finding patterns

GitHub's search supports code search across repositories. Use these searches to find common violation patterns in your codebase before opening the editor.

**SOQL in loops:**
```text
path:*.cls "for (" AND "SELECT"
```

**DML in loops:**
```text
path:*.cls "for (" AND "insert" AND "triggerNew"
```

**Missing sharing:**
```text
path:*.cls "public class" AND NOT "with sharing" AND NOT "without sharing"
```

**Cacheable mutation:**
```text
path:*.cls "@AuraEnabled(cacheable=true)" AND "update" AND "insert"
```

**Hardcoded credentials:**
```text
path:*.cls "sk_live" OR "api_key" OR "password"
```

## VS Code Online

github.dev and vscode.dev open a VS Code instance in the browser with no local installation required. Open any repository and use the search panel (Ctrl+Shift+F) to find patterns across all files.

For Apex files:
- Use `*.cls` to target Apex classes
- Use `*.trigger` to target triggers
- Use `*.js` to target LWC JavaScript files

## Salesforce CLI browser extension

The Salesforce Extension Pack for VS Code has a browser-accessible version through the Salesforce web UI. However, for full Apex analysis, a local VS Code instance or the Salesforce CLI is required. The browser-based tools are for discovery, not deep analysis.

## The lightweight review checklist

Use this checklist for a 10-minute browser-based triage:

1. Open the repository on GitHub
2. Search for `@AuraEnabled` methods that are not `cacheable=false` (mutation flags)
3. Search for `for (.* : triggerNew)` patterns (bulkification flags)
4. Search for `insert` or `update` inside for loops (DML flags)
5. Search for `without sharing` (security flags)
6. Search for `HttpRequest` outside Queueable/Batch context (callout pattern check)
7. Search for `@IsTest(seeAllData=true)` (test isolation flags)
8. Open the largest class (most lines) and check for mixed responsibilities

This is not a full review. It is a first pass that surfaces the most obvious violations. Use it to decide whether to request a full 13-lens review.

## What this chapter covered

- GitHub code search patterns for finding common violations
- Using github.dev for browser-based code review
- The 8-step lightweight review checklist
- Limitations of browser-based review versus full local analysis

## References

- [GitHub Code Search](https://docs.github.com/en/search-github/searching-on-github/searching-code)
- [VS Code for the Web](https://code.visualstudio.com/docs/editor/vscode-web)
- [Salesforce Developer Console](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_debugging_developer_console.htm)