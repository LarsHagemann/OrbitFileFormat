### TYPES USED BY THE ORBIT FILE FORMAT:
```C++
type ByteString: 
	1 byte [uint8_t]: strlen
	strlen bytes [char]: str

type List<size>(Type):
	A list of size instances of Type
	List length: size * sizeof(Type) bytes

struct Vertex:
	12 bytes [Vector3f]: position
	12 bytes [Vector3f]: normal
	8 bytes [Vector2f]: uv

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
	_ bytes [List<numVertices>(Vertex)]: vertices
	_ bytes [ByteString]: material
```

---
## FILE FORMAT V 1.0

### CHANGES:
- Initial version

```C++
// HEADER
18 bytes [typeless]: "ORBIT FILE FORMAT"
2 bytes [uint16_t]: numLights
2 bytes [uint16_t]: numMaterials
2 bytes [uint16_t]: numTextures
2 bytes [uint16_t]: numGeometries
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
```