# Based on (https://github.com/PiotrMachowski/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor)

[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://hacs.xyz/docs/faq/custom_repositories)
[![Community Forum](https://img.shields.io/badge/Community-Forum-41BDF5.svg?style=popout)](https://community.home-assistant.io/t/xiaomi-cloud-vacuum-map-extractor/231292)

# Xiaomi Cloud Map Extractor

This custom integration provides a way to present a live view of a map for a Xiaomi vacuum.

<img src="https://raw.githubusercontent.com/Teka101/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor/master/images/map_no_rooms.png" width=48%>  <img src="https://raw.githubusercontent.com/Teka101/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor/master/images/map_rooms.png" width=48%>

## Installation

### Using [HACS](https://hacs.xyz/) (recommended)

This integration can be added to HACS as a [custom repository](https://hacs.xyz/docs/faq/custom_repositories):
* URL: `https://github.com/Teka101/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor`
* Category: `Integration`

After adding a custom repository you can use HACS to install this integration using user interface.

## Configuration

After installation of the custom component, it needs to be configured in `configuration.yaml` file.
To do so, add a camera entry to your configuration with at least a [basic](#basic) or [recommended](#recommended) configuration.
Vacuum token can be extracted by following [this guide](https://www.home-assistant.io/integrations/xiaomi_miio/#retrieving-the-access-token).
You also need to enter your Xiaomi Cloud username and password.
These are the credentials used for the Xiaomi Home app (_not ones from Roborock app_).

After installation and a reboot of your Home Assistant instance, you should get a camera entity which shows the vacuum map.
This might take a few minutes after a first restart.
If you have a problem with configuration validation you have to remove camera from `configuration.yaml`, restart Home Assistant, add camera config and restart HA again.

#### Available configuration parameters

| Key | Type | Required | Value | Description |
|---|---|---|---|---|
| `platform` | string | true | `xiaomi_cloud_map_extractor_v2` | Name of a platform |
| `host` | string | true | `192.168.0.123` | IP address of a vacuum |
| `token` | string | true | `ghjhca3ykg8o2zyyj7xb5adamhgsypel` | Token of a vacuum |
| `username` | string | true | `xiaomi.account@gmail.com` | Username (email or user ID) used to connect to Xiaomi cloud (the account used in the Xiaomi Home app) |
| `password` | string | true | `aVerySecretPassword` | Password used to connect to Xiaomi cloud (the account used in the Xiaomi Home app) |
| `name` | string | false |   | Desired name of camera entity |
| `country` | string | false | One of: `cn`, `de`, `us`, `ru`, `tw`, `sg`, `in`, `i2` | Server used in Xiaomi cloud. Leave empty if you are not sure. |
| `colors` | map | false |  | Colors configuration ([see below](#colors-configuration)) |
| `room_colors` | map | false |  | Room colors configuration ([see below](#room-colors-configuration)) |
| `draw` | list | false |  | List of elements to draw on a map ([see below](#draw-configuration)) |
| `texts` | list | false |  | List of texts to draw on a map ([see below](#texts-configuration)) |
| `map_transformation` | map | false |  | Parameters of map transformation ([see below](#map-transformation-configuration)) |
| `sizes` | map | false |  | Sizes of map's elements ([see below](#sizes-configuration)) |
| `attributes` | list | false |  | List of desired entity attributes ([see below](#attributes-configuration)) |
| `scan_interval` | interval | false | default: `5` seconds | Interval between map updates ([documentation](https://www.home-assistant.io/docs/configuration/platform_options/#scan-interval)) |
| `auto_update` | boolean | false | default: `true` | Activation/deactivation of automatic map updates. If disabled use service `homeassistant.update_entity` to update map manually. |

#### Colors configuration

  Each color is represented by a list of 3 or 4 parameters: `[red, green, blue]` or `[red, green, blue, alpha]`.
  Each parameter is a number from a range 0-255 and can be also provided as a HEX value: [0x12, 0xAF, 0xC5] matches #12AFC5. 
  
  <img src="https://raw.githubusercontent.com/Teka101/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor/master/images/map_no_rooms_custom_colors.png" width=50%>
   
  | Color name | Description |
  | --- | --- |
  | `color_map_inside` | Map inside (for software without rooms support) |
  | `color_map_outside` | Map outside |
  | `color_map_wall` | Walls (for software without rooms support) |
  | `color_map_wall_v2` | Walls (for software with rooms support) |
  | `color_grey_wall` | Obstacles (e.g. chairs, table legs) |
  | `color_path` | Path of a vacuum |
  | `color_goto_path` | Path for goto mode |
  | `color_predicted_path` | Predicted path to a point in goto mode |
  | `color_zones` | Fill of areas selected for zoned cleaning |
  | `color_zones_outline` | Outline of areas selected for zoned cleaning |
  | `color_virtual_walls` | Virtual walls |
  | `color_no_go_zones` | Fill of no-go zones |
  | `color_no_go_zones_outline` | Outline of no-go zones |
  | `color_no_mop_zones` | Fill of no-mopping zones |
  | `color_no_mop_zones_outline` | Outline of no-mopping zones |
  | `color_charger` | Charger position |
  | `color_obstacle` | Obstacle position |
  | `color_robo` | Vacuum position |
  | `color_scan` | Areas not assigned to any room (for software with rooms support) |
  | `color_unknown` | Other areas |

#### Room colors configuration

  This section contains mapping between room numbers and colors.
  Each color is represented by a list of 3 or 4 parameters: `[red, green, blue]` or `[red, green, blue, alpha]`.
  Each parameter is a number from a range 0-255 and can be also provided as a HEX value: [0x12, 0xAF, 0xC5] matches #12AFC5. 

#### Draw configuration

  A list of features to be drawn on a map. If all features should be drawn it can be replaced with:
  ```yaml
    draw: ["all"]
  ```
  Available values:
  - `charger`
  - `path`
  - `goto_path`
  - `predicted_path`
  - `no_go_zones`
  - `no_mopping_zones`
  - `vacuum_position`
  - `virtual_walls`
  - `zones`

#### Texts configuration

Each list entry must obey a following schema.
You can get a list of available fonts by executing this command:
```bash
fc-list | grep ttf | sed "s/.*\///"| sed "s/ttf.*/ttf/"
```

  | Parameter | Type | Required | Default value | Description |
  |---|---|---|---|---|
  | `text` | string | true |   | Text to draw on a map |
  | `x` | float | true |   | X position of a text (in percents) |
  | `y` | float | true |   | Y position of a text (in percents) |
  | `color` | list | false | black | Desired color of a text, formatted like [here](#colors-configuration) |
  | `font` | string | false |   | Name of a font to use |
  | `font_size` | int | false |   | Size of a font |

#### Map transformation configuration

  | Parameter | Type | Required | Default value | Description |
  |---|---|---|---|---|
  | `scale` | float | false | 1 | Scaling factor for a map. |
  | `rotate` | integer | false | 0 | Angle of map rotation. Available values: [`0`, `90`, `180`, `270`] |
  | `trim` | map | false | 0 | Map trimming configuration. Each trimming direction is in percents: value `25` means trimming of quarter in a given dimension. Available keys: [`left`, `right`, `top`, `bottom`] |

#### Sizes configuration

  | Parameter | Type | Required | Default value | Description |
  |---|---|---|---|---|
  | `charger_radius` | float | false | 4 | Radius of a charger circle. |
  | `vacuum_radius` | float | false | 4 | Radius of a vacuum circle. |

#### Attributes configuration

  A list of attributes that an entity should have.
  Available values:
  - `calibration_points` - Calculated calibration points for [Lovelace Xiaomi Vacuum Map card](https://github.com/PiotrMachowski/lovelace-xiaomi-vacuum-map-card).

     <img src="https://raw.githubusercontent.com/Teka101/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor/master/images/map_card.gif" width=50%>
  
  - `charger`
  - `country`
  - `goto`
  - `goto_path`
  - `goto_predicted_path`
  - `image`
  - `is_empty`
  - `map_name`
  - `no_go_areas`
  - `no_mopping_areas`
  - `obstacles`
  - `path`
  - `room_numbers`
  - `rooms`
  - `vacuum_position`
  - `vacuum_room`
  - `walls`
  - `zones`
  
### Examples

#### Basic

```yaml
camera:
  - platform: xiaomi_cloud_map_extractor_v2
    host: !secret xiaomi_vacuum_host
    token: !secret xiaomi_vacuum_token
    username: !secret xiaomi_cloud_username
    password: !secret xiaomi_cloud_password
```

#### Recommended

```yaml
camera:
  - platform: xiaomi_cloud_map_extractor_v2
    host: !secret xiaomi_vacuum_host
    token: !secret xiaomi_vacuum_token
    username: !secret xiaomi_cloud_username
    password: !secret xiaomi_cloud_password
    draw: ['all']
    attributes:
      - calibration_points    
```


#### Full

```yaml
camera:
  - platform: xiaomi_cloud_map_extractor_v2
    host: !secret xiaomi_vacuum_host
    token: !secret xiaomi_vacuum_token
    username: !secret xiaomi_cloud_username
    password: !secret xiaomi_cloud_password
    country: "de"
    name: "My Vacuum Camera"
    colors:
      color_map_inside: [32, 115, 185]
      color_map_outside: [19, 87, 148]
      color_map_wall: [100, 196, 254]
      color_map_wall_v2: [93, 109, 126]
      color_grey_wall: [93, 109, 126]
      color_path: [147, 194, 238]
      color_goto_path: [0, 255, 0]
      color_predicted_path: [255, 255, 0, 0]
      color_zones: [0xAD, 0xD8, 0xFF, 0x8F]
      color_zones_outline: [0xAD, 0xD8, 0xFF]
      color_virtual_walls: [255, 0, 0]
      color_obstacle: [0, 0, 0, 180]
      color_no_go_zones: [255, 33, 55, 127]
      color_no_go_zones_outline: [255, 0, 0]
      color_no_mop_zones: [163, 130, 211, 127]
      color_no_mop_zones_outline: [163, 130, 211]
      color_charger: [0x66, 0xfe, 0xda, 0x7f]
      color_robo: [75, 235, 149]
      color_unknown: [0, 0, 0]
      color_scan: [0xDF, 0xDF, 0xDF]
    room_colors:
      1: [240, 178, 122]
      2: [133, 193, 233]
      3: [217, 136, 128]
      4: [52, 152, 219]
      5: [205, 97, 85]
      6: [243, 156, 18]
      7: [88, 214, 141]
      8: [245, 176, 65]
      9: [252, 212, 81]
      10: [72, 201, 176]
      11: [84, 153, 199]
      12: [133, 193, 233]
      13: [245, 176, 65]
      14: [82, 190, 128]
      15: [72, 201, 176]
      16: [165, 105, 18]
    draw:
      - charger
      - path
      - goto_path
      - predicted_path
      - obstacles
      - no_go_zones
      - no_mopping_zones
      - vacuum_position
      - virtual_walls
      - zones
    texts:
      - text: "Room 1"
        x: 25
        y: 25
        color: [125, 20, 213]
      - text: "Room 2"
        x: 25
        y: 75
        color: [125, 20, 213, 127]
        font: "FreeSans.ttf"
        font_size: 25
    map_transformation:
      scale: 2
      rotate: 180
      trim:
        top: 10
        bottom: 20
        left: 30
        right: 40
    sizes:
      charger_radius: 4
      obstacle_radius: 3
      vacuum_radius: 6.5
    attributes:
      - calibration_points
      - charger
      - country
      - goto
      - goto_path
      - goto_predicted_path
      - image
      - is_empty
      - map_name
      - no_go_areas
      - no_mopping_areas
      - obstacles
      - path
      - room_numbers
      - rooms
      - vacuum_position
      - vacuum_room
      - walls
      - zones
    scan_interval:
      seconds: 10
    auto_update: true
```

## Supported devices

This integration was tested on following vacuums:
 - Xiaomi Vacuum Gen 1 (Mi Robot Vacuum/SDJQR01RR/SDJQR02RR)
 - Xiaomi Mi Robot 1S 
 - Roborock S4 (software with rooms support)
 - Roborock S5 (software without rooms support)
 - Roborock S5 (software with rooms support)
 - Roborock S5 Max
 - Roborock S6
 - Roborock S6 MaxV
 - Roborock S6 Pure
 - Roborock S7
 
## Unsupported devices

At this moment this integration is known to not work with following vacuums:
 - Roborock E50
 - Xiaomi Mi Robot Vacuum Mop Pro (STYJ02YM)
