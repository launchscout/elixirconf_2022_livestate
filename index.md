---
marp: true
style: |

  section h1 {
    color: #6042BC;
  }

  section code {
    background-color: #e0e0ff;
  }

  footer {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 100px;
  }

  footer img {
    position: absolute;
    width: 120px;
    right: 20px;
    top: 0;

  }
  section #title-slide-logo {
    margin-left: -60px;
  }
---

![bg](./slide-welcome.jpg)

---

![bg right contain](./matched-edge.png)
# Beyond LiveView
### Chris Nelson
![h:200](full-color.png#title-slide-logo)

---

# The two scenarios:
- We need to extend our LiveView app with some Javascript
- We are building an app that isn't served by Elixir

---

# We got a long way to go and a short time to get there...

---

# Scenario the first

---

# What are the options?
- Hooks
- JS Commands
- Both are pretty low level and imperative

---

# A better way
- Custom HTML Elements
- A better unit of abstraction
- Data in, actions out

---

# How to do?
- Data passed in via attributes
- Might need to serialize em tho
- Actions are Custom Events
- But LiveView doesn't know about Custom Events
- Guess we'll need a hook afer all... :( :(

---

# Ugh that sounds like work

---

# LiveElements
### Magically turn Custom Elements into LiveView functional components
- Declaratively using `custom_element` macro
  - Or "magically" with custom elements manifest file
- Handle custom events with `handle_event` callbacks
- Serializes complex attributes as JSON

---

# Example: Airports on map

---

## Two custom elements
- `<lit-google-map>` and `<lit-google-map-marker>`
- Only `<lit-google-map>` needs to be a Liveview component
```html
<div class="map">
  <.lit_google_map id="the_map" api-key={@api_key} version="3.46">
    <%= for %{geo_location: %Geo.Point{coordinates: {lng, lat}}, name: name, ident: ident} <- @airports do %>
      <lit-google-map-marker slot="markers" latitude={lat} longitude={lng}>
        <p><%= name %> (<%= ident %>)</p>
      </lit-google-map-marker>
    <% end %>
  </.lit_google_map>
</div>
```

---
## The view
```elixir
  custom_element :lit_google_map, events: [:bounds_changed, :tilesloaded]

  @impl true
  def mount(_params, _session, socket) do
    {:ok, assign(socket, airports: [], api_key: System.get_env("API_KEY"))}
  end

  @impl true
  def handle_event("bounds_changed", event_payload, socket), do: load_airports(event_payload, socket)

  @impl true
  def handle_event("tilesloaded", event_payload, socket), do: load_airports(event_payload, socket)

  defp load_airports(%{"north" => north, "east" => east, "west" => west, "south" => south}, socket) do
    airports =
      Airports.list_airports_in_bounds(%{north: north, east: east, west: west, south: south})

    {:noreply, socket |> assign(airports: airports)}
  end

```
---

# Extending the reach of Elixir

---

# Two questions

---

# What percentage of websites are served by Elixir?

---

# What percentage of websites use HTML?

---

# Let's take this show on the road..
- What if we wanted to drop our map on arbitrary wobsites?
- What if they weren't served by Elixir at all?

---

# LiveState
### a LiveView-like(tm) developer experience for non Elixir hosted app
- `phx-live-state`: front end npm library
- `live_state`: hex package

---

# Seeing is believing...

---

### `<airport-map>`
```ts
@customElement('airport-map')
@liveState({
  topic: 'airport_map',
  url: 'ws://localhost:4000/socket',
  properties: ['airports'],
  events: {
    send: ['tilesloaded', 'bounds_changed']
  }
})
export class AirportMapElement extends LitElement {
  
  @property({attribute: 'api-key'})
  apiKey: string = '';

  @state()
  airports: Array<Airport> = [];

```

---

## Rendering airports
```ts
  render() {
    return html`
      <div class="map">
        <lit-google-map api-key=${this.apiKey} version="3.46">
          ${this.airports?.map(({name, identifier, latitude, longitude}) => html`
            <lit-google-map-marker slot="markers" latitude=${latitude} longitude=${longitude}>
              <p>${name} (${identifier})</p>
            </lit-google-map-marker>        
          `)}
        </lit-google-map>
      </div>    
    `;
  }

```
---

### `airport_map_channel.ex`
```elixir
defmodule LiveElementsLabsWeb.AirportMapChannel do
  use LiveState.Channel, web_module: LiveElementsLabsWeb

  alias LiveElementsLabs.Airports

  @impl true
  def init(_params, _session, _socket) do
    {:ok, %{api_key: System.get_env("API_KEY")} }
  end

  @impl true
  def handle_event("bounds_changed", event_payload, state), do: load_airports(event_payload, state)

  @impl true
  def handle_event("tilesloaded", event_payload, state), do: load_airports(event_payload, state)

  defp load_airports(%{"north" => north, "east" => east, "west" => west, "south" => south}, state) do
    airports =
      Airports.list_airports_in_bounds(%{north: north, east: east, west: west, south: south})

    {:noreply, state |> Map.put(:airports, airports)}
  end
end

```
---

## Usage
```html
<html>

<head>
  <title>Airport map</title>
  <script type="module" src="http://localhost:4000/assets/custom_elements.js">
  </script>
</head>

<body>
  <h1>Wut up Elixirconf</h1>
  <airport-map api-key="XXXXX"></airport-map>
</body>

</html>
```

---

# [Tada?](airport_map.html)

---

# Livestate in the wild

---

# Links:
* live_state elixir library: https://github.com/launchscout/live_state
* phx-live-state client npm: https://github.com/launchscout/live-state
* discord-element front end: https://github.com/launchscout/discord-element
* discord-element back end: https://github.com/launchscout/discord_element
* [Live comments demo](https://launchscout.github.io/test-livestate-comments/)

---
 
![h:400px](https://cdn.discordapp.com/attachments/564951236311253042/1012446048599416903/qr-chris-elixirconf-22.png)
### github.com/launchscout/elixirconf_2022_livestate

---
