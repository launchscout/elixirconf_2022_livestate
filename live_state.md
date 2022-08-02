---
marp: true
---

![bg](./slide-welcome.jpg)

---
![bg right contain](./matched-edge.png)
# Embedded Apps with LiveState
### Chris Nelson
LaunchScout

---

# Who dis?

---

# What's an Embedded Application?
* Adds functionality to a larger application
* Examples
  * Live support
  * Buy button
  * ??

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
* LiveView

---

# Is there a name for this pattern?
* Event/State Reducers?
* Event Driven Thingamajig?
* Action Oriented Stuff?
* You know, the thing where you have functions that take event and current state and return a new state

---

# The new-ish idea
* Taking this pattern multi-tier
* No more request/reponse
* Dispatch events, receive state
  * and possibly other events

---

# Hot take! 
## It isn't javascript that makes client side development terrible
## It's the complexity on managing requests, responses, and state

---

# What if all your javascript did was:
* dispatch events
* render the current state

---

# Let's build a thing you guys!

---

# Future possible things
* Other clients
  * Swift/iOS
  * Kotlin/Android
  * React
* Connecting to FAAS

---
