---
title: "QML"
date: 2021-04-06T17:13:38+02:00
draft: false
comments: false
menu:
  docs:
    parent: "qt"
images:
---

## General concepts

### Item
Item is an abstract, invisible type, used to group objects, all QML objects inherits from it.

### Signal / slots

to declare a signal:

	signal notify(int count) //declare
	property Receiver target: null     //specify receiver here , Receiver is the type 

to connect signal to slot:

	onTargetChanged: {
			notify.connect(target.receiveInfo)
	}




### Property system

**property binding** - allows to bind properties to dynamically changing values. 
Automatically generates *onPropertyNameChanged* signal.

They can be specified in 2 ways:

    font.family
    font.pointSize

or groupped:

    font {
      family: 
      pointSize: 
    }

## Tips

### How to add an image

An Image can be added in one of 4 ways:

    Image { // by coping image to working directory
            x: 10
            y: 150
            width: 100
            height: 100
            source: "file:avatar.png"

    }

    Image {  //by the use of qrc resource file
            x: 150
            y: 150
            width: 100
            height: 100
            source: "images/avatar.png"

    }

    Image {  // by full path
            x: 300
            y: 150
            width: 100
            height: 100
            source: "file:///C:/Users/Piotr/Pictures/avatar.png"
    }

    Image {  // by URL
            x: 450
            y: 150
            width: 100
            height: 100
            source: "http://www.justfordecor.com/media/catalog/product/cache/1/image/9df78eab33525d08d6e5fb8d27136e95/k/1/k1.png"
    }

### How to add new component?

1. Create file
> Note: each component name starts with Uppercase leter!

2. Wrap with Item

3. Define properties, signales, expose properties of inner elements

> Remember to expose widht and height to be placed in columns / rows correctly!

### How to include Javascript file?

	import "name" as MyName
or 
	Qt.include("filename.js")
	
	
### How to use persistance? 

We have three methods for that:

1.Settings file

	set organization name, application info in main.cpp   
	use Settings {} object
	
2.SQL (database)
		
	import QtQuick.LocalStorage 2.0

3.Link to C++ backend storage

## Model View Delegate

They all have access to two others elements.
Simplest way is to instantiate ListView with a model as a list ["a", "b", "c"]

### Repeaters 

Similiar to ListView approach, but with broader control.
Generates a number of the same looking items based on model data.

### XmlModelList 

More sophisticated, ready only model, which main purpose is to represent XML model data.
To use it, we need to define XmlRole, which is a mapping: how to parse Xmla, and how to name that property.

To parse XML we use XPath queries. They are instructions how to read / split the whole Xml document, what types are used etc.

## Transforms / Animations / States & Transitions

### Transforms

A transform is a simple parameter (property) modification. For instance we can modify x, y, z position,
the scale or rotation based on some event.
The scale and rotation transform can use `origin` property, which tells what is the transform origin point.

### Animations 

There are 3 types of animations in QML: PropertyAnimation, RotationAnimation and NumberAnimation.
They are pretty self-explanatory. All have properties like: `running`, `from`, `to`, `duration` or `paused`.
Example of PropoertyAnimation:

	PropertyAnimation on y {
		to : 450
		duration: 2000
		running: animationRunning
		loops: Animation.Infinite
	}

When propoerty is defined without parameter, will no be started automatically:

	PropertyAnimation {
		id: standaloneAnimation
		target: ...
		property: "y"
		duration: 2000
		to: 200
	}

we need to use `standaloneAnimation.start()`

Animations can be chained together into `Sequential` and `Parallel` animations.

### States / transitions 

Every item has state and states proprties which can be levereged. 
Example to change color and show / hide objects 

	state: "summer" //initial state 

	states : [
		State {
			name: "summer"
			PropertyChanges {
				target: objectId
				color : "lightblue"
			}
			PropertyChanges {
				target: otherObjId
				opacity : 1
			}
			PropertyChanges {
				target: anotherOneObjectId
				opacity : 0
			}
		},
		State {
			name: "spring"
			PropertyChanges {
				target: objectId
				color : "darkblue"
			}
			PropertyChanges {
				target: otherObjId
				opacity : 0
			}
			PropertyChanges {
				target: anotherOneObjectId
				opacity : 1
			}
		}
	]

	transitions : [
		Transition {
			from: "summer" 
			to : "spring" 
			ColorAnimation {
				duration: 500
			}
			NumberAnimation {
				properties : "opacity"
				duration: 500
			}
		},
		
		Transition {
			from: "spring" 
			to : "summer" 
			ColorAnimation {
				duration: 500
			}
			NumberAnimation {
				properties : "opacity"
				duration: 500
			}
		}
	]

Wildcards can be used in transitions, for instance:

	transitions : Transition {
		from : "*" ; to : "*" 
		ColorAnimation {
			duration: 500
		}
		NumberAnimation {
			properties : "opacity"
			duration: 500
		}
	}

## Controls / Widgets

### Mouse Area

nice, convinient signals:

	onClicked (mouse) // gives x, y of the mouse click 
	onWheel (wheel) //gives info about wheel movements 
	hoverEnabled 
	onHoveredChanged 

possibility to specify drag:

	MouseArea {
					anchors.fill: parent
					drag.target: draggableRectId
					drag.axis: Drag.XAxis
					drag.minimumX: 0
					drag.maximumX: dragRectId.width - draggableRectId.width
	}


### Group box

If we group **radio buttons** together - they will be mutually exlusive
If we group **checkboxes** together - they will not be exclusive.

###  ComboBox 

This control has a `model` property, where we provide the list of values / texts.
If multiple attributes are present, we set the `testRole` to decide which is displayed
Combobox can be editable, which allows us adding new items in it.
To use it we need to overload `onAccepted` and add item to model there.

### BusyIndicator 

with properties: busy and visible

