# Arm MCP Gemini CLI Extension

This repository contains a Gemini CLI extension that adds:

* the Arm MCP Server Docker configuration
* a reusable `/arm-migration` custom command for x86-to-Arm migration work
* reusable custom commands for hotspot optimization, full optimization, and Arm-vs-x86 comparison workflows
* install-time prompts for the project root plus the SSH key material needed to reach the target instance for Arm Performix profiling

## Files

* `gemini-extension.json`: Gemini CLI extension manifest with the bundled MCP server configuration
* `commands/arm-migration.toml`: custom slash command prompt
* `commands/arm-hotspots-optimization.toml`: beginner-friendly hotspot optimization workflow
* `commands/arm-full-optimization.toml`: advanced multi-recipe optimization workflow
* `commands/arm-vs-x86-performance-comparison.toml`: x86-vs-Arm comparison workflow

## MCP server config

During install, Gemini CLI should prompt for:

* `Project Root Directory`: the absolute path to the root of the user's project source tree, not a parent workspace folder unless the project itself lives there
* `Arm Performix SSH Private Key`: the absolute path to the SSH private key file used to SSH into the target instance for Arm Performix profiling
* `Arm Performix SSH Known Hosts`: the absolute path to the `known_hosts` file used when SSHing into the target instance for Arm Performix profiling

```json
{
  "name": "arm-mcp-gemini",
  "version": "1.1.0",
  "description": "Gemini CLI extension that adds the Arm MCP Server plus /arm-migration, /arm-hotspots-optimization, /arm-full-optimization, and /arm-vs-x86-performance-comparison workflow commands.",
  "settings": [
    {
      "name": "Project Directory",
      "description": "Absolute host path to the root directory of the project code you want mounted into the Arm MCP Server for running code scan.",
      "envVar": "ARM_MCP_PROJECT_ROOT",
      "sensitive": false
    },
    {
      "name": "Arm Performix SSH Private Key",
      "description": "Absolute host path to the SSH private key file used to SSH into the target instance for Arm Performix profiling.",
      "envVar": "ARM_MCP_SSH_PRIVATE_KEY",
      "sensitive": true
    },
    {
      "name": "Arm Performix SSH Known Hosts",
      "description": "Absolute host path to the known_hosts file used when SSHing into the target instance for Arm Performix profiling.",
      "envVar": "ARM_MCP_SSH_KNOWN_HOSTS",
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
        "-v",
        "${ARM_MCP_SSH_PRIVATE_KEY}:/run/keys/ssh-key.pem:ro",
        "-v",
        "${ARM_MCP_SSH_KNOWN_HOSTS}:/run/keys/known_hosts:ro",
        "armlimited/arm-mcp:latest"
      ],
      "env": {},
      "timeout": 60000
    }
  }
}
```

## Commands

Gemini CLI loads extension commands automatically from the `commands/` directory, and the command name is derived from the filename.

Use these commands after the extension is installed and the MCP config is active:

* `/arm-migration`: migrate a codebase from x86 to Arm with the Arm MCP server
* `/arm-hotspots-optimization`: guide a beginner through hotspot-driven Arm tuning
* `/arm-full-optimization`: run an advanced iterative optimization workflow across multiple APX recipes
* `/arm-vs-x86-performance-comparison`: compare the same workload on x86 and Arm systems
