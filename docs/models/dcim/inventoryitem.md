# Item do Inventário

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

Inventory items represent hardware components installed within a device, such as a power supply or CPU or line card. They are intended to be used primarily for inventory purposes.

Inventory items are hierarchical in nature, such that any individual item may be designated as the parent for other items. For example, an inventory item might be created to represent a line card which houses several SFP optics, each of which exists as a child item within the device. An inventory item may also be associated with a specific component within the same device. For example, you may wish to associate a transceiver with an interface.

!!! tip
    Like most device components, inventory items can be instantiated automatically from [templates](./inventoryitemtemplate.md) assigned to the selected device type when a device is created.

## Fields

### Device

The device in which the inventory item is installed.

### Parent

The parent inventory item to which this item is assigned (optional).

### Name

The inventory item's name. Must be unique to the parent device.

### Label

An alternative physical label identifying the inventory item.

### Role

The functional [role](./inventoryitemrole.md) assigned to this inventory item.

### Manufacturer

The [manufacturer](./manufacturer.md) that produced the item.

### Part ID

The part identification or model number assigned by the manufacturer.

### Serial Number

The serial number assigned by the manufacturer.

### Asset Tag

A unique, locally-administered label used to identify hardware resources.