# Description 📝
* Sensors used in Automatic Gate [blueprint](https://github.com/etiennec78/Home-Automation/tree/master/Automatic%20Gate), [automations](https://github.com/etiennec78/Home-Automation/tree/master/Automatic%20Gate/Extra/Automations), [frontend](https://github.com/etiennec78/Home-Automation/tree/master/Automatic%20Gate/Extra/Frontend), and [gate firmware](https://github.com/etiennec78/Home-Automation/tree/master/Automatic%20Gate/Extra/Esphome%20gate%20firmware)
* Prefer installing sensors through the UI when possible
* Replace user0 with your real name
* Some sensors require to be set for each user


# Required sensors 📌
## Gate ⛩️
**Any gate or garage door which is either a switch or a cover**

Could be from my [esphome firmware](https://github.com/etiennec78/Home-Automation/blob/master/Automatic%20Gate/Extra/Esphome%20gate%20firmware/gate.yaml) or any other integration


## GPS location trackers 🌎
**Each GPS location tracker necessary to detect if you're home or away, estimate your travel time, and track your distance**

*Note : Try to avoid using wifi/ble location trackers as latency could be an issue. Use high precision when driving near home*

Install through [companion app](https://companion.home-assistant.io/docs/core/location/) settings : `Settings > Companion app > Manage sensors > Background location ✔️`

**Settings :**
* High accuracy mode : ✔️
* High accuracy mode only when connected to BT devices : Select vehicles bluetooth devices
* High accuracy mode only when entering zone : `zone.home`
* High accuracy mode trigger range for zone : The range in which you want your phone to spam location updates when arriving (suggested : 500m)
* High accuracy interval : `5s`
* High accuracy mode only when connected to BT devices : ✔️
* Minimal precision : Keep default
* Location sent : `Exact`

Then, assign these device trackers to each person by going into : `Settings > People > Select a user > Track devices > Your new device tracker`


## Driving sensors 🚗
**Each driving sensor which will detect when you start and stop driving**

Either Android Auto, bluetooth connexion, or both grouped

### Option 1 : Android Auto connection
Install through [companion app](https://companion.home-assistant.io/docs/core/sensors#android-auto) settings : `Settings > Companion app > Manage sensors > Android Auto ✔️`

### Option 2 : Bluetooth connection with template sensor
Install through [companion app](https://companion.home-assistant.io/docs/core/sensors/#bluetooth-sensors) settings : `Settings > Companion app > Manage sensors > Bluetooth connection ✔️`

Use a [template helper](https://www.home-assistant.io/integrations/template/)

Install through the UI : `Settings > Devices & services > Helpers tab > Create helper > Template > Binary sensor`

Settings :
* Name : `User0 driving`
* State template : `{{ '00:00:00:00:00:00 (BT-Device)' in state_attr('sensor.user0_bluetooth_connection', 'connected_paired_devices') }}`
* Device class : `Moving`

Or in your configuration.yaml file :

```yaml
template:
  - binary_sensor:
    - name: "user0 driving"
      unique_id: "user0_driving"
      icon: mdi:steering
      device_class:  moving
      state: >
        {{ '00:00:00:00:00:00 (BT-Device)' in state_attr('sensor.user0_bluetooth_connection', 'connected_paired_devices') }}
```

### Option 3 : Both (for multiple vehicles)
Install sensors from options 1 & 2

Then use the following template : `{{ is_state('binary_sensor.user0_android_auto', 'on') or '00:00:00:00:00:00 (BT-Device)' in state_attr('sensor.user0_bluetooth_connection', 'connected_paired_devices') }}`


## Travel time sensors ✈️
**Each travel time sensor which calculates the travel time between each user and your home**

### Option 1 : Waze
**⚠️ Please disable [auto-polling](https://www.home-assistant.io/integrations/waze_travel_time/#defining-a-custom-polling-interval)**

Delivered by the [Waze Travel Time](https://www.home-assistant.io/integrations/waze_travel_time/) integration

Install through the UI : `Settings > Devices & services > Add integration > Waze Travel Time`

Settings :
* Name : `User0 Travel Time`
* Origin : `person.user0`
* Destination : `zone.home`
* Region : Select your region

### Option 2 : Google Maps
**⚠️ Please disable [auto-polling](https://www.home-assistant.io/integrations/waze_travel_time/#defining-a-custom-polling-interval)**

⚠️ Work in progress ! The current integration is not reporting usable attributes

Delivered by the [Google Maps Travel Time](https://www.home-assistant.io/integrations/google_travel_time/) integration

Install through the UI : `Settings > Devices & services > Add integration > Google Maps Travel Time`
* Name : `User0 Travel Time`
* API key : Your api key
* Origin : `person.user0`
* Destination : `zone.home`


## Proximity sensors ↔️
**Each proximity sensor which calculates the distance of each user from home**

Delivered by the [Proximity](https://www.home-assistant.io/integrations/proximity/) integration

Install through the UI : `Settings > Devices & services > Add integration > Proximity`

Settings :
* Track distance to : `zone.home`
* Devices or persons to track : `[person.user0, ...]`
* Zones to ignore : []
* Tolerance distance : Not required by Automatic Gate


## Notify services 💬
**Each phone notification service to notify of the itinerary status**

Delivered by the [companion app](https://companion.home-assistant.io/docs/notifications/notifications-basic) by default


## Itinerary sensors 🗺️
**Each itinerary helper whicA helper you have to create which will store the state of each user ih will store the state of each user itinerary**

Use an [input text helper](https://www.home-assistant.io/integrations/input_text/)

Install through the UI : `Settings > Devices & services > Helpers tab > Create helper > Text`

Settings :
* Name : `User0 itinerary`
* Icon : `mdi:map`
* Minimum lenght : `0`
* Maximum length : `100`
* Display mode : `Text`

Or in your configuration.yaml file :

```yaml
input_text:
  user0_itinerary:
    name: User0 itinerary
    initial: none
    icon: mdi:map
```

## Planned opening 📅
**A helper which will store the planned automatic opening of the gate**

Use an [input datetime helper](https://www.home-assistant.io/integrations/input_datetime/)

Install through the UI : `Settings > Devices & services > Helpers tab > Create helper > Date and/or time`

Settings :
* Name : `Automatic gate planned opening`
* What do you want to input : `Date and time`

Or in your configuration.yaml file :

```yaml
input_datetime:
  planned_opening:
    name: Automatic gate planned opening
    has_date: true
    has_time: true
```



# Optional sensors ➕

## Bluetooth transmitter 📡
**Companion app ble transmitter to let a scanner automatically close your gate upon leaving**

The automation will automatically turn the transmitter off if not needed

Install through [companion app](https://www.home-assistant.io/integrations/mobile_app/) settings : `Settings > Companion app > Manage sensors > BLE Transmitter ✔️`

Settings :
* Advertise mode : `Low latency (10Hz)`
* Transmit only enabled on Home Wifi Network SSIDs : ❌
* major : `100`
* Measured power at 1 meter : Not necessary for Automatic Gate
* minor : `1`
* Enable transmitter : ❌
* Transmit power : `High`
* UUID : A [random UUID](https://www.uuidgenerator.net/)

## Bluetooth entities 🔎
**Each BLE entity to monitor when someone is leaving home, to close when it goes to unavailable**

Could be from my [esphome firmware](https://github.com/etiennec78/Home-Automation/blob/master/Automatic%20Gate/Extra/Esphome%20gate%20firmware/gate.yaml) or any other bluetooth iBeacon scanner near your gate

## Bluetooth scanner switch ⏻
**A switch which can turn on/off your BLE scanner. Not useful if you want your BLE scanner running 24/7**

## Notify all devices group 🔔
**A group which allows my [esphome firmware](https://github.com/etiennec78/Home-Automation/blob/master/Automatic%20Gate/Extra/Esphome%20gate%20firmware/gate.yaml) and [automations](https://github.com/etiennec78/Home-Automation/tree/master/Automatic%20Gate/Extra/Automations) to notify all users in case of an event like your gate opening**

Use a [notification group](https://www.home-assistant.io/integrations/group/#notify-groups)

Install in your configuration.yaml file :
```yaml
notify:
  platform: group
  name: "All devices"
  services:
    - service: mobile_app_user0_phone
    - ...
```

## Nearest distance sensor 🤏
Only necessary for [esphome firmware](https://github.com/etiennec78/Home-Automation/blob/master/Automatic%20Gate/Extra/Esphome%20gate%20firmware/gate.yaml)

**Gives the distance of the nearest person from home, to only open if someone is close enough**

To install, please follow the instructions for the [required proximity sensors](https://github.com/etiennec78/Home-Automation/edit/master/Automatic%20Gate/sensors.md#proximity-sensors)