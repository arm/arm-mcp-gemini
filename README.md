# Arm MCP Gemini CLI Extension

This repository contains a Gemini CLI extension that adds:

* the Arm MCP Server Docker configuration you requested
* a reusable `/arm-migration` custom command for x86-to-Arm migration work
* an install-time prompt for the project root directory to mount into the container

## Files

* `gemini-extension.json`: Gemini CLI extension manifest with the bundled MCP server configuration
* `commands/arm-migration.toml`: custom slash command prompt

## MCP server config

During install, Gemini CLI should prompt for `Project Root Directory`. This should be the absolute path to the root of the user's project source tree, not a parent workspace folder unless the project itself lives there.

```json
{
  "name": "arm-mcp-gemini",
  "version": "1.0.0",
  "description": "Gemini CLI extension that adds the Arm MCP Server and an /arm-migration workflow command.",
  "settings": [
    {
      "name": "Project Directory",
      "description": "Absolute host path to the root directory of the project code you want mounted into the Arm MCP Server for running code scan.",
      "envVar": "ARM_MCP_PROJECT_ROOT",
      "sensitive": false
    }
  ],
  "mcpServers": {
    "arm_mcp_server": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "--pull=always",
        "-i",
        "-v",
        "${ARM_MCP_PROJECT_ROOT}:/workspace",
        "armlimited/arm-mcp:latest"
      ],
      "env": {},
      "timeout": 60000
    }
  }
}
```

## Command

Use `/arm-migration` inside Gemini CLI after the extension is installed and the MCP config is active.
