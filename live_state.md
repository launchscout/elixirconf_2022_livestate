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
# Embedded Apps with LiveState
### Chris Nelson
![h:200](full-color.png#title-slide-logo)

---
<!-- footer: ![](full-color.png) -->
# What's an Embedded Application?
* Adds functionality to a larger application
* Examples
  * Live support
  * Buy button
  * Comments
  * Surveys
* You could also lump in Micro Front Ends

---

# Current state of the art
* Third party javascript
* Often `iframe` based
* Proprietary, limited customization

---

# There's a better way
* Custom Elements are here
* Supported by all the browsers
* Fully stylable with CSS
* The right tool for the job!

---

# Let's render LiveView in a Custom Element then!
* It's certainly *possible*
* I even got simple examples working
* But things get *really* complicated *really* fast
* In the end, it just didn't feel right (to me)

---

# What's LiveState?
* An elixir library (live_state)
* A javascript library (phx-live-state)
  * Note: not an official Phoenix thing!
  * I just stole the prefix cuz `live-state` npm was taken

---

# The basic ideas of LiveState
* Events
* State
* Reducers: functions which take
  * event
  * current state
  * return a new state

---

# Does this seem familiar?
* `GenServer`
* Redux
* Elm
* LiveView

---

# Side rant: This pattern needs a name!!

---
# Let's call it: Event State Reducer
---

# The new-ish idea
* Taking this pattern multi-tier
* No more request/reponse
* Dispatch events, receive state
  * and possibly other events

---

# Hot take! 
## It isn't javascript that makes client side development terrible
## It's the complexity of managing requests, responses, and distributed state

---

# How it works on the Client
* Client library uses Phoenix Channels
* Sends Custom Events over channel as `lvs:event_name`
  * Custom Events are user defined DOM Events
  * `name` up to you
  * `detail` a payload of arbitrary data
* Receives `state:change` events to update local state
* Can optionally receive other events from channel and dispatch them

---

# How it works on the server
* Server use Phoenix Channels too :)
* state is maintained in a socket assign
* events are sent from client over the channel
* state changes are pushed to client over the channel
* A `LiveState.Channel` behaviour makes things easy

---

## Mostly, I'm just using Phoenix Channels :)
You might be surprised how little code there is...

---

# `live_state`

* The elixir library
* Add it to your deps
* `use LiveState.Channel` behaviour in your channel
* implement callbacks

---
## `init/3` to build initial state
* Receives:
  * channel
  * payload
  * socket
* Returns
  * `{:ok, state}`
  * state must be map of `Jason.Encoder` able things
  * pushes `state:change` to clients

---

## `handle_event/3` to handle events
* Receives
  * event name (`CustomEvent` name)
  * event payload (`CustomEvent` detail)
  * state (from socket assigns)
* Returns
  `{:noreply, new_state}`
  `{:reply, event_or_events, new_state}`
* new state is pushed to clients over channel
* Events dispatched on client

---

## `handle_message/3` to handle process messages
* Receives
  * message
  * state
* Return tuples same as `handle_event/3`
* Handy for `PubSub`!

---

# Sending the whole state every time?
* `json_patch` new in 0.5.1
* uses `json_diff` to calculate state patch
* sends a `lvs:update` event containing a json patch
* client applies patch to produce new state
* tracks a state version number to keep things in sync
  * although Phoenix Channels may make this redundant
* opt in

---

# Introducing `phx-live-state`
* javascript (typescript) npm
* increasing levels of abstraction:
  * `LiveState` - lower level API
  * `connectElement()` allows you to "wire up" a Custom Element
  * `@liveState` TS decorator lets you declaratively annotate a Custom Element class

---

# The goal:
## Your TS/JS should only have to:
* declaratively connect to LiveState
* render the current state
* dispatch events

---

## `@liveState` typescript decorator
* decorates a Custom Element class
* takes a single `object` param
  * url: (optional, defaults to `url` prop of element)
  * channelName: (optional, defaults to `channelName` prop on element)
  * properties - list of properties to set from state
  * attributes - list of attributes to set from state
  * events
    * send - Custom Events to send from this element
    * receive - Custom Events to receive and then dispatch on this element

---

# Let's make a ~~Todo List~~
Dear god no lets do something else plz

---

# Demo 2.0: Live support via discord
* `<discord-chat>` html element you can add to any web page
* Users enter their name to start a "chat with agent"
* Creates and connects to a newly created discord channel
* Messages are sent to discord from website user
* Messages are received from the "agent" in the discord channel

---

# The players
## `<discord-chat>`
## `DiscordElement.DiscordChatChannel`
## `DiscordElement.ChatBotConsumer`

---

# Codez plz

---

# Demo time!

---

# Future possible things
* Very soon:
  * ~~jsonpatch for efficient state management~~
  * authorization hook and example
* Other clients
  * Swift/iOS
  * Kotlin/Android
  * React
* Connecting to FAAS

---

# It's early times!
* APIs may still change
* It's great time to join us if you want to help them be better :)
* If you are building an embedded or micro front end app, let's talk
  * chris@launchscout.com

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
