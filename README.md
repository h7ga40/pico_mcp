# MCP for Raspberry Pi Pico W

## ATX Power Button Pulse via JSON-RPC

The firmware exposes JSON-RPC tools that can be invoked using the `tools/call` method. Use `tools/list` to discover available tools:
`set_location`, `set_switch_id`, and `set_switch`.
These allow you to configure the target switch and trigger a momentary ATX PWR_SW pulse.

Example requests:

```json
{ "jsonrpc": "2.0", "method": "tools/call",
  "params": { "name": "set_location", "arguments": { "location": "office" } },
  "id": 1 }
```

```json
{ "jsonrpc": "2.0", "method": "tools/call",
  "params": { "name": "set_switch_id", "arguments": { "switch_id": "led" } },
  "id": 2 }
```

```json
{ "jsonrpc": "2.0", "method": "tools/call",
  "params": { "name": "set_switch", "arguments": { "state": "on" } },
  "id": 3 }
```

Call `set_switch` with `"state": "on"` or `"off"`; the state value is accepted for
compatibility but always triggers a single pulse. The pulse fires only when the
request's `location` or `switch_id` matches the previously set values or when
both fields are omitted.

GPIO configuration defaults (adjust in `pico_mcp.c`):

- `ATX_PWR_SW_GPIO` (GPIO number)
- `ATX_PWR_SW_ACTIVE_LEVEL` (active-high or active-low)
- `ATX_PWR_SW_PULSE_MS` (pulse width in ms, default 200ms)

## How it works

Use the agent mode of Github copilot Chat in Visual Studio Code.

https://github.com/user-attachments/assets/7c040786-b527-4f8a-829e-545591232b0f

## Installing the pico-sdk

The following is an excerpt from a previous setup guide.

1. Create a working directory

   ```bash
   mkdir -p ~/.pico-sdk/sdk
   ```

2. Download the latest release (2.1.1) from the official Raspberry Pi repository

   ```bash
   cd ~/.pico-sdk/sdk
   git clone -b 2.1.1 https://github.com/raspberrypi/pico-sdk.git 2.1.1
   cd 2.1.1
   git submodule update --init
   ```

3. Set the `PICO_SDK_PATH` environment variable

   ```bash
   export PICO_SDK_PATH=$HOME/.pico-sdk/sdk/2.1.1
   ```

After verifying that the repository was cloned and its submodules were checked out,
the pico-sdk will be available under `~/.pico-sdk/sdk/2.1.1`.

## Build Instructions

The commands below automatically fetch the Pico SDK and build the project.

```bash
mkdir build
cd build
cmake -E env PICO_SDK_FETCH_FROM_GIT=1 cmake ..
make -j$(nproc)
```

When the build succeeds, artifacts such as `pico_mcp.uf2` are generated in the
`build` directory.
