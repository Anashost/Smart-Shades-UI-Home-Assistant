# Home Assistant shades custom smart cards

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
      entity: automation.a_on_state
      layout: vertical
      primary_info: none
      secondary_info: name
      tap_action:
        action: perform-action
        target:
          entity_id: cover.all_shades
        perform_action: cover.set_cover_position
        data:
          position: 50
      icon: mdi:roller-shade
      icon_color: orange
      name: All half open
    - type: custom:mushroom-entity-card
      entity: automation.a_on_state
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
          entity_id: cover.all_shades
        data:
          position: 100
      layout: vertical
      icon_color: green
      icon: mdi:roller-shade-closed
      name: All Open
    - type: custom:mushroom-entity-card
      entity: automation.a_on_state
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
          entity_id: cover.all_shades
        data:
          position: 0
      layout: vertical
      icon_color: red
      icon: mdi:roller-shade
      name: All Close
  columns: 3

```
</details>
