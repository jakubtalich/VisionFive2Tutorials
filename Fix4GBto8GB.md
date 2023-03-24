# Fixing Device Trees Limit of 4GB of RAM on 8GB devices

Source: https://github.com/starfive-tech/VisionFive2/issues/20#issuecomment-1374907916

01. Install `# apt install device-tree-compiler`.

02. Decompile your source dtb into `/tmp` (on a booted-up system):
```
cd /tmp; dtc -I dtb -O dts /sys/firmware/fdt -o vf2.dts
```

03. Edit `vf2.dts` (e.g. with `nano vf2.dts`:
- Find section beginning with `memory@4`.
- Your line of interest is `reg = <0x00 0x40000000 0x01 0x00>;` or `reg = <0x00 0x40000000 0x1 0x00>;`.
- It needs to be replaced with `reg = <0x00 0x40000000 0x02 0x00>;`. Notice `0x02` and not `0x01`.

04. Compile the file back: `dtc -I dts -O dtb -o /boot/dtbs/starfive/vf2.dtb vf2.dts`
- My location of the `.dts` files was in `/boot/dtb-5.15.0-vf2-104+/starfive/jh7110-visionfive-v2.dtb` (on a custom Debian image), yours maybe also be different from the paths mentioned here.
- (So in my case I ran: `# dtc -I dts -O dtb -o /boot/dtb-5.15.0-vf2-104+/starfive/jh7110-visionfive-v2.dtb vf2.dts`)

05. Reboot and check the available memory with `free -m`.
