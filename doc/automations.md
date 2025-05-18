# Useful automations


## Automatically apply 60% export limit on Home Assistant startup

To comply with local regulations (e.g., German EEG §9), some installations are required to limit the export of active power to 60% of the installed PV capacity until smart meters are installed. The following Home Assistant automation ensures this limit is enforced every time Home Assistant starts:

```yaml
- alias: "Activate 60% export limit"
  trigger:
    - platform: homeassistant
      event: start
  action:
    - service: modbus.write_register
      data:
        hub: SungrowSHx
        unit: 1
        address: 13073
        value: 9610  # 60% of 16.02 kWp in watts
    - delay: "00:00:01"
    - service: modbus.write_register
      data:
        hub: SungrowSHx
        unit: 1
        address: 13086
        value: 170  # 0x00AA = enable export power limit mode
  mode: single
```

This automation writes two Modbus registers:
- `13073`: sets the export power limit (in watts).
- `13086`: activates the export power limitation mode.

You may adjust the values as needed based on your inverter model and installed PV capacity.