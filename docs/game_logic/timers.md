---
title: Timers
---

# Timers


Related Config File Sections:

* [timers:](../config/timers.md)

MPF config files include the concept of "timers" which you can use to
count towards a specific event based on time. Timers can be configured
to count up or down, at whatever interval you want, at any speed you
want. You can use events to start, stop, pause, reset, or change their
speed.

Timers post events with each "tick" which you can use to update the
display, play sounds, etc. They also post events when they complete
which you can use to stop a mode, play a show, etc.

Example uses of timers might include:

* Hurry up count down to make a shot (with variable score based on how
    much time is left).
* Timer to end a timed mode.
* A timer which ticks periodically to rotate a lit shot left or right.
* Etc.

The example config files section of the documentation contains
[examples of timers in modes](../examples/index.md).

## Displaying the value of a timer on a slide

If you want to use your timer in a slide you have to set the value to a
player variable first:

``` mpf-mc-config
##! mode: your_mode
# in your mode
timers:
  your_timer:
    start_value: 0
    end_value: 20
    control_events:
      - action: start
        event: mode_your_mode_started
variable_player:
  timer_your_timer_tick:
    your_timer_variable_times_100:
      int: device.timers.your_timer.ticks * 100
      action: set
slides:
  show_timer:
    widgets:
      - type: Text
        text: (player|your_timer_variable_times_100)
slide_player:
  mode_your_mode_started: show_timer
##! test
#! start_game
#! start_mode your_mode
#! advance_time_and_run .1
#! assert_text_on_top_slide 0
#! advance_time_and_run 1
#! assert_text_on_top_slide 100
```

In this example we update the player variable `timer_your_timer_tick`
every time the timer changes based on the tick event. The value is
multiplied by 100 (but you can also omit this or do anything
[Variable player](../config_players/variable_player.md)
supports). Afterwards, you can use the variable in your slide.

## Displaying the value of a timer on a slide in minutes and seconds

If you want to show the timer value in minutes and seconds, then you'll want to also add in the following variable_player and slide_player code:

```
variable_player:
  timer_song01_intro_timer_tick:
    song01_intro_timer_minutes:
      int: device.timers.song01_intro_timer.ticks / 60
      action: set
    song01_intro_timer_seconds:
      int: device.timers.song01_intro_timer.ticks % 60
      action: set

  timer_song01_combo_1_timer_tick:
    song01_combo_1_timer_minutes:
      int: device.timers.song01_combo_1_timer.ticks / 60
      action: set
    song01_combo_1_timer_seconds:
      int: device.timers.song01_combo_1_timer.ticks % 60
      action: set
timers:
  song01_intro_timer:
    start_value: 211
    end_value: 0
    direction: down
    tick_interval: 1s
    control_events:
      - action: start
        event: mode_song01_combos_started
slide_player:
  mode_song01_combos_started:
    song01_intro_slide:
      widgets:
        - type: text
          text: 'Time Left: (player|song01_intro_timer_minutes)'
          # min_digits: 2
          font_size: 72
          font_name: Teal_DarkBlue_72px_86lh
          bitmap_font: true
          anchor_x: left
          anchor_y: bottom
          x: 100
          y: 906
        - type: text
          text: ':'
          font_size: 72
          font_name: Teal_DarkBlue_72px_86lh
          bitmap_font: true
          anchor_x: left
          anchor_y: bottom
          x: 350
          y: 906
        - type: text
          text: '(player|song01_intro_timer_seconds)'
          min_digits: 2
          font_size: 72
          font_name: Teal_DarkBlue_72px_86lh
          bitmap_font: true
          anchor_x: left
          anchor_y: bottom
          x: 365
          y: 906
```
In this example, we're adding two variable definitions within the "variable_player" section of our config file in order to define how to calculate the minutes and seconds separately.

You'll also note that we're using three separate text widgets to display this as intended. This is due to the fact that if you add the ":" into the same text widget as either the minutes or seconds widget, the "min_digits" setting will not get displayed resulting in a timer that shows single digits without a zero in front of them as is typically done on a digital clock. 


## Related Events

* [timer_(name)_started](../events/timer_timer_started.md)
* [timer_(name)_stopped](../events/timer_timer_stopped.md)
* [timer_(name)_paused](../events/timer_timer_paused.md)
* [timer_(name)_complete](../events/timer_timer_complete.md)
* [timer_(name)_time_added](../events/timer_timer_time_added.md)
* [timer_(name)_time_subtracted](../events/timer_timer_time_subtracted.md)
* [timer_(name)_tick](../events/timer_timer_tick.md)
