---
title: Loqed Touch Smart Lock
description: Instructions on how to integrate a Loqed Touch Smart Lock
ha_category:
  - Lock
ha_release: 0.38
ha_iot_class: Local Push
ha_codeowners:
  - '@cpolhout'
ha_domain: loqed
ha_platforms:
  - lock
ha_config_flow: true
ha_dhcp: true
ha_integration_type: integration
---

Integrate your LOQED Touch Smart Lock with Home Assistant. The lock instantly notifies Home Assistant of a lock state change (no polling), and you can change the lock state yourself.

## Features
This integration supports:
* Send real-time (!) status changes of the lock (open, unlock, lock)
* Send battery status updates
* Change the lock state (open, unlock, lock). 
  * IMPORTANT: The HASS lovelace standard card has only support for unlock and lock. By leveraging the lock-service: lock.open you can connect any buttomn or action to opening the lock.
  * Only if your lock has a fixed knob on the outside of your door, you can use the “open” lock state. If you do not have this (thus you have a handle on the outside of your door that you can push down), this command will behave as if the unlock command is sent.
* Future: change certain settings on the LOQED Touch Smart Lock
* Switch the relay in the bridge (to open a shared access door or garage door)


## Prerequisites
On https://app.loqed.com/API-Config/, please follow the following steps:
* Login with your LOQED App e-mail address (you need to be admin)
* Tap “API Configuration tool”
* Tap “Add new API key”
* Choose any name and your lock, and tap “Add API key”. If you cannot add an API key, ensure that your lock is connected to the internet (the LED on your bridge should be constant green).
Back on the overview page, under the heading “API Keys”, tap the button “View / Edit” next to your newly created API key.
* Copy the contents of the field “Integration information”. This starts with “{"lock_id":"....”

NB 1: You do not need to create any webhooks (not for the web API, nor for the bridge), as the LOQED integration will take care of this.
NB 2: The API call from the LOQED lock to Home assistant is verified before the lock-status is updated and the event is generated. This is to prevent incoming calls to change the lock-status.

{% include integrations/config_flow.md %}

## Services
Please see the default lock integration for the services: https://www.home-assistant.io/integrations/lock/

## Events
For easy creation of automations this integration generates a separate event: `LOQED_status_change_*` for every state coming from the lock. Please use developer tools to check what is returned. Here is a sample response:
```
{
    "event_type": "LOQED_status_change_night_lock",
    "data": {
        "requested_state": "NIGHT_LOCK",
        "requested_state_numeric": 3,
        "mac_wifi": "c44f3357c161",
        "mac_ble": "c44f3357c163",
        "event_type": "STATE_CHANGED_NIGHT_LOCK",
        "key_local_id": 9
    },
    "origin": "LOCAL",
    "time_fired": "2022-04-19T18:53:54.187244+00:00",
    "context": {
        "id": "5b77f838dff20183e45a7e7ad8647d4f",
        "parent_id": null,
        "user_id": null
    }
}
```
The different states:


| Event | Description |
| ------------------ | --------------- |
| `LOQED_status_change_unlocking` | The lock is busy unlocking
| `LOQED_status_change_latch` | The lock has reached the static state `latch`
| `LOQED_status_change_opening` | The lock is busy unlatching=/opening the door (NB: After success the lock state will go back to unlocked)
| `LOQED_status_change_locking` | The lock is busy locking
| `LOQED_status_change_night_lock` | The lock has reached the static state `locked`

