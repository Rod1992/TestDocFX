# Overview

The RaceEditor is a tool designed to easily create race tracks and export it to the production environment.   
It is supposed to be used on the Gamesparks preview environment either on a WebGL or a Standalone build.

# Race configuration

Once a race track is configurated in the editor. The export generates a json readable for Photon and the Unity client.
Actually the json is translated to XML on the Photon side then sent to the clients with the rest of the config (boat, challenge...). The structure of this (historical) XML file is identical (cf CourseConfig.cs in Photon code).

## Example:
BANANE COURTE 2T 946m (J70 - STAR)
```json
{
  "_id": 12,
  "description": "BANANE COURTE 2T 946m (J70 - STAR)",
  "size": 560,
  "picture": "/race_course/Banan-2-tour.png",
  "boatLengthToStartLineLengthCoefficient": 3.2,
  "beaconPositionRandomnessRadiusMeters": 4,
  "beacon": [
    {
      "id": 1,
      "position": {
        "x": 210,
        "y": 430
      },
      "type": "start",
      "radius": 1
    },
    {
      "id": 2,
      "position": {
        "x": 290,
        "y": 440
      },
      "type": "start",
      "isComity": true,
      "outline": [
        {
          "x": 0,
          "y": -5.97
        },
        {
          "x": 1.23,
          "y": -4.34
        },
        {
          "x": 1.85,
          "y": -2.32
        },
        {
          "x": 1.85,
          "y": 6.38
        },
        {
          "x": -1.85,
          "y": 6.38
        },
        {
          "x": -1.85,
          "y": -2.32
        },
        {
          "x": -1.23,
          "y": -4.34
        }
      ]
    },
    {
      "id": 3,
      "position": {
        "x": 290,
        "y": 140
      },
      "radius": 1
    },
    {
      "id": 4,
      "position": {
        "x": 255,
        "y": 124
      },
      "radius": 1
    },
    {
      "id": 5,
      "position": {
        "x": 250,
        "y": 280
      },
      "radius": 1
    },
    {
      "id": 6,
      "position": {
        "x": 275,
        "y": 280
      },
      "radius": 1
    }
  ],
  "mark": [
    {
      "id": 1,
      "name": "start1",
      "beacon": 1,
      "leave": "port"
    },
    {
      "id": 2,
      "name": "start2",
      "beacon": 2,
      "leave": "stbd"
    },
    {
      "id": 3,
      "name": "inter",
      "beacon": 3,
      "leave": "port"
    },
    {
      "id": 4,
      "name": "dogleg",
      "beacon": 4,
      "leave": "port"
    },
    {
      "id": 5,
      "name": "door1",
      "beacon": 5,
      "leave": "stbd"
    },
    {
      "id": 6,
      "name": "door2",
      "beacon": 6,
      "leave": "port"
    },
    {
      "id": 7,
      "name": "end1",
      "beacon": 1,
      "leave": "stbd"
    },
    {
      "id": 8,
      "name": "end2",
      "beacon": 2,
      "leave": "port"
    }
  ],
  "leg": [
    {
      "order": 1,
      "startmark": [
        1,
        2
      ],
      "endmark": [
        3
      ]
    },
    {
      "order": 2,
      "startmark": [
        3
      ],
      "endmark": [
        4
      ]
    },
    {
      "order": 3,
      "startmark": [
        4
      ],
      "endmark": [
        5,
        6
      ]
    },
    {
      "order": 4,
      "startmark": [
        5,
        6
      ],
      "endmark": [
        3
      ]
    },
    {
      "order": 5,
      "startmark": [
        3
      ],
      "endmark": [
        4
      ]
    },
    {
      "order": 6,
      "startmark": [
        4
      ],
      "endmark": [
        7,
        8
      ]
    }
  ]
}
```

## Main Parameters:
- **Size**: Size in meters of one side of the race limits (being a square) 
- **boatLengthToStartLineLengthCoefficient**: Ratio to control the width of the start line based on the length of the boat.
- **beaconPositionRandomnessRadiusMeters**: The beacons are positioned in a slightly random position based on this radius.
- **beacon** are all the individual buoys or comity boat
    - **id**: Identifier
    - **position**: position on the 2D plane (so x and z in unity)
    - **type**: (optional) can be _start_ if part startline
    - **isComity**: (optional) is the beacon a comity boat
    - **oultine**: (optional) in case of a comity boat, the collider outline is defined here 
    - **radius**: (optional) in case of a buoy, the circular collider is defined with this radius
- **mark** are defining all the elements of the gates in the race
    - **id**: Identifier
    - **name**: a name to easily understand which gate
    - **beacon**: the beacon it uses
    - **leave**: by which side the boat should go around. (port or starboad)
- **leg** defines the whole race connecting the marks together
    - **order**: order in the race track
    - **startmark**: array whith the mark(s) at the start of this leg
    - **endmark**: array whith the mark(s) at the end of this leg
