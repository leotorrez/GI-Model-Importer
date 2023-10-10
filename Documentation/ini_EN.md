
<!--
V0.2.0
By lewis252310

-->
#### Preface
This is a GIMI-based ini file documentation.
This is the first version, so only the most basic syntax is covered.
Some programming language knowdelege may be required.
But don't worry, we will update it in the future, the ultimate goal is to make `ini` easy to understand.
Originally written in Chinese, then revised by a non-english native, so if you are not reading the Chinese version, you may need pay attention to the discrepancies caused by translation.

---

### Notice! This documentation is limited to GIMI and does not apply to other versions of 3dmigoto.

---

### .ini Structure brief
Since `ini` is not the focus of this article, and the relevant `ini` syntax can be found on the Internet, we will only explain how to read `ini` in short.
The following is an example, which is from a very common mod syntax.
![img](./ini_code_simple.png)
It can be simply divided into three parts: sections, parameters, and comments.
Sections and parameters are case-insensitive, but in GIMI, sections are written in uppercase camel case.

__**Section**__
The part enclosed in [] is the beginning of a **section**. A section represents a code block, and its scope includes the current line up to the line before the next section or the end of the file.

__**Properties**__
Properties are sub-items of a section. I haven't encountered standalone properties so far. An easy way to identify them is that they always have an `=` sign.

__**Comments**__
Also referred to as annotations, comments start with `;` and continue until the end of the line. Here's one thing to note: in INI files, comments can only occupy a separate line. In other words, placing a semicolon after a property or section is not allowed.
```ini
; Commenting a whole line separately is allowed
[TextureOverrideA] ; Commenting after a section or property is not allowed
hash = abcd1234 ; Note that commenting in the wrong place will result in incorrect syntax highlighting
```

