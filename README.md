# Nixos Config for Roo

This is the /etc/nixos directory on my roo laptop, an aging Macbook Pro.


# Installation Notes

Booted from a minimal Nixos image (17.03) on a USB stick

Wireless network did not come up. `ifconfig` showed the wireless device but no ip.

I got wifi working by:

```
systemctl stp wap_supplicant 

wpa_passphrase NAME PASS >>/etc/wpa_supplicant.conf 
wpa_supplicant -B -i wlp2s0b1 -c /etc/wpa_supplicant.conf   # wlp2s0b1 from ifconfig 

cd /nix/store/*dhcp* 
./bin/dhclient wlp2s0b1 

# wait

ip link show
```

Installation after that was straight forward
```
gdisk -l /dev/sda

mkswap -L swap /dev/sda3
mkfs.ext4 -L nixos /dev/sda2
mkfs.vfat /dev/sda1

mount /dev/disk/by-label/nixos/mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/disk/by-label/swap

mixos-generate-config --root /mnt

cd /mnt/nixos
# curl https://raw.githubusercontent.com/mcyster/nixos-roo/master/configuration.nix >configuration.nix
vi configuration.nix  # edit as needed

nixos-install
```

