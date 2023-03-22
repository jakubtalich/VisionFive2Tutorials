# Upgrading/Flashing Firmware

After purchasing a new board, it is required to flash and upgrade its firmware to be able to boot and use newer Linux/UNIX-like OSes.

**You will need:** a USB drive formatted with FAT32 and microSD card or eMMC

1. From https://drive.google.com/drive/folders/1jljq2s1w9HXlw8cST0nMS-U2DKfk1iEB download StarFive's version 55 of Debian and using balenaEtcher (https://www.balena.io/etcher) flash it to either a microSD card or eMMC.

2. Connect the VisionFive 2 board to a display (ideally via HDMI) and internet (ethX), insert the flashed media into the board and boot it up.

3. Open the Terminal application and using the command `$ ip a` find out your board's assigned IP and then power it off (e.g. using the command `# poweroff`).

4. Remove the storage media from the board and plug it back to your computer.

5. From https://github.com/starfive-tech/VisionFive2/releases download the latest release of **sdcard.img**, **u-boot-spl.bin.normal.out** and **visionfive2_fw_payload.img** (at the time of writing this tutorial the latest release is v2.10.4).

6. Using balenaEtcher flash **sdcard.img** to your plugged-in storage, then eject it (e.g. with `# eject /dev/sda`), insert it back into the board and power it up.

7. Copy **u-boot-spl.bin.normal.out** and **visionfive2_fw_payload.img** to a USB drive and plug it into the board.

8. Via SSH connect to the board from another computer under the root account (e.g. with `$ ssh root@IP` - IP from step 3). **Password for the root account is:** **`starfive`**

9. Check for `mtd` with `$ cat /proc/mtd`. It should give you 3 entries with `mtdX`.

10. Make a directory under `tmp` to mount your USB drive (e.g. with `# mkdir /tmp/usbdrive`).

11. Mount your USB drive to the directory created in step 10 (e.g. with `# mount /dev/sdXX /mnt/usbdrive`) and `cd` there.

12. `# flashcp -v u-boot-spl.bin.normal.out /dev/mtd0`

12. `# flashcp -v visionfive2_fw_payload.img /dev/mtd1`

13. Done. Power the board off (e.g. with `# poweroff`) and remove the storage and USB drive. Now you should be able to run any Linux/UNIX-like OS.
