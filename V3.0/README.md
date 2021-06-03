### TYPES USED BY THE ORBIT FILE FORMAT:
```C++
enum class MaterialFlag
{
	F_HAS_COLOR_MAP = (1 << 0),
	F_HAS_NORMAL_MAP = (1 << 1),
	F_HAS_OCCLUSION_MAP = (1 << 2),
	F_HAS_ROUGHNESS_MAP = (1 << 3)
};

type Generic<ByteWidth>:
	generic data with sizeof(Generic<ByteWidth>) = ByteWidth

type ByteString: 
	1 byte [uint8_t]: strlen
	strlen bytes [char]: str

type List<size>(Type):
	A list of size instances of Type
	List bytes: size * sizeof(Type) bytes
	
type Map<size>(TypeA, TypeB):
	A list of size pairs of type <TypeA, TypeB>
	Map bytes: size * (sizeof(TypeA) + sizeof(TypeB))
	
type Time = uint32_t

struct Version:
	2 bytes [uint16_t]: major
	2 bytes [uint16_t]: revision
	1 byte  [uint8_t]: build_type ['R'=Release, 'D'=Debug]
	1 byte  [uint8_t]: build
	2 bytes [uint16_t]: unused

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
	_ bytes [ByteString]: colorMapId (only if the flag F_HAS_COLOR_MAP is set)
	_ bytes [ByteString]: normalMapId (only if the flag F_HAS_NORMAL_MAP is set)
	_ bytes [ByteString]: rougnessMapId (only if the flag F_HAS_ROUGHNESS_MAP is set)
	_ bytes [ByteString]: occlusionMapId (only if the flag F_HAS_OCCLUSION_MAP is set)

struct Texture:
	_ bytes [ByteString]: name
	4 bytes [uint32_t]: data size
	_ bytes [Raw]: texture data
	
struct SubMesh:
	4 bytes [uint32_t]: vertexStart
	4 bytes [uint32_t]: vertexCount
	4 bytes [uint32_t]: indexStart
	4 bytes [uint32_t]: indexCount
	_ bytes [ByteString]: materialName

struct Geometry:
	_ bytes [ByteString]: name
	4 bytes [uint32_t]: numVertices
	4 bytes [uint32_t]: compressedVertexSize
	1 bytes [uint8_t]: vertexByteStride
	_ bytes [List<numVertices>(Generic<vertexByteStride>)]: vertices // zip compressed
	4 bytes [uint32_t]: numIndices
	4 bytes [uint32_t]: compresseIndexSize
	_ bytes [List<numIndices>(uint16_t)]: indices // zip compressed
	4 bytes [uint32_t]: numSubmeshes
	_ bytes [List<numSubmeshes>(SubMesh)]: submeshes
	
// BEGIN OF THE FORMAT SPECIFICATION:
// HEADER
3 bytes [typeless]: "OFF"
8 bytes [Version]: version
2 bytes [uint16_t]: numLights
2 bytes [uint16_t]: numMaterials
2 bytes [uint16_t]: numTextures
2 bytes [uint16_t]: numGeometries
2 bytes [uint16_t]: numAnimations
// END OF HEADER
// LIGHTS
numLights * 64 bytes [List<numLights>(Light)]: lights
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