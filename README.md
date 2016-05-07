# AR9285 art tool 

With this tool you can tune a few parameters in the eeprom dump from 
the AR9285 wireless chips:
* Regulatory domain
* Capabilities

Tested with the "virtual" eeprom present in the Tplink WR741ND v2.4 
router. The eeprom at this router lives in a partition called *art*

Example:  
Backup the partition under Openwrt
```
root@OpenWrt:/# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00020000 00010000 "u-boot"
mtd1: 00104200 00010000 "kernel"
mtd2: 002cbe00 00010000 "rootfs"
mtd3: 000c0000 00010000 "rootfs_data"
mtd4: 00010000 00010000 "art"
mtd5: 003d0000 00010000 "firmware"
root@OpenWrt:/# 
```

`root@OpenWrt:/# dd if=/dev/mtd4 of=/tmp/art.bin`

Transfer art.bin to your computer, and hack it with the tool

`[dani@tool ath9k_caldata]$ ./ar9285_art -i art.bin -o art-hacked.bin -r 255`


Now transfer art-hacked.bin to your router, and write it to art partition

`mtd write art-hacked.bin art`



---

    

**Note:** The art partition must be writeable. As default it isn't in Openwrt, probably
you will need to build a custom firmware with this code:

	parts[3].name = "art";
	parts[3].offset = art_offset;
	parts[3].size = TPLINK_ART_LEN;
	parts[3].mask_flags = MTD_WRITEABLE;

changed to

	parts[3].name = "art";
	parts[3].offset = art_offset;
	parts[3].size = TPLINK_ART_LEN;
	parts[3].mask_flags = 0;

at file:  
*./target/linux/ar71xx/files/drivers/mtd/tplinkpart.c*


