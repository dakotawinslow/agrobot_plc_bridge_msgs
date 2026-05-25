# agrobot_plc_bridge_msgs

ROS2 message and service definitions for the agrobot physical operator panel.

This package contains **only interface definitions** — no executable code, no
OPC UA dependency.  Any ROS2 node that needs to interact with the PLC bridge
(send commands, read panel state, receive button events) depends on this
package rather than the full bridge node.

## Interfaces

### `msg/OutputState`

Represents a single physical output (light or buzzer) and its desired state.

| Constant | Value | Description |
|---|---|---|
| `OUTPUT_RUNNING_TOWER` | 0 | Green tower light |
| `OUTPUT_FAULT_TOWER` | 1 | Red tower light |
| `OUTPUT_AUTO` | 2 | AUTO panel button light |
| `OUTPUT_CYCLE` | 3 | CYCLE panel button light |
| `OUTPUT_STEP` | 4 | STEP panel button light |
| `OUTPUT_PREV` | 5 | PREV panel button light |
| `OUTPUT_HALT` | 6 | HALT panel button light |
| `OUTPUT_SLOW` | 7 | SLOW panel button light |
| `OUTPUT_BUZZER` | 8 | Buzzer |

| State constant | Value | Description |
|---|---|---|
| `STATE_OFF` | 0 | Off |
| `STATE_SOLID` | 1 | Solid on |
| `STATE_BLINK` | 2 | Slow blink (~1 Hz) |
| `STATE_FAST_BLINK` | 3 | Fast blink (~4 Hz) |

### `srv/SetOutputs`

Write one or more panel outputs in a single call.

```
OutputState[] outputs          # outputs to write (others unchanged)
---
bool          success
uint8         error_code        # ERROR_NONE | ERROR_OPC_UA_UNREACHABLE | ERROR_OPC_UA_WRITE_FAILED
string        error_message
OutputState[] actual_states    # readback of all 9 outputs after write
```

### `srv/QueryOutputs`

Read the current state of all 9 outputs.

```
# (empty request)
---
bool          success
bool          opc_ua_sync       # False if values may be stale
uint8         error_code        # ERROR_NONE | ERROR_OPC_UA_UNREACHABLE
string        error_message
OutputState[] states            # all 9 outputs
```

### `srv/ButtonStateChange`

Notification service called by the bridge when a button in `state_change` mode
is pressed or released.

```
bool pressed    # True = press, False = release
---
bool   success
string message
```

## Dependencies

- ROS2 Jazzy (or compatible)
- `rosidl_default_generators` (build)
- `rosidl_default_runtime` (runtime)

## Building

```bash
cd <your_ws>
colcon build --packages-select agrobot_plc_bridge_msgs
source install/setup.bash
```

## Related

- [`agrobot_plc_bridge`](https://github.com/dakotawinslow/agrobot_plc_bridge) — the bridge node that implements these services
