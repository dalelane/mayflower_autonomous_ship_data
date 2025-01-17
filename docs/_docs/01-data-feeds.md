---
title: "Data Feeds"
permalink: /docs/data-feeds/
excerpt: "A summary of live data feeds available from the Mayflower Autonomous Ship."
last_modified_at: 2022-03-31T08:48:05-04:00
toc: true
---

The Mayflower Autonomous Ship (MAS) has a number of live data feeds that are usually available whilst it is out at sea.

You can use the data feeds to find out all kinds of things about the ship, including:

- Live Navigational Data & AIS track
- Live Ship Status
- Live Science & Data Collection Status and readings
- Live Weather Data
- Replay Data

The live data feed is available via MQTT from `mqtt.mas400.com`, we are also looking into making historical data available once the MAS has completed its voyage from the UK to US in Spring 2022.


## Connecting using MQTT

You can connect to our public MQTT broker using both traditional MQTT and MQTT over websockets (useful if you want to build a web page that consumes the data).

The connection details are:

**MQTT**
```
Host: mqtt.mas400.com
Port: 1883
```

**MQTT Websockets**
```
Host: mqttws.mas400.com
Port: 443
```



Because our broker is public and read only, there is currently no authentication required.

## MQTT Topic Tree

As of March 2022, the current high level topic tree is as follows:

