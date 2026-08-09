# Spec2Test — Skills (open agent-skills)

Open, agent-agnostic skills for the Spec2Test test-authoring pipeline. **Generated** from the
private engine repo — do not edit by hand.

## Install

```sh
npx skills add nmahindra-crestron/spec2test-skills
```

Pin a version to match your engine (recommended):

```sh
npx skills add nmahindra-crestron/spec2test-skills#v0.2.0
```

These skills drive the Spec2Test **engine** (a locally-run MCP server) via its tools. Install the
engine first; in VS Code also add a workspace `.vscode/mcp.json` pinning `cwd` to
`${workspaceFolder}` so the engine tools resolve your repo. Each skill's first step checks engine
compatibility via `spec2test_info`.

## Skills

- `s2t-new` — stage 1 (intake)
- `s2t-analyze` — stage 2 (analysis)
- `s2t-generate` — stage 3 (test cases)
- `s2t-export` — stage 4 (xlsx export)
