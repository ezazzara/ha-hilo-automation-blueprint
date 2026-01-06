# ha-hilo-automation-blueprint

Hilo (peak) automation blueprint for Home Assistant.

Manage AM/PM preheat, challenge (reduction), and comfort recovery phases for one or more thermostats. Supports configurable skip toggles, optional water-heater control, scene restoration, and notifications.

---

## Quick features ⚙️

- Independent AM and PM configurations for preheat, challenge, and recovery temperatures
- Optional preheat phase
- Automatic challenge (reduced) temperature while peak is active
- Recovery set to normal_temp + offset and held for a configurable duration
- Optional water-heater control during challenge/recovery
- Optional scene restoration and notification support

---

## How it works (brief) 🔍

- **Triggers:** the `preheat` and `peak` binary sensors; optional "peak tomorrow" sensors for notifications.
- **AM/PM & logic:** The automation determines AM vs PM from `now().hour`, applies skip toggles, and selects the configured temperatures.
- **Sequence:**
  1. Preheat — set preheat temperature when `preheat` sensor is ON (if preheat enabled)
  2. Challenge — set challenge temperature while `peak` sensor is ON
  3. Recovery — set recovery temperature (normal + offset), hold for configured duration, restore scene, and clear the skip toggle for that period.

---

## Example scenarios 📋

- Standard AM event: `preheat` ON → thermostat set to AM preheat temp; `peak` ON → thermostat set to AM challenge temp; `peak` OFF → thermostat set to normal + AM offset for recovery, then scene restored.

- Skip PM: toggle the configured `input_boolean` (required) to skip preheat, challenge, and recovery for the next PM event.

- Water-heater control: provide a `water_heater` switch to be turned off during challenge and turned on during recovery.

---

## Inputs (short) 🧩

- `preheat_entity`, `peak_entity` (binary_sensor)
- `skip_am_entity`, `skip_pm_entity` (`input_boolean`, REQUIRED) — runtime toggles used for notification-driven skipping
- `do_pre_heat` (boolean)
- `thermostats` (climate, required)
- `water_heater` (optional switch)
- Temperatures: `normal_temp`, `preheat_temp_am`, `preheat_temp_pm`, `challenge_temp_am`, `challenge_temp_pm`
- Recovery: `appreciation_offset_am`, `appreciation_offset_pm`, `comfort_recovery_duration`
- `restore_scene` (optional), `notify_device`, `peak_tomorrow_am_entity`, `peak_tomorrow_pm_entity` (optional)

---

## Install 📥

1. Preferred (recommended): Import the blueprint from this repository via Home Assistant **Configuration → Automations → Blueprints → Import Blueprint** and paste the raw GitHub URL for this blueprint. This links the blueprint in the UI and is the recommended method.

2. Manual: download `blueprints/hilo_peak_manager.yaml` and copy it to `config/blueprints/automation/`, then reload automations in Home Assistant.

---

## Creating skip toggles (required)

This blueprint requires two `input_boolean` Toggle helpers to be present and selected as `skip_am_entity` and `skip_pm_entity.

- UI: Home Assistant → Settings → Devices & Services → Helpers → Add Helper → Toggle. Create e.g. `hilo_skip_am` and `hilo_skip_pm`.

- YAML example (configuration.yaml or a helpers file):

```yaml
input_boolean:
  hilo_skip_am:
    name: "Hilo: Skip next AM challenge"
    icon: mdi:weather-sunset-up
  hilo_skip_pm:
    name: "Hilo: Skip next PM challenge"
    icon: mdi:weather-night
```

After creating the helpers, select them in the blueprint inputs under "Participation & Skip Toggles".

---

## Notes ⚠️

- AM/PM is determined by `now().hour` (0–11 = AM, 12–23 = PM).
- Skip toggles are required `input_boolean` entities (`skip_am_entity` / `skip_pm_entity`). The blueprint will set or clear these `input_boolean` entities when the user taps the actionable "Skip this event" notification — create and select the `input_boolean` helpers before configuring the blueprint.
- Recovery runs when `peak` goes OFF; a more robust approach would set and check an explicit "challenge started" flag.

---

## Contributing 🤝

Bugs, improvements, and pull requests are welcome.
