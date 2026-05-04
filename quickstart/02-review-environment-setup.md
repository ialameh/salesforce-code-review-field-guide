# Q2. Review Environment Setup

A consistent review environment reduces friction and lets you focus on the code, not the tooling. This chapter covers the minimum viable setup for running a full 13-lens review: what to install, how to organize your review workspace, and what files to have on hand before you open a single class.

## What to install

You need only a text editor, a terminal, and a Salesforce DX project.

**Recommended tools:**

VS Code with:
- Salesforce Extension Pack (Apex syntax highlighting, SOQL console, deployment)
- Prettier or any Apex formatter

Alternatively:
- Any syntax-aware text editor (Sublime Text, JetBrains, Vim with Apex syntax)
- A terminal with `grep`, `find`, and standard Unix tools

**Optional but useful:**

- `sf` CLI (`npm install -g sf-cli`) for scratch org management and SOQL queries
- `jq` for parsing JSON output from SOQL queries
- `tree` for generating directory structure views of the codebase

## Review workspace directory

Create a working directory for each review. Do not mix reviews.

```text
~/reviews/
  ├── <org-name>-<date>/
  │   ├── intake/        (copied source files)
  │   ├── inventory.md
  │   ├── system-map.md
  │   ├── findings/
  │   │   ├── architecture.md
  │   │   ├── governor-limits.md
  │   │   ├── security.md
  │   │   └── ...
  │   └── final-report.md
```

One directory per review keeps output isolated and lets you cross-reference findings across reviews.

## The inventory checklist

Before opening any file, run an inventory. This takes 2 minutes and gives you a map of everything you need to review.

For each category, count the files and note the most complex ones:

| Category | Count | Largest file | Notes |
|---|---|---|---|
| Apex classes | | | |
| Triggers | | | |
| Test classes | | | |
| Batch/Queueable | | | |
| REST/Aura endpoints | | | |
| LWCs | | | |
| Flows | | | |
| Custom Metadata | | | |
| Named Credentials | | | |
| Permission Sets | | | |

Mark anything that is larger than 300 lines or that has more than 5 methods. Large files are where problems hide.

## Source file handling

Copy the files you intend to review into the `intake/` subdirectory before starting. This gives you a clean working copy that will not change while you are reviewing.

If the source is a Salesforce DX project:

```bash
cp -r /path/to/force-app/main/default/classes intake/classes
cp -r /path/to/force-app/main/default/triggers intake/triggers
```

If the source is a static zip export:

```bash
unzip source.zip -d intake/
```

Do not work directly in the source directory. You want to be able to re-run inventory at any time without the file count changing under you.

## What to have open in your editor

Before opening any class file, open these two reference documents:

1. The Governor Limits cheat sheet (official Salesforce PDF or the Apex developer guide table)
2. The 13-lens checklist summary (from the inside cover of this guide)

This keeps your checks consistent and prevents you from forgetting a lens mid-review.

## Verifying your setup

Run this check to confirm your environment is ready:

1. Open a terminal in your review workspace
2. Run `find intake/ -name "*.cls" | wc -l` to confirm files are accessible
3. Open any `.cls` file in your editor to confirm syntax highlighting works
4. Confirm you have write access to the findings directory

If any of these fail, fix the environment before starting the review. A bad environment produces bad reviews.

## What to do when setup is done

Once your environment is ready, proceed directly to Chapter O1 (Intake and Inventory) to begin the formal review pipeline.

## What this chapter covered

- Minimum tool stack for a full 13-lens review
- Directory structure for organized review workspaces
- Pre-review inventory checklist
- Source file handling conventions
- Setup verification steps

## References

- [Salesforce DX Setup Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_get_started.htm)
- [VS Code Salesforce Extension Pack](https://developer.salesforce.com/tools/vscode)