That's the basic introduction to INI files. As long as you know how to distinguish sections and properties, and how to write comments, you should be good. If you're interested, you can check [Wiki](https://en.wikipedia.org/wiki/INI_file) for more information.
Lastly, it's worth mentioning that INI files are case-insensitive, but in GIMI, section names follow the PascalCase convention.

---

Now we can start to talk about the modifiers and reserved words used in GIMI's `ini` files.

### Syntax tree
> Here is the syntax tree, modifiers are in the next section

[[\*Override\*]](#override)

├ [hash](#hash)

├ [handling](#handling)

├ [drawindexed](#drawindexed)

├ [match_first_index](#match_first_index)

├ [vb0, vb1, vb2, ...](#vbx)

├ [ib](#ib)

├ [ps-t0, ps-t1, ps-t2, ps-t3, ...](#ps-tx)

├ [filter_index](#filter_index)

├ [allow_duplicate_hash](#allow_duplicate_hash)

├ [match_priority](#match_priority)

├ [match_type](#match_type)

├ [match_width](#match_width)

├ [match_height](#match_height)

├ [match_msaa](#match_msaa)

├ [match_msaa_quality](#match_msaa_quality)

├ [match_usage](#match_usage)

├ [match_bind_flags](#match_bind_flags)

├ [match_cpu_access_flags](#match_cpu_access_flags)

├ [match_misc_flags](#match_misc_flags)

├ [match_byte_width](#match_byte_width)

├ [match_stride](#match_stride)

├ [match_mips](#match_mips)

├ [match_format](#match_format)

├ [match_depth](#match_depth)

└ [match_array](#match_array)

[[Resource*]](#resource)

├ [type](#type-resourse)

├ [filename](#filename)

├ [format](#format)

├ [stride](#stride)

└ [data](#data)

[[CommandList*]](#commandlist)

[[Constants]](#constants)

[[Present]](#present)

[[Key*]](#key-section)

├ [key](#key-properties)

├ [type](#type-key)

└ [warp](#warp)

[[CustomShader*]](#customshader)

├ [topology](#topology)

├ [cull](#cull)

├ [fill](#fill)

├ [blend](#blend)

├ [alpha](#alpha)

├ [max_executions_per_frame](#max_executions_per_frame)

└ [alpha_to_coverage](#alpha_to_coverage)

> If you can't find what you need here, it might be a reserved word or a modifier.

---

### Modifiers
> Modifiers here, the syntax tree is in the previous section.

[post](#post)

[pre](#pre)

[global](#global)

[local](#local)

[persist](#persist)

[ref | reference](#ref)

[copy](#copy)

---

### Reserved words
> Reserved words.

[time](#time)

[if, endif, else if, else](#condition)

[run](#run)

---

### Rules
> Here are some general rules.

[Variable](#variable)

[Condition](#condition)

[ERROR msg](#error-msg)

[Operators](#operators)

---

## Override

There are two types of overrides: `TextureOverride` and `ShaderOverride`. When there is a corresponding hashed object on the screen, it triggers the operation of the respective override section. This is the core functionality of GIMI and the starting point for all mods.

```ini
[*Override*]
[TextureOverrideLumineBody]
[ShaderOverridGroundHealthBar]
```
#### hash

It tells GIMI which object to pay attention to and triggers the corresponding action when found.
```ini
[TextureOverrideLumineBody]
hash = afd36b46
```

#### handling

It specifies the rendering operation for the designated object, usually using `skip` to bypass rendering.
```ini
[TextureOverrideLuminePantsu]
handling = skip
```

#### drawindexed

Tell GIMI to perform our own rendering instead of using the game's rendering. It is usually used in conjunction with [handling](#handling).
```ini
[TextureOverrideLumineBody]
drawindexed = auto
```

#### draw

Assigns x vertex in memory space to be drawn at y index. DirectX Documentation: https://learn.microsoft.com/en-us/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-draw
```ini
[TextureOverrideLumineBlend]
draw = x, y
```

#### match_first_index

Specifies the starting position of the buffer. Sometimes, a hash may contain more than one material, so it is necessary to specify the correct resource to load.
```ini
[TextureOverrideLumineBody]
match_first_index = 25600
```

#### vbx
Vertex buffer. It usually points directly to another [Resource](#resource) section.
```ini
[TextureOverrideLumineBody]
vb0 = ResourceLuminePosition
```

#### ib
Index buffer. It usually points directly to a [Resource](#resource) section that contains the IB. There can only be one index buffer per object.
```ini
[TextureOverrideLumineBody]
ib = ResourceLumineBodyIB
```

#### ps-tx
Texture resource layer. There are several different types, generally, t0 represents the texture map, t1 represents the light map, t2 represents the metal map, and t3 represents the shadow map. In 3.0 characters this convetion is broken. For more info and fix check: 
```ini
[TextureOverrideLuminePantsu]
ps-t0 = ResourceLuminePantsuDiffuse
ps-t1 = ResourceLuminePantsuLightMap
ps-t2 = ResourceLuminePantsuMetalMap
ps-t3 = ResourceLuminePantsuShadowRamp
...

```

#### filter_index

Declares a check value that allows checking elsewhere. It is likely to occupy ps-t0, so it is uncertain whether it is a good approach.
<!-- Actually it's still not very clear (for me), from someone, it seems that it can disable a specified filter. -->
```ini
[TextureOverrideLumineGlasses]
filter_index = 34
```

#### allow_duplicate_hash
At ShaderOverride.
Controls whether to allow overriding the same hash or not.

Possible values are:
 - true: Overrides when duplicates exist.
 - false: Does not override when duplicates exist.
 - overrule: Forces the override, which seems to be a plugin-level override.

```ini
[ShaderOverrideLumineQEffect]
hash = 030dbce199e10697
allow_duplicate_hash = overrule
```

#### match_priority
At TextureOverride.
Declares the order priority for overrides. The higher the value, the higher the priority.
It is not commonly used in GIMI, except in cases where it is used to eliminate hash conflicts. In those cases, setting it to 0 is sufficient.
```ini
[TextureOverrideLumineGlasses]
match_priority = 0
```

#### match_type
At TextureOverride.
Used instead of hash. Called when any component of the selected type is rendered.
```ini
[TextureOverrideTexture2D]
match_type = Texture2D
```

#### match_width
At TextureOverride.
Checks the width of the texture.
```ini
[TextureOverrideWidth1024]
match_width = 1024
```

#### match_height
At TextureOverride.
Checks the height of the texture.
```ini
[TextureOverrideHeight1024]
match_height = 1024
```

#### match_msaa
At TextureOverride.
Filter by MSAA (Not used in anime game).
```ini
[TextureOverrideMsaa]
match_msaa = 1
```

#### match_msaa_quality
At TextureOverride.
```ini
[TextureOverrideMsaaQuality]
match_msaa_quality = 1
```

#### match_usage
At TextureOverride.
This setting doesn't make much sense and defaults to DEFAULT.
More details here:
https://learn.microsoft.com/en-us/windows/win32/api/d3d11/ne-d3d11-d3d11_usage
```ini
[TextureOverrideUsage]
match_usage = IMMUTABLE
```

#### match_bind_flags
At TextureOverride.
Another filter.
You can use + or - before the flag to change the filtering.
If there is no + or - then the filter is simply not used.
```ini
[TextureOverrideAllBindFlags]
match_bind_flags = +VERTEX_BUFFER -INDEX_BUFFER CONSTANT_BUFFER SHADER_RESOURCE STREAM_OUTPUT RENDER_TARGET DEPTH_STENCIL UNORDERED_ACCESS DECODER VIDEO_ENCODER
```

#### match_cpu_access_flags
At TextureOverride.
Another filter.
You can use + or - before the flag to change the filtering.
If there is no + or - then the filter is simply not used.
```ini
[TextureOverrideAllCPUAccessFlags]
match_cpu_access_flags = +READ -WRITE
```

#### match_misc_flags
At TextureOverride.
Another filter.
You can use + or - before the flag to change the filtering.
If there is no + or - then the filter is simply not used.
```ini
[TextureOverrideAllMiscFlags]
match_misc_flags = GENERATE_MIPS SHARED TEXTURECUBE DRAWINDIRECT_ARGS BUFFER_ALLOW_RAW_VIEWS BUFFER_STRUCTURED RESOURCE_CLAMP SHARED_KEYEDMUTEX GDI_COMPATIBLE SHARED_NTHANDLE RESTRICTED_CONTENT RESTRICT_SHARED_RESOURCE RESTRICT_SHARED_RESOURCE_DRIVER GUARDED TILE_POOL TILED
```

#### match_byte_width
At TextureOverride.
Match byte width.
```ini
[TextureOverrideByteWidth]
match_byte_width = res_width * res_height
```

#### match_stride
At TextureOverride.
Something to do with buffers.
```ini
[TextureOverrideStride]
match_stride = 40
```

#### match_mips
At TextureOverride.
```ini
[TextureOverrideMips]
match_mips = 1
```

#### match_format
At TextureOverride.
Filter by format. Useful for modifying something that doesn't have a constant hash.
List of DX formats:
https://learn.microsoft.com/en-us/windows/win32/api/dxgiformat/ne-dxgiformat-dxgi_format
```ini
[TextureOverrideFormat]
match_format = R32G32B32A32_FLOAT
```

#### match_depth
At TextureOverride.
```ini
[TextureOverrideDepth]
match_depth = 1
```

#### match_array
At TextureOverride.
```ini
[TextureOverrideArray]
match_array = 12
```

---

## Resource

Not sure if it is one of the reserved words in GIMI, but since sections with resources are usually prefixed with `Resource` let's consider it a reserved word. This makes it easier to identify and avoids confusion with other sections that have specific purposes. These sections are typically used to store various resource locations.
```ini
[Resource*]
[ResourceLuminePantsu]
[ResourceMyRGBWeapon]
```

#### type (Resourse)

This is a properties under [Resource](#resource), not under [Key](#key-section).  
Used to indicate the type of resource  
There are 15 types in 3DMigoto (But in reality there are 7)  
7 main ones:  
Buffer  
StructuredBuffer  
ByteAddressBuffer  
Texture1D  
Texture2D  
Texture3D  
TextureCube  
  
rest:  
AppendStructuredBuffer  
ConsumeStructuredBuffer  
RWBuffer  
RWStructuredBuffer  
RWByteAddressBuffer  
RWTexture1D  
RWTexture2D  
RWTexture3D  
  
```ini
[ResourceLuminePantsuPosition]
type = Buffer
``` 

#### filename

Only appears under [Resource](#resource). It uses a relative path to point to the storage location of the resource. It is unclear whether absolute paths are supported, but using absolute paths in this context of redistributable data wouldn't make much sense.
```ini
[ResourceLumineBodyDiffuse]
filename = .\LumineParts\LumineBodyDiffuse.dds
```

#### format

Used to specify a specific resource format  
List of formats:  
https://learn.microsoft.com/en-gb/windows/win32/api/dxgiformat/ne-dxgiformat-dxgi_format  
```ini
[ResourceLumineBodyIB]
format = DXGI_FORMAT_R32_UINT
```

#### stride
Used for VB (Vertex Buffer) resources, specifies the byte size of a single vertex's total data.
```ini
[ResourceLuminePantsu]
stride = 20
```

#### data
Used for Buffer resources.  
```ini
[ResourceFloat]
type = Buffer
format = R32_FLOAT
data = 1 2 3 4
```

data can convert the first 128 ASCII characters into a buffer (R8_UINT format only)  
```ini
[ResourceString]
type = Buffer
format = R8_UINT
data = "Jsut a string."
```
You will get a buffer with these values:  
74 115 117 116 32 97 32 115 116 114 105 110 103 46  
---

## CommandList

Not certain if `CommandList` is one of the reserved words, but theoretically, it is just a section where multiple properties are grouped together, and it is only called when needed. However, its functionality is similar to a function, so I believe it is better to consider it a reserved word.
```ini
[CommandList*]
[CommandListToggleLogic]
[CommandListCallTheAmbulance]
```
Since the `CommandList` section is all about advanced operations, there are no fixed properties. The only elements that might be repeated are various [Variables](#variable) and [Conditions](#condition).

---

## Constants

The initialization location for global variables is under the `Constants` section. All global variables need to be declared there for GIMI to recognize them. The initialization only occurs once when the `ini` file is read. For more information about variables, please refer to the [Variables section](#variable).
```ini
[Constants]
global $active = 0
```

---

## Present

This section is executed at the beginning of each frame and is intended for code that needs to be continuously executed. It is commonly used for real-time calculations, such as switching appearances based on key inputs or various interactive effects.
```ini
[Present]
post $active = 0
```
Similar to [CommandList](#commandlist), is an area for various operations. so it does not have fixed properties. However, it is generally not directly linked to [Resource](#resource) sections. Instead, it is typically linked to a CommandList, which then calls the Resource.


---

## Key (section)

Defines which keypress conditions GIMI should detect.
After pressing it, the set operation will be performed.
The following example defines a Q key and [type](#type-key) is `toggle`
```ini
[KeyHelp]
key = q
type = toggle
```

#### key (properties)
At [Key](#key-section) section, section to specify which key to listen for.
All available key types can be found [here](https://learn.microsoft.com/zh-tw/windows/win32/inputdev/virtual-key-codes?redirectedfrom=MSDN).
```
[KeyHelp]
key = q
```

#### type (Key)

This is a properties under [Key](#key-section), not under [Resourse](#resource).
It declares the type of the key. There are four available values: default, cycle, hold, and toggle.
1. default: Executes the pre-configured operation. This is the default type when no "type" is specified, and it simply runs the configured settings.
2. cycle: Cycles through the written list in order. Whether the cycle is allowed to wrap around (connect the first and last elements) is controlled by [warp](#warp).
3. hold: Triggered continuously when the key is held down. This can be used for skills or actions that require holding down a key.
4. toggle: Toggles between two states. If the previous trigger was 1, the next trigger will be 0.

You can find more information about keys in `d3dx.ini` of GIMI.

```ini
[KeyK]
key = k
type = cycle
$swapvar = 0, 1, 2, 3
```

#### warp
Controls whether the key-cycle type allows wrapping around (connecting the first and last elements).
The default value is True.
```ini
[KeyK]
key = k
warp = false
type = cycle
$swapvar = 0, 1, 2, 3
```

## CustomShader (section)
Running your own shader.
```ini
[CustomShaderWOW]
handling = skip
drawindexed = auto
```

#### topology
https://learn.microsoft.com/en-us/windows/win32/direct3d11/d3d11-primitive-topology  
Change object rendering type.  
Values:  
point_list  
line_list  
line_strip  
triangle_list  
triangle_strip  
line_list_adj  
line_strip_adj  
triangle_list_adj  
triangle_strip_adj  
1_control_point_patch_list  
2_control_point_patch_list  
3_control_point_patch_list  
4_control_point_patch_list  
5_control_point_patch_list  
6_control_point_patch_list  
7_control_point_patch_list  
8_control_point_patch_list  
9_control_point_patch_list  
10_control_point_patch_list  
11_control_point_patch_list  
12_control_point_patch_list  
13_control_point_patch_list  
14_control_point_patch_list  
15_control_point_patch_list  
16_control_point_patch_list  
17_control_point_patch_list  
18_control_point_patch_list  
19_control_point_patch_list  
20_control_point_patch_list  
21_control_point_patch_list  
22_control_point_patch_list  
23_control_point_patch_list  
24_control_point_patch_list  
25_control_point_patch_list  
26_control_point_patch_list  
27_control_point_patch_list  
28_control_point_patch_list  
29_control_point_patch_list  
30_control_point_patch_list  
31_control_point_patch_list  
32_control_point_patch_list  
```ini
[CustomShaderTopology]
topology = point_list
handling = skip
drawindexed = auto
```

#### cull
https://learn.microsoft.com/en-us/windows/win32/api/d3d11/ne-d3d11-d3d11_cull_mode  
Indicates triangles facing a particular direction are not drawn.  
Values:  
none  
front  
back  
```ini
[CustomShaderCull]
cull = none
handling = skip
drawindexed = auto
```

#### fill
https://learn.microsoft.com/en-us/windows/win32/api/d3d11/ne-d3d11-d3d11_fill_mode  
Determines the fill mode to use when rendering triangles.
Values:  
wireframe    
solid    
```ini
[CustomShaderFill]
fill = solid
handling = skip
drawindexed = auto
```

#### blend
https://learn.microsoft.com/en-us/windows/win32/api/d3d11/ne-d3d11-d3d11_blend  
Blend factors, which modulate values for the pixel shader and render target.  
2 applications  
1. blend = disable  
2. blend = BlendOp SrcBlend DestBlend
Where SrcBlend and DestBlend can have values:  
zero  
one  
src_color  
inv_src_color  
src_alpha  
inv_src_alpha  
dest_alpha  
inv_dest_alpha  
dest_color  
inv_dest_color  
src_alpha_sat  
blend_factor  
inv_blend_factor  
src1_color  
inv_src1_color  
src1_alpha  
inv_src1_alpha  

BlendOp Values:  
add  
subtract  
rev_subtract  
min  
max  

also blend is blend[0]-blend[7]

```ini
[CustomShaderBlend]
blend[0] = add src_alpha inv_src_alpha
handling = skip
drawindexed = auto
```

#### alpha 
alpha = BlendOpAlpha SrcBlendAlpha DestBlendAlpha

Where SrcBlendAlpha and DestBlendAlpha can have values:  
zero  
one  
src_color  
inv_src_color  
src_alpha  
inv_src_alpha  
dest_alpha  
inv_dest_alpha  
dest_color  
inv_dest_color  
src_alpha_sat  
blend_factor  
inv_blend_factor  
src1_color  
inv_src1_color  
src1_alpha  
inv_src1_alpha  

BlendOpAlpha Values:  
add  
subtract  
rev_subtract  
min  
max  

also alpha is alpha[0]-alpha[7]

```ini
[CustomShaderAlpha]
alpha[0] = add src_alpha inv_src_alpha
handling = skip
drawindexed = auto
```

#### max_executions_per_frame
max_executions_per_frame to limit this to the first time the reflection  
```ini
[CustomShaderMEPF]
max_executions_per_frame = 1
handling = skip
drawindexed = auto
```

#### alpha_to_coverage
Alpha-to-coverage is a multisampling technique that is most useful for situations such as dense foliage where there are several overlapping polygons that use alpha transparency to define edges within the surface.
```ini
[CustomShaderATC]
alpha_to_coverage = 0
handling = skip
drawindexed = auto
```


---

## Modifiers

#### post

Specifies that the corresponding parameter is computed at the ***beginning of a frame***, such as setting the start time of a frame.
```ini
post $triggerDate = time
```

#### pre

Specifies that the corresponding parameter is computed at the ***end of a frame***, such as calculating the number of times [Present](#present) has been executed.
```ini
pre $auxTime = $auxTime + 1
```

#### global

The necessary modifier when declaring a **global** variable. [Variable rules can be found here](#variable).
Also, note that global variables are only declared within the [Constants](#constants) section.

```ini
[Constants]
global $a_global_var = 1
```

#### local

The necessary modifier when declaring a **local** variable. [Variable rules can be found here](#variable).
Local variables can be declared anywhere needed for calculations. However, it is uncertain how GIMI handles the recycling mechanism for local variables. At least for now, local variables are not seen frequently.

```ini
[AnySection]
local $i = 0
```

#### persist

The necessary modifier when declaring a **persistent** variable. [Variable rules can be found here](#variable).
This modifier is only used for global variables. Once declared, the variable will persist and be stored in `d3dx_user.ini`. It will only be reset when you use `Ctrl + Alt + F10`.
```ini
[Constants]
global persist $a_persist_var = 1
```

#### ref

ref or reference are used as pointers to a resource. It's up to the programmer to take advantage of this powerful tool
```ini
pre ResourceHelp = ref ResourceHelpFull
pre ResourceHelp = reference ResourceHelpFull
```
#### copy

It copies the resource into the new one. Very helpful to keep a copy of a resource before it gets modified by another shader or draw call.
```ini
pre ResourceHelp = copy ResourceHelpFull
```

#### run

Declares the section to be executed.
Commonly used to refer to a [CommandList](#commandlist) for further computations.
```ini
[KeyChangeColor]
run = CommandListLumineChangePanTsuColor
```

---

#### Variable

In GIMI, variables are identified by starting with the `$` symbol.
And if there is no `$` symbol in some positions that should be variables, that is a [reserved word](#reserved-words)
```ini
$last_date = time
; $last_date is our defined variable, while time is a reserved word in GIMI.
```
Note: the variable `time` saves a float with the time in seconds since the game launched.
#### Condition

GIMI has condition control reserved words, including `if`, `else if`, `else`, and `endif`.
The condition block starts with `if` and ends with `endif`. Nesting is supported.
If you are new to programming, it is recommended to familiarize yourself with condition control syntax in other programming languages. This explanation will not delve into it extensively.

```ini
if time == $lest_date + 10.0
    run = CommandListA
else if time == $lest_date + 15.0
    run = CommandListB
else
    run = CommandListC
endif
```

#### ERROR msg

Certain compilation errors, specially in shaders, will be shown in screen. When it comes to ini files it will just show warnings that hint possible mod conflicts. Good to troubleshoot broken mods.

#### Arimethical Operators

This is just a list of operators allowed in GIMI and does not include any usage tutorials.

| Operators | Name           | Note                            |
| --------- | -------------- | ------------------------------- |
| +         | Addition       |                                 |
| -         | Subtraction    |                                 |
| *         | Multiplication |                                 |
| /         | Division       |                                 |
| //        | Division Floor |                                 |
| %         | Modulus        |                                 |
| =         | Assignment     |                                 |
| ==        | Equal          |                                 |
| !=        | Not equal      |                                 |
| !==       | Not equal      | Similar to `!=` but more strict |

#### Logical Operators

This is just a list of operators allowed in GIMI and does not include any usage tutorials.

| Operators | Name           | Note                      |
| --------- | -------------- | ------------------------- |
| &&        | AND            |                           |
| \|\|      | OR             |                           |
| ( )       | Parenthesis    |                           |
