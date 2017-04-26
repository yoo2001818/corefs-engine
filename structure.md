# The structure of corefs

## Addressing
Corefs uses 64bit address. Because corefs has to support small blocks, it uses
upper 8 bits to address 'lower' addresses.

Corefs is intended to use as an internal storage format for applications,
such as databases. Thus, it only supports incrementing 8 byte file names.
It may be changed later, though.

## Master record
Master record should store the version information of the filesystem,
filesystem name, allocated block size, etc.

- Magic number (0x1a7ef00d)
- Filesystem version
- Configuration data (Reserved)
- Allocated block size
- Root index node position, and allocated IDs

## Superblock record
The superblock region is placed every N blocks. It stores the block allocation
status.

- Free large block count
- Free small block count
- Block bitmap
  - 0 - unallocated
  - 1-250? - allocated small block
  - 254 - allocated index record block
  - 255 - allocated large block

## Block
Block stores actual data, and it's size is 16KB.

### Large block
Large blocks store single file block.

### Small block
Small blocks store multiple small file blocks, which is smaller than 8K.
They can store up to 64 small file blocks, and their size is 256 bytes.

### Index record
Index record stores mapping information for each file. Since most files would
be lower than 16KB and wouldn't be fragmented, it uses 25 bytes per each file.
If the file is fragmented, the address will point to file pointer record
address.

- File name (8 bytes)
- Start Address (8 bytes)
- End address (8 bytes)
- Reserved (1 byte)
