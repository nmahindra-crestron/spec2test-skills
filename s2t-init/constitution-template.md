# Project Constitution

## Project
Name: <project name>
Application: <application under test>
Platforms: <platforms, comma separated>
Version: 1
Updated: <YYYY-MM-DD>

## Story Setup Defaults
Setup Details: <numbered setup a tester does before every test, or None>
Pre-requisite: <baseline pre-requisite, or None>
Default Actions: <Setup Action IDs, comma separated, or None>

## Placeholder Defaults
| Placeholder | Default |
|---|---|
| application | <application under test> |
| workspace-type | <what a tester creates or opens, e.g. project> |
| items | <what a tester adds, e.g. components> |
| settings | <what a tester configures, e.g. project settings> |

## Setup Actions
| ID | Action | Expected Result | Placeholders |
|---|---|---|---|
| LAUNCH-APP | Launch <application> on <platform>. | <application> opens with no errors. | application, platform |
| CREATE-WORKSPACE | Create a new <workspace-type> and fill in all required details. | A new <workspace-type> is created with no errors. | workspace-type |
| OPEN-WORKSPACE | Open an existing <workspace-type> <name>. | <workspace-type> opens with all content loaded. | workspace-type, name |
| ADD-ITEMS | Add <items> to <location>. | <items> appear under <location> with no errors. | items, location |
| CONFIGURE | Configure <settings> using <method>. | <settings> are applied with no errors. | settings, method |
| NAVIGATE | Navigate to <area> and open <view>. | <view> is displayed in <area>. | area, view |
| PREPARE-DATA | Prepare <data> required for the test. | <data> is available and valid. | data |

## Change Log
| Version | Date | Change | Source |
|---|---|---|---|
| 1 | <YYYY-MM-DD> | Created by s2t-init | User reply |
