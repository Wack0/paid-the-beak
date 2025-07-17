# paid the beak

This repo contains exploit code for `paid the beak`, a coldboot exploit for Wii U diag boot1 (sdboot1) and devkit dual/sdio boot1.

- `exploit.s` is the originally implemented exploit, which overwrites the saved secure boot state on stack and the address of `LT_EFUSEPROT` in main() constpool, this only works on diag boot1 v7 and requires the loaded ancast entrypoint to be at `0x01000300`.
- `exploit2.s` is an implementation that overwrites 0x100 bytes around the return address of the `load_firmware()` function in boot1 to `blx r0` - thus jumping to physical address zero in Thumb mode - and writes a small Thumb payload to address zero to calculate the correct entrypoint of the loaded ancast image and jumps to it.
- `exploit2dev.s` is the same as `exploit2.s` but puts a small ancast header at address 0 such that dev boot1 will execute the vulnerable code (when boot device in SEEPROM is SD3).

## Usage

Build with armips. For the non-dev implementations you need to provide a copy of diag boot1 v7 as `sdboot1.ancast`:

```
    File: sdboot1.ancast
  CRC-32: 60675212
     MD5: b4b27d7149d85e4d1b7b586af6f7b942
   SHA-1: 7ea1188a8584b621fe61e47f5d93f399e914e2a8
 SHA-256: dd8cc042032fd0d3b861bd2dd220dcc1eac03409d56e61f39ac112c5ff726c23
 SHA-512: 84ce226b6893238a69a534dce049cf6108085fde2151f8329b28469516847b028c03e1df3f87578cf6b38828446a2e42c2da31cd7576e88cdcfff45691a99cb4
```

Concatenate the image with a plaintext fw.img (`copy /b exploit2.bin + fw.img exploit2_fw.bin` or `cat exploit2.bin fw.img > exploit2_fw.bin`) and then write it to a 2GB or lower SD card using `dd` on a POSIX system or Win32DiskImager (etc) on Windows.

Insert SD card into Wii U. For the dev system (CAT-DEV), just powering on should boot your plaintext fw.img. For the retail system, you must use a battery jig (or compatible reimplementation) so boot0 will load boot1 from SD.
