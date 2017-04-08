# mssl1680-firmware
Firmware for silead mmsl1680 touchscreen.
Depends:linux>=4.9
firmware/silead
<code># mkdir /lib/firmware/silead</code>
<code># cp mssl1680.fw /lib/firmware/silead</code>
For calibration refer
<url>https://wiki.archlinux.org/index.php/Calibrating_Touchscreen#Your_screen</url>
Extracted from Windows driver "SileadTouch.sys"
Command to find the offsets used for extraction:
------------------------------------------------
    cd firmware/trekstor/surftab-twin-10.1-ST10432-8/
    export F=Windows_System32_drivers/SileadTouch.sys
    cat $F \
        | hexdump -e '1/1  "0x%8.8_ax    "' -e '1/1  "%8._ad    "' -e '8/1 "%02X ""\n"""' \
        | grep -i -E "F0 00 00 00 02 00 00 00|7C 00 00 00 .. .. .. .." \
        | grep "F0 00 00 00 02 00 00 00" -B1 \
        ; \
    echo -- \
        ; \
    cat $F \
        | hexdump -e '1/1  "0x%8.8_ax    "' -e '1/1  "%8._ad    "' -e '8/1 "%02X ""\n"""' \
        | grep -i -E "F0 00 00 00 02 00 00 00|7C 00 00 00 .. .. .. .." \
        | tail -n2


Gives this output:
------------------
    0x00009540       38208    7C 00 00 00 00 00 00 01
    0x00009548       38216    F0 00 00 00 02 00 00 00   (start fw_1)
    --
    0x0000b010       45072    7C 00 00 00 00 00 00 00   (end fw_1)
    0x0000b818       47128    F0 00 00 00 02 00 00 00   (start fw_2)
    --
    0x000152c0       86720    7C 00 00 00 30 32 3A 32   (end fw_2)
    0x00015ac8       88776    F0 00 00 00 02 00 00 00   (start fw_3)
    --
    0x0001f570      128368    7C 00 00 00 30 32 3A 32   (end fw_3)
    0x0001fd78      130424    F0 00 00 00 02 00 00 00   (start fw_4)
    --
    0x00029b38      170808    7C 00 00 00 30 32 3A 32   (end fw_4)
    0x0002b468      177256    7C 00 00 00 01 30 07 30


Extract the firmware.fw files:
------------------------------
    dd bs=1 if=$F of=firmware.fw_1 skip=38216  count=$(( 45072 -  38216 + 8))
    dd bs=1 if=$F of=firmware.fw_2 skip=47128  count=$(( 86720 -  47128 + 8))
    dd bs=1 if=$F of=firmware.fw_3 skip=88776  count=$((128368 -  88776 + 8))
    dd bs=1 if=$F of=firmware.fw_4 skip=130424 count=$((170808 - 130424 + 8))
    unset F
    cd -

| file          | comments                                  |
|---------------|-------------------------------------------|
| firmware.fw_1 | Not tested, much smaller then the others. |
| firmware.fw_2 | Reacts to input, mouse pointer jumpy.     |
| firmware.fw_3 | Reacts to input, mouse pointer jumpy.     |
| firmware.fw_4 | Works good.                               |