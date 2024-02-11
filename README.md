# Executable Loader
The loader will load the executable file into memory page by page, using a demand paging mechanism - a page will be loaded only when needed. For simplicity, the loader will only run static executables - those not linked with shared/dynamic libraries.

To run an executable file, the loader will perform the following steps:

Initialize its internal structures.
Parse the binary file - for this, you have a parser for ELF files on Linux available in the assignment skeleton. Find more details in the section describing the executable parser interface.
Execute the first instruction of the executable (entry point).
During execution, a page fault will be generated for each access to an unmapped page in memory.
Detect each access to an unmapped page and check to which segment of the executable it belongs.
If it is not found in a segment, it means an invalid memory access - the default page fault handler is executed.
If the page fault is generated in an already mapped page, then an unauthorized memory access is attempted (the segment does not have the necessary permissions) - likewise, the default page fault handler is executed.
If the page is found in a segment and has not been mapped yet, it is mapped to the corresponding address with the permissions of that segment.
Use the mmap function (Linux) to allocate virtual memory within the process.
The page must be mapped precisely to the address indicated within the segment.