- [Connecting using MQTT](#connecting-using-mqtt)
- [MQTT Topic Tree](#mqtt-topic-tree)
- [Status Feed](#status-feed)
- [AIS Feed](#ais-feed)
- [Replay](#replay)
  - [Hazard Feed](#hazard-feed)
- [Future planned feeds](#future-planned-feeds)



[@andysc](https://twitter.com/andysc) is also hosting a [Hypercat](https://hypercatiot.github.io/) catalogue [here](http://hypercat.stanford-clark.com:1884/cat/MAS400). This can be a great way of programmatically exploring the topics and data available.


## Status Feed

When connected via Cellular or Satellite to the internet, the MAS can send a small packet of data up on a semi-regular basis (conditions dependant) containing a summary of the ships status.

| topic | Data Type |  Notes |
|-------|-----------|--------|
| JSON | JSON object | This is a JSON Object summarising the data to save you from subscribing to individual topics |
| date |  Timestamp | When the status update was received |
| shipDataTimeStamp | float | Unix Epoch timestamp for when the update was sent on the MAS |
| shipStatus | string | Ship Status: offline, standby, autonomous, remote |
| shipConnectivity | string | Whether the satellite link is active (Connected / Disconnected) |
| shipRudderAnglePercentThousands | float | The Rudder angle in degrees, the key name is misleading |
| shipLatitudeDeg | float | MAS Latitude in Degrees |
| shipLongitudeDeg | float | MAS Longitude in Degrees |
| shipHeadingDeg | float | MAS Heading in Degrees |
| shipSpeedOverGroundMS | float | MAS Speed over ground in m/s |
| shipCourseOverGroundDeg | float | MAS Course over Ground in Degrees |
| shipThrottlePercentThousands | float | MAS Throttle - TODO |
| shipPitchDeg | float  | MAS Pitch in Degrees |
| shipRollDeg | float | MAS Roll in Degrees |
| waterDepthM | float | fathometer depth in meters |
| batteryVoltageTenths | float | Overall battery voltage in tenths |
| batteryCapacityPct | float | Battery charge capacity in percent |
|solarChargerPowerWatts | float | Solar Power charging rate in Watts |
| weatherDataTimestamp | float | Unix Epoch timestamp for when the weather update was made |
| temperatureDegC | int | The Temperature where the MAS is |
| windSpeedKmH | int | Windspeed where the MAS is |
| windDirectionDeg | int | Wind Direction where the MAS is in Degrees |
| weather | string | short weather conditions description |
| weatherPhrase | string | longer weather conditions description |
| sunrise | timestamp | Sunrise time where the MAS is |
| sunset | timestamp | Sunset time where the MAS is |
| _id | string | Database record ID for this update |


**JSON Example**
```
{
   "shipDataTimeStamp": "1648751244.0",
   "shipLatitudeDeg": "50.36032",
   "shipLongitudeDeg": "-4.11589",
   "shipPitchDeg": "0.0",
   "shipRollDeg": "0.0",
   "shipSpeedOverGroundMS": "0.004",
   "shipCourseOverGroundDeg": "265.996",
   "waterDepthM": "0.0",
   "shipHeadingDeg": "168.7925",
   "batteryCapacityPct": "94.186989",
   "batteryVoltageTenths": "539.831846",
   "shipThrottlePercentThousands": "0.0",
   "shipRudderAnglePercentThousands": "-20.0",
   "solarChargerPowerWatts": "0.0", "shipStatus": "Standby",
   "weatherDataTimestamp": "1648751251.827717",
   "date": "2022-03-31 18:27:31.827717+00:00",
   "temperatureDegC": "7", "windSpeedKmH": "26",
   "windDirectionDeg": "350", "weather": "sun",
   "weatherPhrase": "Fair",
   "sunrise": "2022-03-31 05:55:25+00:00",
   "sunset": "2022-03-31 18:46:53+00:00",
   "shipConnectivity": "Connected",
   "_id": "6245f2931317b53e48ecc133"
}
```






## AIS Feed

The MAS is equipped with an [AIS](https://en.wikipedia.org/wiki/Automatic_identification_system) transponder which allows it to transmit its location, speed and bearing from almost anywhere in the world. This data is received either locally by other ships or via satellite so that it can be tracked by services online like [MarineTraffic](https://www.marinetraffic.com/en/ais/home/centerx:-4.109/centery:50.357/zoom:15). In cases where the MAS onboard satellite connection fails, then AIS is the best way to track it.

| topic | Data Type |  Notes |
|-------|-----------|--------|
| JSON | JSON object | This is a JSON Object summarising the data to save you from subscribing to individual topics |
| time | ISO8601 Timestamp | When the AIS update was received |
| latitude | float | Latitude of the MAS in degrees |
| longitude | float | Longitude of the MAS in degrees |
| speed | float | Speed in Knots |
| heading | float | Heading in degrees |
| courseOverGround | float | The Actual course of the ship |
| turnRate | float | Rate of turn (°/min) |

**JSON Example**

```
{
   "latitude":50.36039,
   "longitude":-4.115903,
   "speed":0,
   "heading":0,
   "courseOverGround":0,
   "turnRate":0,
   "timeStamp":"2022-03-31 18:28:12"
}
```



## Replay

Whilst the MAS is alongside the dock, the live data feed may not be particularly eventful. For those who might want to demonstrate their projects, a Replay feed is available on the `replay` topic. This has the same or similar underlying topic structure as the live topics with a few bonus topics only available once the MAS has offloaded certain datasets. Currently, the replay feed is replaying the June 2021 main voyage and should restart once complete on a loop.

For brevitys sake, the subtopics below will link to their live counterparts if they have them:

  - [status](#status-feed)
  - [AIS](#ais-feed)
  - [hazard](#hazard-feed)

### Hazard Feed
One of the experiments onboard the MAS is looking at how to optimise machine learning algorithms on custom silicon and so was running a simple object detection algorithm on the 6 camera feeds around the mast (__Note: This is not the same system used as part of the AI captain, which does not have a data feed__). Once the captured images and recorded classifications were uploaded off the MAS, these were added to the replay feed on the following topics:

| topic | Data Type |  Notes |
|-------|-----------|--------|
| JSON | JSON object | This is a JSON Object summarising the data to save you from subscribing to individual topics |
| time | ISO8601 Timestamp | When the classification was made |
| epoch_time | Unix Epoch Timestamp | When the classification was made |
| object_name | string | The classified object name |
| object_id | integer | The classified object ID |
| prob | float | The classification probability |
| cam_id | integer | The camera ID (1 to 6) |
| annotation | URL | A URL to the captured image with the annotation |

**JSON Example**

```
{
   "time":"2021-06-18T02:10:44.000Z",
   "epoch_time":1623982244,
   "object_name":"traffic light",
   "object_id":10,
   "prob":0.5565,
   "cam_id":1,
   "annotation":"https://mas-science-data-publicimages-mission1-jun21.s3.eu-gb.cloud-object-storage.appdomain.cloud/hds-run_y2021m06d17_h19m50s26_cam_1_frame_02377.png"
}
```

**Note:** This object detection model was not built specifcially for the environment the MAS is in (the sea), or the cameras (Wide angle, with Infra Red at night). As such, you may see some entertaining / bizzare classifications for objects. Please be assured that this is absolutely fine!
{: .notice--warning}



## Future planned feeds

As we get closer to departure, more feeds will come online to monitor the status of the MAS, it's sensors and experiments. Once they are brought online, they will be documented in the sections above.

**Planned feeds**

| topic | description |
|-------|-------------|
| science_pod | Status and alerts from the onboard science pod |
| hazard_detection | Classifications from the experimental hazard detection system |
| hydrophone | Classifications from the Marine Mammals Experiment |
| sensors | sensor data from the 'wet' sensors mounted on the MAS |
| microphone | Classifications from the internal microphone onboard the MAS |


---
