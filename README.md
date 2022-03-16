# breaker-breaker
Experimental tool to track which circuit breaker each plug, fixture and switch is on 

## Components

* The project consists of a database backend and a UI. For the backend:
* **outlets** have the following properties: 
  * outletID -> UID of this component
  * breakerID -> ID of the breaker it is on
  * floorID -> ID of the floor it is located on
  * posX -> X position, relative to the origin
  * posY -> Y position, relative to the origin
  * posZ -> Z position (height), relative to the floor
* **fixtures** have the following properties:
  * fixtureID -> UID of this component
  * breakerID
  * floorID
  * posX
  * posY
  * posZ
* **switches** have the following properties
  * switchID -> UID of this component
  * breakerID
  * floorID
  * posX
  * posY
  * posZ
  * numPoles -> 2-way or 3-way switch
  * isDimmed -> on a dimmer true:false
* **switchFixtures** maps switches to fixtures
  * switchFixtureID
  * switchID
  * fixtureID
* **panels** have the following properties
  * panelID -> UID of this component
  * floorID
  * posX
  * posY
  * posZ
  * numBreakers
* **slots** have the following properties
  * panelSlotID -> UID of this record
  * panelID
  * slotNumber
* **breakers** have the following properties
  * breakerID -> UID of this component
  * currentRating -> number of Amps 
  * voltage -> 120V or 240V
  * option to extend to include things like manufacturer, model, etc.
* **breakerSlots** maps Breakers to panels
  * breakerPanelID -> UID for the record
  * breakerID
  * panelSlotID
* **rooms** with properties like 
  * roomID -> UID for this component
  * floorID -> which floor it's on
  * roomType -> bedroom, kitchen, hallway, stairwell, etc.
  * posX -> position of the origin of the room
  * posY -> position of the origin of the room
  * sizeX -> room bounding box size in X direction
  * sizeY -> room bounding box size in Y direction
  * sizeZ -> room bounding box size in Z direction

* In NoSQL terms, the schema can look something like this:
```javascript
components: [{
    id:1,
    type: "plug|switch|fixture",
    breaker: 1, //breaker id
    floor:1,
    location: {x:100, y:300, z:30}, //x, y relative to origin, z relative to floor
    numPoles:0, //only used for switches
    isDimmed:false, //only used for switches
    switchedCircuit:[] //list of id's of devices on this switch
}]
```
```javascript
breakers: [{
    id:1,
    volts:120|240,
    amps:15,
    slots:['1A', '1B']
}]
```
```javascript
panelSlots: [{
    slot:'1A',
    breaker:1
}]
```
```javascript
rooms: [{
    id:1,
    floor:1,
    type:"bedroom|bathroom|hallway|stairwell...",
    location: {x:100, y:200, z:0} //bottom-left corner relative to origin
    boundingBox: {x:240, y:200, z:108} //bounding box of room
}]
```

### Workflow

* For entering information, interface could be something like:
1. Panel Setup
  * Set number of slots in the panel -> Panels
  * Add breakers, set the volts and amps -> Slots, Breakers, BreakerSlots
  * Join slots that are paired, eg for 240 volts -> Slots, BreakerSlots

2. Position Switches, Outlets and Fixtures
  * Upload image of architectural drawing
  * Set scale and origin, and ceiling height
  * Drag & Drop outlets, switches and fixtures, using defaults for Z position -> Switches, Fixtures, Outlets
  * Draw lines connecting fixtures to switches or other fixtures -> SwitchFixtures
  * Stairwell mode where switches and fixtures span multiple floors -> SwitchFixtures
  * Note need option for split plugs for kitchen (or just drop two outlets)
  * Optionally box in rooms -> Rooms

3. Assign components to circuits
  * Select a breaker
  * Click on all components on that breaker
  * Save

* For viewing info, something like this:
  * Click on nodes to see which breaker it's on
  * Click on breaker to see which nodes are on it
  * Option to edit which components are on a breaker
  * Option to add a component to a breaker 
  * See all floors or select a floor to view it
  * [Search by room type]

## Installation

* Should be a SPA, so no application required

## Development Queue

* Environment: NPM script to post to raspberry Pi for dev
* Back-end: IndexedDB interface
* Back-end: CRUD operations for DB
* Front-end: Rendering of house
* Front-end: Panel Setup UI
* Front-end: drag&Drop components
* Front-end: Connecting switches to fixtures
* Front-end: Adding components to breakers
* Front-end: Room box drawing