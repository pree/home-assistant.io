---
title: Synology DSM
description: Instructions on how to integrate the Synology DSM sensor within Home Assistant.
ha_category:
  - Camera
  - System Monitor
ha_release: 0.32
ha_iot_class: Local Polling
ha_domain: synology_dsm
ha_codeowners:
  - '@hacf-fr'
  - '@Quentame'
ha_config_flow: true
---

The `synology_dsm` sensor platform provides access to various statistics from your [Synology NAS](https://www.synology.com) as well as cameras from the [Surveillance Station](https://www.synology.com/en-us/surveillance).

## Configuration

There are two ways to integrate your Synology DSM into Home Assistant.

### Via the frontend

Menu: *Configuration* -> *Integrations*. Search for "Synology DSM", fill in the configuration form with your username and password, and then click **Submit**.

### Via the configuration file

Add the following section to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
synology_dsm:
  - host: IP_ADDRESS_OR_HOSTNAME_OF_SYNOLOGY_NAS
    username: YOUR_USERNAME
    password: YOUR_PASSWORD
```

{% configuration %}
host:
  description: The IP address or DNS hostname of the Synology NAS to monitor.
  required: true
  type: string
port:
  description: The port number on which the Synology NAS is reachable.
  required: false
  default: 5001 if `ssl` is true, 5000 if `ssl` is false
  type: integer
ssl:
  description: Determine if HTTPS should be used.
  required: false
  default: true
  type: boolean
username:
  description: The account username to connect to the Synology NAS. Using a separate account is advised, see the [Separate User Configuration](#separate-user-configuration) section below for details.
  required: true
  type: string
password:
  description: The password of the user to connect to the Synology NAS.
  required: true
  type: string
volumes:
  description: "Array of volumes to monitor. Defaults to all volumes. Replace any spaces in the volume name with underscores. For example, replace `volume 1` with `volume_1`."
  required: false
  type: list
disks:
  description: "Array of disks to monitor. Defaults to all disks. Use only disk names like `sda`, `sdb`, and so on."
  required: false
  type: list
{% endconfiguration %}


<div class='note warning'>

This sensor will wake up your Synology NAS if it's in hibernation mode.

You can change the scan interal within the configuration options (default is 15 min).

Having cameras or the Home mode toggle from [Surveillance Station](https://www.synology.com/en-us/surveillance) will fetch every 30 seconds. Disable those entities if you don't want your NAS to be fetch as frequently.

</div>


## Separate User Configuration

Due to the nature of the Synology DSM API, it is required to grant the user admin rights. This is related to the fact that utilization information is stored in the core module.

When creating the user, it is possible to deny access to all locations and applications. By doing this, the user will not be able to login to the web interface or view any of the files on the Synology NAS. It is still able to read the utilization and storage information using the API.

### If you utilize 2-Step Verification or Two Factor Authentication (2FA) with your Synology NAS

If you have the "Enforce 2-step verification for the following users" option checked under **Control Panel > User > Advanced > 2-Step Verification**, you'll need to configure the 2-step verification/one-time password (OTP) for the user you just created before the credentials for this user will work with Home Assistant. 

Make sure to log out of your "normal" user's account and then login with the separate user you created specifically for Home Assistant. DSM will walk you through the process of setting up the one-time password for this user which you'll then be able to use in Home Assistant's frontend configuration screen. 

<div class='note'>
If you denied access to all locations and applications it is normal to receive a message indicating you do not have access to DSM when trying to login with this separate user. As noted above, you do not need access to the DSM and Home Assistant will still be able to read statistics from your NAS.
</div>

## Sensors

Utilisation:
- `cpu_other_load`: Displays unspecified (that is, not user or system) load in percentage.
- `cpu_user_load`: Displays user load in percentage.
- `cpu_system_load`: Displays system load in percentage.
- `cpu_total_load`: Displays combined load in percentage.
- `cpu_1min_load`: Displays maximum load in past minute.
- `cpu_5min_load`: Displays maximum load in past 5 minutes.
- `cpu_15min_load`: Displays maximum load in past 15 minutes.
- `memory_real_usage`: Displays percentage of memory used.
- `memory_size`: Displays total size of memory in MB.
- `memory_cached`: Displays total size of cache in MB.
- `memory_available_swap`: Displays total size of available swap in MB.
- `memory_available_real`: Displays total size of memory used (based on real memory) in MB.
- `memory_total_swap`: Displays total size of actual memory in MB.
- `memory_total_real`: Displays total size of real memory in MB.
- `network_up`: Displays total up speed of network interfaces (combines all interfaces).
- `network_down`: Displays total down speed of network interfaces (combines all interfaces).

Information:
- `temperature`: Displays the temperature of the NAS.
- `uptime`: Displays the uptime of the NAS (in seconds).

For each disk:
- `disk_smart_status`: Displays the S.M.A.R.T status of the disk.
- `disk_status`: Displays the status of the disk.
- `disk_temp`: Displays the temperature of the disk.

For each volume:
- `volume_status`: Displays the status of the volume.
- `volume_size_total`: Displays the total size of the volume in TB's.
- `volume_size_used`: Displays the used space on this volume in TB's.
- `volume_percentage_used`: Displays the percentage used for this volume.
- `volume_disk_temp_avg`: Displays the average temperature of all disks in the volume.
- `volume_disk_temp_max`: Displays the maximum temperature of all disks in the volume.


## Binary sensors

Security:
- `security_status`: Displays safe to indicate if the NAS is safe.

For each disk:
- `disk_exceed_bad_sector_thr`: Displays on to indicate if the disk exceeded the maximum bad sector threshold. (Does not work with DSM 5.x)
- `disk_below_remain_life_thr`: Displays on to indicate if the disk dropped below the remain life threshold. (Does not work with DSM 5.x)


## Switch
- `home_mode`: Displays a toggle to enable/disable the [Surveillance Station](https://www.synology.com/en-us/surveillance) Home mode.


## Cameras
- `{camera_name}`: Displays cameras added in [Surveillance Station](https://www.synology.com/en-us/surveillance).
