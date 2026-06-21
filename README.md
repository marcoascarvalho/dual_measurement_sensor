# Dual Measurement Sensor

`dual_measurement_sensor` is a custom Home Assistant integration created for the Book Advanced Smart Home Automation Solutions with Home Assistant. It listens to a single MQTT base topic containing a JSON payload from a device that reports both **temperature** and **presence**.

The integration filters noisy temperature readings, stabilizes presence detection, and exposes the device as two Home Assistant entities:

- `sensor.<device_id>_temperature`
- `binary_sensor.<device_id>_presence`

This approach allows one physical MQTT device to appear in Home Assistant as a clean, organized device with multiple related entities.

## Features

- Listens to a single MQTT topic with a JSON payload
- Creates a temperature sensor entity
- Creates a presence binary sensor entity
- Supports configurable JSON keys for temperature and presence
- Applies a moving average filter to temperature readings
- Publishes temperature changes only when the change is meaningful
- Applies debounce and hold timing to presence detection
- Configurable through the Home Assistant UI using a config flow
- Can be installed and updated using HACS

## Example MQTT Payload

The device should publish a JSON payload to the configured base topic for each sensor, presence and temperature.

Example topic for temperature:

```text
tele/Garage_Temp_PIR/SENSOR
```
Example payload:

```json
{"Time":"2026-06-21T01:42:25","Switch1":"OFF","BMP280":{"Temperature":40.9,"Pressure":917.7},"PressureUnit":"hPa","TempUnit":"C"}
```

Example topic for presence:

```text
sensor/Garage_Temp_PIR/PR1
```

Depending on your device firmware, the JSON keys can be changed during setup. For example, you may use `temp` instead of `temperature`, or `motion` instead of `presence`.

## Requirements

Before installing this integration, make sure that:

- Home Assistant is running properly
- HACS is installed and configured
- The MQTT integration is installed and configured in Home Assistant
- Your MQTT broker is receiving messages from the device
- The device publishes temperature and presence data in JSON format

You can use MQTT Explorer or the Home Assistant MQTT integration tools to confirm that the payload is being published correctly before configuring this integration.

## Installation with HACS

This integration is intended to be installed through HACS as a custom repository.

1. Open **HACS** in Home Assistant.
2. Open the menu in the upper-right corner.
3. Select **Custom repositories**.
4. Add this GitHub repository URL.
5. Select **Integration** as the repository category.
6. Click **Add**.
7. Find **Dual Measurement Sensor** in HACS.
8. Click **Download** or **Install**.
9. Restart Home Assistant when prompted.
10. Go to:

```text
Settings → Devices & services → Add integration
```

11. Search for:

```text
Dual Measurement Sensor
```

12. Complete the configuration flow.

> When installing through HACS, do not manually copy files into `custom_components`. HACS downloads and manages the integration files automatically.

## Repository Structure for HACS

For HACS installation, the repository should use the following structure:

```text
dual_measurement_sensor/
├── custom_components/
│   └── dual_measurement_sensor/
│       ├── __init__.py
│       ├── manifest.json
│       ├── config_flow.py
│       ├── const.py
│       ├── sensor.py
│       ├── binary_sensor.py
│       └── translations/
│           └── en.json
├── README.md
├── hacs.json
└── LICENSE
```

The integration runtime files must remain inside:

```text
custom_components/dual_measurement_sensor/
```

The repository-level files, such as `README.md`, `hacs.json`, and `LICENSE`, are used for documentation, HACS metadata, and project licensing.

## Configuration

During setup, the integration asks for the information required to read and process the MQTT payload.

| Option | Description | Example |
|---|---|---|
| Device ID | Name used to identify the device and create entity IDs | `dev1` |
| Base topic | MQTT topic where the JSON payload is published | `home/book2/dev1` |
| Temperature JSON key | JSON field containing the temperature value | `temperature` |
| Presence JSON key | JSON field containing the presence value | `presence` |
| Temperature window | Number of samples used for the moving average filter | `5` |
| Minimum delta to publish | Minimum temperature change required before updating the entity | `0.2` |
| Presence debounce | Time in milliseconds used to ignore unstable presence changes | `500` |
| Presence hold | Time in milliseconds to keep presence active after detection | `2000` |

## Created Entities

After configuration, the integration creates the following entities:

```text
sensor.<device_id>_temperature
binary_sensor.<device_id>_presence
```

For example, if the device ID is `dev1`, the created entities will be:

```text
sensor.dev1_temperature
binary_sensor.dev1_presence
```

These entities can be used in dashboards, automations, scripts, templates, and history graphs.

## Updating

When a new version is available, HACS can notify you and provide the update through the HACS interface.

After updating the integration through HACS, restart Home Assistant if HACS or Home Assistant requests it.

## Optional Manual Installation for Development

Manual installation is only recommended for development or local testing.

1. Copy the `dual_measurement_sensor` folder into your Home Assistant `custom_components` directory.

The final path should look like this:

```text
config/custom_components/dual_measurement_sensor/
```

2. Restart Home Assistant.

3. Go to:

```text
Settings → Devices & services → Add integration
```

4. Search for:

```text
Dual Measurement Sensor
```

5. Complete the configuration flow.

If you installed the integration through HACS, skip this manual installation section.

## Troubleshooting

### The integration does not appear in HACS

Confirm that:

- The GitHub repository URL is correct
- The repository was added as category **Integration**
- The repository contains `custom_components/dual_measurement_sensor/`
- The repository contains a valid `hacs.json` file
- The repository contains a valid `manifest.json` file inside the integration folder

### The integration appears in HACS but not in Home Assistant

Restart Home Assistant after installing the integration through HACS. Then go to:

```text
Settings → Devices & services → Add integration
```

Search for:

```text
Dual Measurement Sensor
```

If it still does not appear, check the Home Assistant logs under:

```text
Settings → System → Logs
```

### Entities are not created

Confirm that:

- The MQTT integration is configured
- The MQTT broker is receiving messages
- The base topic matches the topic used by the device
- The JSON payload is valid
- The configured JSON keys match the payload

### Temperature is not updating

Check that the temperature value is numeric and that the minimum delta is not set too high. If the minimum delta is large, small temperature changes will be ignored.

### Presence is unstable

Increase the debounce or hold values. This can help stabilize noisy motion or presence signals that rapidly switch between `on` and `off`.

## Development Notes

This integration is intended as an educational example for building Home Assistant custom integrations and preparing them for HACS distribution. It demonstrates how to combine MQTT input, configuration flows, sensor entities, binary sensor entities, filtering, and device organization into a reusable integration.

Key files include:

| File | Purpose |
|---|---|
| `manifest.json` | Defines Home Assistant integration metadata and dependencies |
| `__init__.py` | Initializes the integration |
| `config_flow.py` | Provides UI-based setup |
| `const.py` | Stores constants and default values |
| `sensor.py` | Creates the temperature sensor entity |
| `binary_sensor.py` | Creates the presence binary sensor entity |
| `translations/en.json` | Provides UI text for the configuration flow |
| `hacs.json` | Provides HACS-specific repository metadata |
| `README.md` | Provides installation, configuration, and troubleshooting documentation |
| `LICENSE` | Defines the project license |

## License

This project is provided as an educational example. 
