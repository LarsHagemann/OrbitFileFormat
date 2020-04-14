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
	
type Time = uint32_t
	
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
	
struct KeyFrameValue:
	_ bytes [variant<float, Vector3f>]: value // type depends on the channel used in the KeyFrameList

struct KeyFrame:
	_ bytes [List<_>[KeyFrameValues]]: values

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
	4 bytes [int32_t]: compressedSize
	_ bytes [List<numVertices>(Vertex)]: vertices // zip compressed
	_ bytes [ByteString]: material
	
struct Animation:
	_ bytes [ByteString]: name
	2 bytes [uint16_t]: numChannels
	4 bytes [uint32_t]: numKeyframes
	_ bytes [List<numChannels>[Channel]]: channels
	_ bytes [List<Pair<Time, KeyFrame>>]: keyframes
	
// BEGIN OF THE FORMAT SPECIFICATION:
// HEADER
18 bytes [typeless]: "ORBIT FILE FORMAT"
4 bytes [uint32_t]: version
2 bytes [uint16_t]: numLights
2 bytes [uint16_t]: numMaterials
2 bytes [uint16_t]: numTextures
2 bytes [uint16_t]: numGeometries
2 bytes [uint16_t]: numAnimations
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
_ bytes [List<numAnimations>(Animation)]: animations
// END OF ANIMATIONS
```