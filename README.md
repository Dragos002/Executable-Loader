# Executable Loader
The loader will load the executable file into memory page by page, using a demand paging mechanism - a page will be loaded only when needed. For simplicity, the loader will only run static executables - those not linked with shared/dynamic libraries.

To run an executable file, the loader will perform the following steps:

1. Initialize its internal structures.

2. Parse the binary file - for this, you have a parser for ELF files on Linux available in the assignment skeleton. Find more details in the section describing the executable parser interface.

3. Execute the first instruction of the executable (entry point).

4. During execution, a page fault will be generated for each access to an unmapped page in memory.

5. Detect each access to an unmapped page and check to which segment of the executable it belongs.

6. If it is not found in a segment, it means an invalid memory access - the default page fault handler is executed.

7. If the page fault is generated in an already mapped page, then an unauthorized memory access is attempted (the segment does not have the necessary permissions) - likewise, the default page fault handler is executed.

8. If the page is found in a segment and has not been mapped yet, it is mapped to the corresponding address with the permissions of that segment.

9. Use the mmap function (Linux) to allocate virtual memory within the process.

10. The page must be mapped precisely to the address indicated within the segment.

# Parser interface
The parser interface provides two functions:

1. `so_parse_exec` - parses the executable and returns a structure of type `so_exec_t`. This structure can be used further to identify the segments of the executable and their attributes.

2. `so_start_exec` - prepares the program's environment and begins its execution. From this point onwards, page faults will occur for each access to a new/unmapped page.

The structures used by the interface are:

- `so_exec_t` - describes the executable's structure:
  - `base_addr` - indicates the address at which the executable should be loaded.
  - `entry` - the address of the first instruction executed by the executable.
  - `segments_no` - the number of segments in the executable.
  - `segments` - an array (of size `segments_no`) containing the executable's segments.

- `so_seg_t` - describes a segment within the executable:
  - `vaddr` - the address at which the segment should be loaded.
  - `file_size` - the size of the segment within the file.
  - `mem_size` - the size occupied by the segment in memory; the size of the segment in memory may be larger than the size in the file (e.g., for the BSS segment). In this case, the difference between the memory space and the file space must be zeroed out.
  - `offset` - the offset within the file where the segment begins.
  - `perm` - a bitmask representing the permissions that the pages in the current segment should have:
    - `PERM_R` - read permissions
    - `PERM_W` - write permissions
    - `PERM_X` - execute permissions
  - `data` - an opaque pointer that you can use to attach your own information related to the current segment (for example, you can store information about the pages in the segment already mapped here).
