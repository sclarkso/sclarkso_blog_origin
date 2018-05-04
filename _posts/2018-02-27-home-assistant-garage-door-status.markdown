---
author: sclarkso@redhat.com
comments: true
date: 2018-02-27 00:59:47+00:00
layout: post
link:
slug: home-assistant-garage-door-status
title: Home Assistant - Garage Door Status
wordpress_id: 18
categories:
- HASS
- Tech
---

In my previous post I went over basically how to get [Home Assistant](https://hass.io) up and running.  In this post I will document one of the automations I have written.




As a precursor, many of my automations revolve around binary data from sensors.  I use Xiaomi sensors as they’re easy to get, cheap, and easy to integrate into Home Assistant).  They use the [Zigbee](https://en.wikipedia.org/wiki/Zigbee) protocol, which allows sensors to communicate back to the gateway via other sensors.  There is a single gateway that is connected to my network and each of the sensors in turn communicates with that gateway.




Integrating the Xiaomi gateway is an involved process requiring the use of Xiaomi’s android app in order to get a ‘security’ key for the device.  I followed the HASS (Home Assistant) guide here: [https://home-assistant.io/components/xiaomi_aqara/](https://home-assistant.io/components/xiaomi_aqara/).




All CLI configuration for HASS is done via YAML files, and follows much of the same formatting as Ansible.  So luckily knowledge of one helps with the other.  I have anxiety about the garage door being left open, so this was one of the first automations I worked on.  It uses a Xiaomi door sensor I have mounted on the arm of the garage door opener.  It’s basically a reed switch with a Zigbee module.  If the magnet in the small module isn’t holding the circuit in the large module closed, then an ‘open’ signal is sent to the gateway and ultimately HASS.

<figure>
  <img src="{{site.url}}/assets/images/gd.gif" alt="Sensors"/>
  <figcaption>Mounted Sensor just above quick disconnect arm</figcaption>
</figure>

When the garage door opens, I use an automation to send me an alert.  All of this code can be in the main configuration.yaml, or can be separated out into child YAML files, which get included in the configuration.yaml.  I have my system set up like this.





    - alias: Garage door open
      trigger:
        platform: state
        entity_id: binary_sensor.door_window_sensor_XXXX
        from: 'off'
        to: 'on'
        for:
          seconds: 10
      action:
      - service: shell_command.take_garage_image
      - service: notify.pushbullet
        data:
          message: "Garage Door is OPEN"
          data:
            file: /tmp/camera.jpg




Trigger section:




This is the part of the configuration that tells the automation what to ‘listen’ for.  The ‘alias’ is just a friendly name for the automation in the UI.  The ‘platform’ just signifies that this automation works with the state of a sensor, and not for instance when a button is pressed, which is ‘platform: event’.  ‘entity_id’ is HASS’s internal name for the door sensor on the garage door.  ‘from’ and ‘to’ are telling this automation in which direction to fire.  So you could have a different event if the door was opened, versus when it closed.  When the switches open, sometimes they fire back and forth open and closed, which can cause issues with automation, so I put the ‘for’ condition in, just to prevent false alerts.




Action section:




This is the part of the configuration that tells the automation what to do when the trigger condition is met.  ‘Service’ is basically any sort of automation or action you want to take.  You can send an email, or a text, or call a script.  You can also use jinja to call a service based on additional conditions.  My ‘shell_command.take_garage_image’ service uses the ‘shell_command’ component to run a shell command on the raspberry pi. 





    # Shell Commands
    shell_command:
      pb_send_image: ~/.homeassistant/python_scripts/pb_send_file.py
      take_garage_image: ffmpeg -rtsp_transport tcp -y -i rtsp://10.0.0.233:554/unicast -vframes 2 /tmp/camera.jpg




In this case that command uses FFMPEG to scrape an image from a camera located in the garage, and saves that image to /tmp/camera.jpg.  The next service then sends me that image with pushbullet.  Originally I used another service ‘shell_command.pb_send_image’ that used a python script to send me the image with pushbullet, but in a recent update of HASS, that feature was added into the pushbullet component, but it was good practice to write that script in python nonetheless.




In addition to this automation, I am using the new ‘alerts’ feature, to send me an ongoing alert if the garage door stays open (which is dismissible in the UI, if I want to dismiss the alert), and when it closes.





    garage_door:</span>
      name: 'Garage door is still OPEN {{now().strftime("%H:%M:%S %Y-%m-%d")}}'
      done_message: 'Garage door is CLOSED {{now().strftime("%H:%M:%S %Y-%m-%d")}}'
      entity_id: binary_sensor.door_window_sensor_XXXX
      state: 'on'
      repeat: 1
      can_acknowledge: True
      skip_first: True
      notifiers:
        - steven_phone




In alerts, the ‘name’ is the data that is sent in the alert.  So I get a pushbullet message after 1 minute (and every 1 minute ongoing, curtesy of the ‘repeat’ line) saying that the Garage door is still open, and a time stamp (as pushbullet is sometimes delayed).  Then when the door closes the ‘done_message’ is fired and I get an alert that the door is closed.  The ‘can_acknowledge’ line allows me to dismiss the ongoing alert in the UI.  The ‘notifiers’ section tells the alert what the alert, which is defined in the main configuration.yaml.
