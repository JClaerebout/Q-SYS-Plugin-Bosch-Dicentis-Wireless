# Q-SYS Plugin – Bosch DCNM-Wireless

## Overview

The **Bosch DCNM-Wireless Q-SYS Plugin** enables monitoring and control of a **Bosch DCNM-Wireless Access Point (WAP)** directly from a Q-SYS system.

The plugin communicates with the Bosch WAP over its HTTP API and provides:

- system power control
- seat status monitoring
- active speaker monitoring
- waiting list monitoring
- microphone activation control per seat

It is designed for conference and discussion environments where operator visibility and direct control of Bosch wireless discussion devices is required inside Q-SYS.

---

## Features

- Login to Bosch DCNM-Wireless WAP via API
- Power on / off control of the wireless system
- Monitor WAP connection status
- Read available seat information
- Monitor active speakers
- Monitor waiting speakers
- Add waiting speakers to the active speaker list
- Remove active speakers from the active speaker list
- Per-seat microphone on/off control
- Per-seat priority indication
- Seat battery and connection status feedback
- Configurable number of seats (1–120)
- Multi-page UI for larger systems
- Automatic polling and re-login handling

---

## Plugin Information

| Property | Value |
|----------|------|
| Name | Bosch DCNM-Wireless |
| Version | 1.0.0.0 |
| Author | Jens Claerebout |
| Protocol | HTTP API |
| Transport | TCP/IP |
| Authentication | Username / Password |

---

## Configuration

### Properties

| Property | Description |
|----------|-------------|
| `#Seats` | Number of seats to display and control in the plugin (1–120) |

---

### Control Pins

#### Inputs

| Control | Type | Description |
|--------|------|-------------|
| `Power` | Toggle Button | Turns the Bosch wireless discussion system on or off |
| `RemoveActiveSpeaker` | Trigger Button (x5) | Removes a speaker from the active speaker list |
| `AddWaitingSpeaker` | Trigger Button (x10) | Adds a waiting speaker to the active speaker list |
| `SeatMicOn` | Toggle Button (per seat) | Adds or removes a seat from the speaker list by toggling microphone participation |

#### Outputs

| Control | Type | Description |
|--------|------|-------------|
| `Status` | Status Indicator | Plugin / WAP connection status |
| `Power` | Toggle Button | Feedback of current power state |
| `SpeakerName` | Text Indicator (x5) | Names of active speakers |
| `SpeakerID` | Integer Indicator (x5) | IDs of active speakers |
| `WaitName` | Text Indicator (x10) | Names of waiting speakers |
| `WaitID` | Integer Indicator (x10) | IDs of waiting speakers |
| `SeatPrioEnabled` | LED Indicator (per seat) | Shows whether priority is enabled on that seat |
| `SeatMicOn` | Toggle Button (per seat) | Indicates whether the seat microphone is active |

---

### Internal / UI Controls

These controls are used inside the plugin UI and are not exposed as user pins:

| Control | Type | Description |
|--------|------|-------------|
| `IP` | Text | WAP IP address |
| `user` | Text | Username for Bosch API login |
| `password` | Text | Password for Bosch API login |
| `SeatID` | Text Indicator (per seat) | Seat ID from the WAP |
| `SeatName` | Text Indicator (per seat) | Seat name from the WAP |
| `SeatBatteryStatus` | Text Indicator (per seat) | Battery status of each seat |
| `SeatConnectedStatus` | LED Indicator (per seat) | Indicates whether the seat is connected |
| `Message` | Text Indicator | Warning / mismatch message about configured vs available seats |

---


## UI Layout

### Page 1 – Config

Configuration page for:

- WAP IP address
- username
- password
- system power
- overall plugin status
- message / warnings

### Page 2 – Speaker List

Displays:

- active speaker list
- waiting speaker list
- remove active speaker buttons
- add waiting speaker buttons

### Pages 3+ – Seat Pages

Seat pages are created automatically based on the `#Seats` property.

- 25 seats per page
- each page shows:
  - seat ID
  - seat name
  - battery status
  - connection state
  - priority enabled indication
  - microphone on/off control

---

## Communication

The plugin communicates with the Bosch WAP using the built-in HTTP API.

### Main API Endpoints Used

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/login` | POST | Authenticate and obtain session ID |
| `/api/system/status` | GET / PUT | Read or change system power status |
| `/api/system-info` | GET | Heartbeat / connection check |
| `/api/speakers` | GET / POST | Read active speakers or add a speaker |
| `/api/speakers/{id}` | DELETE | Remove a speaker |
| `/api/waiting-list` | GET | Read waiting list |
| `/api/seats` | GET | Read seat information |

---

## Behavior

### Login

The plugin logs in automatically when:

- the script starts
- the IP address changes
- the username changes
- the password changes

On successful login:

- the plugin stores the Bosch session ID
- starts heartbeat polling
- reads power status
- reads seat list
- reads active speaker list
- reads waiting list

### Polling

The plugin continuously polls:

- system status
- seat information
- active speakers
- waiting speakers

Polling is done using Bosch API polling endpoints where supported.

### Heartbeat

A heartbeat timer checks `/api/system-info` every 10 seconds to keep the session alive and verify communication.

If heartbeat fails:

- plugin status changes
- heartbeat stops
- automatic re-login is attempted

### Seat Microphone Control

Toggling `SeatMicOn`:

- **ON** → sends a POST to add that seat to the speaker list
- **OFF** → sends a DELETE to remove that seat from the speaker list

### Active Speaker List

- Up to 5 active speakers are displayed
- Each active speaker can be removed individually

### Waiting List

- Up to 10 waiting speakers are displayed
- Each waiting speaker can be promoted to the active speaker list

### Seat Monitoring

For each configured seat, the plugin shows:

- seat ID
- seat name
- battery status
- connected / disconnected state
- priority capability
- microphone state

If a seat is disconnected:

- battery status shows `NA`
- microphone control is disabled

---

## Installation

1. Add the plugin to your Q-SYS design
2. Set the `#Seats` property to the number of seats you want to display
3. Open the Config page
4. Enter:
   - WAP IP address
   - username
   - password
5. Deploy to the Core
6. Verify the plugin status changes to **OK**

---

## Notes

- This plugin uses the Bosch DCNM-Wireless WAP HTTP API
- Valid login credentials are required
- The plugin is designed around:
  - 5 active speakers shown
  - 10 waiting speakers shown
  - up to 120 seats configurable
- If the number of seats configured in `#Seats` does not match the available seats in the WAP, a warning message is shown in the plugin

---

## Known Limitations

- Only the first 5 active speakers are displayed
- Only the first 10 waiting speakers are displayed
- The plugin depends on Bosch API availability and session stability

---

## Future Improvements

- Add support for dynamic active / waiting speakers
- Add more Bosch system information feedback
- Improve handling of edge cases during polling and login recovery

---

## License

MIT License

---

## Author

**Jens Claerebout**