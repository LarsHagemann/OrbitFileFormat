### TYPES USED BY THE ORBIT FILE FORMAT:
```C++
type ByteString: 
	1 byte [uint8_t]: strlen
	strlen bytes [char]: str

type List<size>(Type):
	A list of size instances of Type
	List length: size * sizeof(Type) bytes
	
type Map<size>(TypeA, TypeB):
	A list of size pairs of type <TypeA, TypeB>
	
struct Vertex:
	12 bytes [Vector3f]: position
	12 bytes [Vector3f]: normal
	8 bytes [Vector2f]: uv
	
enum Channel[uint16_t]:
	CHANNEL_TRANSLATION
	CHANNEL_ROTATION
	CHANNEL_SCALE
	CHANNEL_VISIBILITY
	
	CHANNEL_UNKNOWN
	
struct KeyFrame:
	_ bytes [variant<float, Vector3f>]: value // depends on the channel used in the KeyFrameList

struct Light:
	16 bytes [Vector4f]: strength
	16 bytes [Vector4f]: position
	16 bytes [Vector4f]: direction
	4 bytes [float]: angle
	4 bytes [float]: falloffBegin
	4 bytes [float]: falloffEnd
	4 bytes [uint32_t]: type

struct Material:
	_ bytes [ByteString]: name
	16 bytes [Vector4f]: diffuseColor
	4 bytes [float]: roughness
	4 bytes [uint32_t]: flags
	8 bytes [typeless]: padding

struct Texture:
	1 byte [uint8_t]: type
	_ bytes [ByteString]: filename
	2 bytes [uint16_t]: numMaterialRefs
	_ bytes [List<numMaterialRefs>(ByteString)]: materialRefs

struct Geometry:
	4 bytes [int32_t]: numVertices
	4 bytes [int32_t]: compressedSize // since V1.1
	_ bytes [List<numVertices>(Vertex)]: vertices // zip compressed since V1.1
	_ bytes [ByteString]: material
	
struct Animation:
	_ bytes [ByteString]: name
	2 bytes [uint16_t]: numChannels
	4 bytes [uint32_t]: numKeyframes
	_ bytes [Map<numChannels>(Channel, List<numKeyframes>(KeyFrame))]: keyframes
	
```

---
### FILE FORMAT V 2.0
#### Changes:
- add support for animations

#### Remarks:
- depending on the channel, a KeyFrame has different strides
	- 12 bytes [Vector3f] for CHANNEL_TRANSLATION, CHANNEL_ROTATION, CHANNEL_SCALE
	- 4 bytes [float] for CHANNEL_VISIBILITY
- the channel of a keyframe is given by the first pair entry of Animation::keyframes
	thus, the nth pair in Animation::keyframes kfs consists of the pair
	kfs.first, the channel identification, see enum Channel for more information
	kfs.second, the list of values for the channel

---
### FILE FORMAT V 1.1
#### Changes:
- add a 4 byte field to the Geometry structure
- compress the vertices

---
### FILE FORMAT V 1.0

#### Changes:
- Initial version

```C++
// HEADER
18 bytes [typeless]: "ORBIT FILE FORMAT"
4 bytes [uint32_t]: version
2 bytes [uint16_t]: numLights
2 bytes [uint16_t]: numMaterials
2 bytes [uint16_t]: numTextures
2 bytes [uint16_t]: numGeometries
2 bytes [uint16_t]: numAnimations // since V2.0
// END OF HEADER
// LIGHTS
_ bytes [List<numLights>(Light)]: lights
// END OF LIGHTS
// MATERIALS
_ bytes [List<numMaterials>(Material)]: materials
// END OF MATERIALS
// TEXTURES
_ bytes [List<numTextures>(Texture)]: textures
// END OF TEXTURES
// GEOMETRIES
_ bytes [List<numGeometries>(Geometry)]: geometries
// END OF GEOMETRIES
// ANIMATIONS
_ bytes [List<numAnimations>(Animation)]: animations // since V2.0
// END OF ANIMATIONS
```