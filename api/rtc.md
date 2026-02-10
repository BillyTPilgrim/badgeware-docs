---
title: rtc
summary: Access to the Realtime Clock functions.
icon: build
publish: true
---

# Introduction

The Badgeware RTC is a realtime clock which will continue running while the unit is in sleep mode. It can be set to wake up the unit either at a specific time, or after a particular amount of time has elapsed.

# Methods

## alarm_status()
Returns True if the alarm interrupt pin has been set, False otherwise.

## clear_alarm()
Disables the alarm interrupts, clears the alarm and timer flags and unsets the alarm.

## datetime()
Gets or sets the current date and time. This is stored as a tuple of form `(year, month, day, hour, minute, second, dow)`.

### Usage
- `.datetime()` - Returns the current date and time as a tuple.
- `.datetime(dt)` - Sets the RTC time to the provided date and time.
        - `dt` - Tuple containing the new date and time.

## localtime_to_rtc()
Copies the badge's local date and time to the RTC.

## rtc_to_localtime()
Copies the RTC date and time to the badge's local date and time.

## set_alarm([hours=0, minutes=0, seconds=0])
Sets an alarm 

### Usage
- `.set_alarm(hours, minutes, seconds)` - 

## set_timer(ticks[, enable_interrupts=True])
Sets a timer to fire after a specified number of ticks.

### Usage
- `.set_timer(ticks, enable_interrupts)`
        - `ticks` - The number of ticks to elapse before setting off the timer.
        - `enable_interrupts` (Optional) - Boolean to enable or disable the interrupts for the timer.

## time_from_ntp()
Sets the time from an online NTP server. Requires an internet connection.

## timer_elapsed()
Returns True if the timer has fired, False otherwise.