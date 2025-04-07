# Home Assistant shades custom smart cards

## Requirements
- [lovelace-mushroom](https://github.com/piitaya/lovelace-mushroom)
- [lovelace-mushroom-themes](https://github.com/piitaya/lovelace-mushroom-themes)
- [stack-in-card](https://github.com/custom-cards/stack-in-card)
- [card-mod](https://github.com/thomasloven/lovelace-card-mod)

## Create those Helpers:
*Settings > Devices & Services > Helpers*

- Shade Toggle Sun `input_boolean.shade_toggle_sun`
- Shade Toggle Time `input_boolean.shade_toggle_time`
- Offset shade open
`input_number.offset_shade_open`
- Offset shade close
`input_number.offset_shade_close`
- Pick time shade open
`input_datetime.pick_time_shade_open`
- Pick time shade close
`input_datetime.pick_time_shade_close`
- Always On State `input_boolean.always_on_state` (and set it to `on`)
- All shades `cover.all_shades` (to group all shades into one entity)

## Add those Home Assistant basic integrations from: "Devices & Services"
*Settings > Devices & Services > +Add Integration*

- `Sun`
- `Time`

## Ussfull Notes:
* replace `cover.livingroomshade` with your shade entity.
* use Find & Replace All feature `Ctrl+F ` to replace all entities at once.

## Cards & Automations

<img src="https://github.com/user-attachments/assets/e9c9bd85-a3b0-4a53-8e6a-b5575269a514" style="width: 50%; min-width: 150px; margin: 5px;" />
<details>
  <summary>Shade card</summary>
  
```yaml
type: custom:stack-in-card
mode: vertical
keep:
  outer_padding: false
  margin: false
  box_shadow: false
  background: false
cards:
  - type: custom:mushroom-template-card
    card_mod:
      style:
        mushroom-state-info$: |
          .secondary {
            opacity: 0.80 !important;
            background: rgba(114, 114, 114, 0.3);
            color: white;
            border-radius: 50px;
            margin-left: 100px;
            padding: 10px 15px 10px 15px;
            position: absolute;
            top: 10px;
            right: 10px
          }
        mushroom-shape-icon$: ""
        .: |
          mushroom-shape-icon {
            --icon-size: 65px;
            display: flex;
            margin: -22px 0px 0px -22px !important;
          }
          ha-card {
            clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
          }
    primary: LivingRoom Shade
    secondary: >-
      {% if state_attr('cover.livingroomshade', 'current_position') is not none
      %}
        {{ state_attr('cover.livingroomshade', 'current_position') }}% • 
      {% endif %} {{states('cover.livingroomshade')|title }}
    icon: mdi:awning-outline
    icon_color: |-
      {% if is_state('cover.livingroomshade','closed') %}
        grey
      {% else %}
        blue
      {% endif %}
    entity: cover.livingroomshade
    double_tap_action:
      action: none
    hold_action:
      action: none
    tap_action:
      action: none
    layout: horizontal
  - type: horizontal-stack
    cards:
      - type: custom:mushroom-template-card
        card_mod:
          style: |
            ha-card {
            --spacing: 0.5em;
            }
        layout: vertical
        primary: ""
        secondary: ""
        icon: mdi:arrow-down
        entity: input_boolean.always_on_state
        icon_color: >-
          {% if state_attr('cover.livingroomshade', 'current_position') | int ==
          100 %}
              grey
          {% else %}
              green
          {% endif %}
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: call-service
          service: cover.open_cover
          target:
            entity_id: cover.livingroomshade
          data: {}
      - type: custom:mushroom-template-card
        card_mod:
          style: |
            ha-card {
            --spacing: 0.5em;
            }
        layout: vertical
        primary: ""
        secondary: ""
        icon: mdi:pause
        entity: input_boolean.always_on_state
        icon_color: |-
          {% if is_state('cover.livingroomshade','opening')%}
          red 
          {% elif is_state('cover.livingroomshade','closing')%}
          red 
          {% else %}
          grey
          {% endif %}  
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: call-service
          service: cover.stop_cover
          target:
            entity_id: cover.livingroomshade
          data: {}
      - type: custom:mushroom-template-card
        card_mod:
          style: |
            ha-card {
            --spacing: 0.5em;
            }
        layout: vertical
        primary: ""
        secondary: ""
        icon: mdi:arrow-up
        entity: input_boolean.always_on_state
        icon_color: |-
          {% if is_state('cover.livingroomshade','closed')%}
          grey            
          {% else %}
          green
          {% endif %}  
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: call-service
          service: cover.close_cover
          target:
            entity_id: cover.livingroomshade
          data: {}
  - type: custom:mushroom-cover-card
    entity: cover.livingroomshade
    secondary_info: none
    primary_info: none
    show_position_control: true
    show_tilt_position_control: false
    show_buttons_control: false
    double_tap_action:
      action: none
    hold_action:
      action: none
    tap_action:
      action: none
    fill_container: false
    icon_type: none
```
</details>

------------------------------------------------------------------

<img src="https://github.com/user-attachments/assets/e47d565c-8002-4307-ae69-b57d26612fda" style="width: 50%; min-width: 150px; margin: 5px;" />

<details>
  <summary>presets buttons </summary>

```yaml
type: conditional
conditions:
  - condition: state
    entity: input_boolean.shade_all_view
    state: "on"
card:
  square: false
  type: grid
  cards:
    - type: custom:mushroom-entity-card
      entity: input_boolean.always_on_state
      layout: vertical
      primary_info: none
      secondary_info: name
      tap_action:
        action: perform-action
        target:
          entity_id: cover.livingroomshade
        perform_action: cover.set_cover_position
        data:
          position: 50
      icon: mdi:roller-shade
      icon_color: orange
      name: All half open
    - type: custom:mushroom-entity-card
      entity: input_boolean.always_on_state
      secondary_info: name
      primary_info: none
      double_tap_action:
        action: none
      hold_action:
        action: none
      tap_action:
        action: call-service
        service: cover.set_cover_position
        target:
          entity_id: cover.livingroomshade
        data:
          position: 100
      layout: vertical
      icon_color: green
      icon: mdi:roller-shade-closed
      name: All Open
    - type: custom:mushroom-entity-card
      entity: input_boolean.always_on_state
      secondary_info: name
      primary_info: none
      double_tap_action:
        action: none
      hold_action:
        action: none
      tap_action:
        action: call-service
        service: cover.set_cover_position
        target:
          entity_id: cover.livingroomshade
        data:
          position: 0
      layout: vertical
      icon_color: red
      icon: mdi:roller-shade
      name: All Close
  columns: 3

```
</details>

------------------------------------------------------------------

<img src="https://github.com/user-attachments/assets/e6f5a188-c086-4b47-96e3-d1dc15ecdc08" style="width: 50%; min-width: 150px; margin: 5px;" />

>### You can use this setup in two ways:
>
>- either create individual automations for each shade.
>
>- or group all your shades together using Helpers and automate them as one. then use this entity `cover.all_shades` we created earlier for the UI and Automation.

<details>
  <summary>Sun Based automation UI</summary>

```yaml
type: custom:stack-in-card
mode: vertical
keep:
  outer_padding: false
  margin: false
  box_shadow: false
  background: false
cards:
  - type: custom:mushroom-title-card
    title: Sun Based
    subtitle_tap_action:
      action: none
    title_tap_action:
      action: none
    subtitle: |-
      automates shades to open at sunrise 🌅
      and close at sunset 🌇 (with optional Offset)
  - type: grid
    square: false
    cards:
      - type: custom:mushroom-entity-card
        entity: input_boolean.shade_toggle_sun
        primary_info: name
        layout: vertical
        name: Activate
        icon: mdi:power
        secondary_info: state
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: toggle
      - type: custom:mushroom-template-card
        primary: >-
          {{ ((as_timestamp(states('sensor.sun_next_rising')) + ((
          states('input_number.offset_shade_open')|float ) * 3600)) |
          timestamp_custom('%H:%M')) }}
        secondary: Sunrise
        icon: mdi:alarm-check
        entity: input_boolean.on_state
        layout: vertical
        icon_color: amber
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: none
      - type: custom:mushroom-template-card
        primary: >-
          {{ ((as_timestamp(states('sensor.sun_next_setting')) + ((
          states('input_number.offset_shade_close')|float ) * 3600)) |
          timestamp_custom('%H:%M')) }}
        secondary: Sunset
        icon: mdi:alarm-check
        entity: input_boolean.on_state
        layout: vertical
        icon_color: indigo
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: none
    columns: 3
  - type: grid
    square: false
    cards:
      - type: custom:mushroom-title-card
        title: ""
        subtitle_tap_action:
          action: none
        title_tap_action:
          action: none
      - type: custom:mushroom-number-card
        entity: input_number.offset_shade_open
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: none
        display_mode: buttons
        primary_info: none
        secondary_info: name
        layout: vertical
        icon_type: none
        name: Offset
      - type: custom:mushroom-number-card
        entity: input_number.offset_shade_close
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: none
        display_mode: buttons
        primary_info: none
        secondary_info: name
        layout: vertical
        icon_type: none
        name: Offset
    columns: 3


```
</details>

<details>
  <summary>Sun Based automation - Automation</summary>

```yaml
alias: Shade automation sun based
description: ""
triggers:
  - value_template: >-
      {{ ((as_timestamp(states('sensor.sun_next_setting')) + ((
      states('input_number.offset_shade_close')|float ) * 3600)) |
      timestamp_custom('%H:%M')) == states('sensor.time') }}
    trigger: template
    id: sunset
  - value_template: >-
      {{ ((as_timestamp(states('sensor.sun_next_rising')) + ((
      states('input_number.offset_shade_open')|float ) * 3600)) |
      timestamp_custom('%H:%M')) == states('sensor.time') }}
    trigger: template
    id: sunrise
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - sunset
        sequence:
          - data:
              position: 0
            target:
              entity_id:
                - cover.livingroomshade
            action: cover.set_cover_position
      - conditions:
          - condition: trigger
            id:
              - sunrise
        sequence:
          - data:
              position: 100
            target:
              entity_id: cover.livingroomshade
            action: cover.set_cover_position
mode: single

```
</details>

------------------------------------------------------------------

<img src="https://github.com/user-attachments/assets/d23053bb-6de9-46a6-9f40-0def4abc1089" style="width: 50%; min-width: 150px; margin: 5px;" />

>### You can use this setup too in two ways:
>
>- either create individual UI & automation for each shade.
>
>- or group all your shades together using Helpers and automate them as one. then use this entity `cover.all_shades` we created earlier for the UI and Automation.

<details>
  <summary>Time Based automation UI</summary>
  
```yaml
type: custom:stack-in-card
mode: vertical
keep:
  outer_padding: false
  margin: false
  box_shadow: false
  background: false
cards:
  - type: custom:mushroom-title-card
    title: Time Based
    subtitle_tap_action:
      action: none
    title_tap_action:
      action: none
    subtitle: automates shades to open and close according to a specific time.
  - type: grid
    square: false
    cards:
      - type: custom:mushroom-entity-card
        entity: input_boolean.shade_toggle_time
        primary_info: name
        layout: vertical
        name: Activate
        icon: mdi:power
        secondary_info: state
        double_tap_action:
          action: none
        hold_action:
          action: none
        tap_action:
          action: toggle
      - type: custom:mushroom-entity-card
        entity: input_datetime.pick_time_shade_open
        name: Open time
        primary_info: state
        secondary_info: name
        layout: vertical
        icon: mdi:alarm-check
        double_tap_action:
          action: none
        hold_action:
          action: none
        icon_color: amber
        tap_action:
          action: more-info
      - type: custom:mushroom-entity-card
        primary_info: state
        secondary_info: name
        layout: vertical
        icon: mdi:alarm-check
        double_tap_action:
          action: none
        hold_action:
          action: none
        icon_color: indigo
        name: Close time
        tap_action:
          action: more-info
        entity: input_datetime.pick_time_shade_close
    columns: 3


```
</details>

<details>
  <summary>Time Based automation - Automation</summary>

```yaml
alias: Shade automation time based
description: ""
triggers:
  - value_template: >-
      {{ (strptime(states('input_datetime.pick_time_shade_open'),
      '%H:%M:%S').strftime('%H:%M')) == states('sensor.time') }}
    enabled: true
    trigger: template
    id: open
  - value_template: >-
      {{ (strptime(states('input_datetime.pick_time_shade_close'),
      '%H:%M:%S').strftime('%H:%M')) == states('sensor.time') }}
    enabled: true
    trigger: template
    id: close
conditions:
  - condition: numeric_state
    entity_id: sensor.home_acc_wind
    above: 0
    below: 30
    enabled: true
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - open
        sequence:
          - data:
              position: 100
            target:
              entity_id: cover.livingroomshade
            action: cover.set_cover_position
      - conditions:
          - condition: trigger
            id:
              - close
        sequence:
          - data:
              position: 100
            target:
              entity_id: cover.livingroomshade
            action: cover.set_cover_position
          - data:
              position: 0
            target:
              entity_id:
                - cover.livingroomshade
            action: cover.set_cover_position
mode: single

```
</details>


------------------------------------------------------------------
> This automation ensures that only one of the sun-based or time-based automations is active at a time. It switches between them to prevent both from running together.

<details>
  <summary>Shades toggle Between sun and time</summary>

```yaml
alias: Shades toggle Between sun and time
description: ""
triggers:
  - trigger: state
    entity_id:
      - input_boolean.shade_toggle_sun
    from: "off"
    to: "on"
    id: sun on
  - trigger: state
    entity_id:
      - input_boolean.shade_toggle_sun
    from: "on"
    to: "off"
    id: sun off
  - trigger: state
    entity_id:
      - input_boolean.shade_toggle_time
    from: "off"
    to: "on"
    id: time on
  - trigger: state
    entity_id:
      - input_boolean.shade_toggle_time
    from: "on"
    to: "off"
    id: time off
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - sun on
        sequence:
          - action: automation.turn_on
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_sun_based
          - action: automation.turn_off
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_time_based
          - action: input_boolean.turn_off
            metadata: {}
            data: {}
            target:
              entity_id: input_boolean.shade_toggle_time
      - conditions:
          - condition: trigger
            id:
              - sun off
        sequence:
          - action: automation.turn_off
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_sun_based
      - conditions:
          - condition: trigger
            id:
              - time on
        sequence:
          - action: automation.turn_on
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_time_based
          - action: automation.turn_off
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_sun_based
          - action: input_boolean.turn_off
            metadata: {}
            data: {}
            target:
              entity_id:
                - input_boolean.shade_toggle_sun
      - conditions:
          - condition: trigger
            id:
              - time off
        sequence:
          - action: automation.turn_off
            metadata: {}
            data: {}
            target:
              entity_id:
                - automation.shade_automation_time_based
mode: single

```
------------------------------------------------------------------


