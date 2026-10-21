# devicetree.resource

Read-only access to the device tree handed over by the Raspberry Pi firmware, from AmigaOS under Emu68. The resource is
embedded in the Emu68 ROM: nothing to install, only the headers to copy to your SDK.

The firmware passes the tree as a Flattened Device Tree (FDT), a binary blob. It is parsed once at boot into a tree of nodes,
each with its properties. Keys and properties are plain pointers which
stay valid until reboot.

## Usage

```c
#include <proto/exec.h>
#include <proto/dos.h>
#include <proto/devicetree.h>

APTR DeviceTreeBase;

if (DeviceTreeBase = OpenResource("devicetree.resource"))
{
    APTR key = DT_OpenKey("/soc");
    APTR prop;

    if (key)
    {
        /* Walk the children of /soc */
        APTR child = NULL;

        while (child = DT_GetChild(key, child))
            Printf("%s\n", DT_GetKeyName(child));

        /* Read a property; cells are big-endian */
        if (prop = DT_FindProperty(key, "compatible"))
            Printf("compatible: %s\n", DT_GetPropValue(prop));

        DT_CloseKey(key);
    }
}
```

A resource is never closed: it is always there, in the ROM. A node name without unit address (`serial`) also matches
`serial@7e201000` in `DT_OpenKey()`.

See `Autodocs/devicetree.doc` for the full function reference.

## Build

`mkdir build && cd build && cmake .. && make`, with the m68k-amigaos gcc and `sfdc` in the path. `build/include/` gets the
prototypes, pragmas and inlines made from `sfd/devicetree_lib.sfd`, and `build/FD/` the `.fd` file.
