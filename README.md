### Orbit File Format
This repository versions the different iterations of the Orbit File Format
used in the Orbit Game Engine
### Change Log:
#### FILE FORMAT V2.0
- add support for animations

##### Remarks:
- depending on the channel, a KeyFrame has different strides
	- 12 bytes [Vector3f] for CHANNEL_TRANSLATION, CHANNEL_ROTATION, CHANNEL_SCALE
	- 4 bytes [float] for CHANNEL_VISIBILITY
- the channel of a keyframe is given by the first pair entry of Animation::keyframes
	thus, the nth pair in Animation::keyframes kfs consists of the pair
	kfs.first, the channel identification, see enum Channel for more information
	kfs.second, the list of values for the channel

---
#### FILE FORMAT V 1.1
##### Changes:
- add a 4 byte field to the Geometry structure
- compress the vertices

---
#### FILE FORMAT V 1.0

##### Changes:
- Initial version