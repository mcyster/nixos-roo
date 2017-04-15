# Nixos Config for Roo

This is the `/etc/nixos` directory on my roo laptop, an aging Macbook Pro.

The configuration is minimal, most of the custom installation and configuration is done under my user using `nix-env` <http://nixos.org/releases/nix/nix-1.7/manual/#sec-nix-env> or `nix-shell` <http://nixos.org/releases/nix/nix-1.7/manual/#sec-nix-shell>

Making changes:
..* change configuration 
..* `nix-rebuild switch`
..* check changes, reboot if needed
..* `git add -A; git commit -m 'descriptive message'; git push origin master`

## Other user configs

<https://github.com/mbbx6spp/mbp-nixos> - on a macbook pro, also has notes on temperature control and brightness

<https://github.com/polynomial/cattle/blob/master/nixos/configuration.nix> - uses trusted binary caches etc

<https://github.com/fooblahblah/nixos/blob/master/configuration.nix> - lots of stuff

# Installation Notes

See <https://nixos.org/nixos/manual/index.html#sec-installation>

Booted from a minimal Nixos image (17.03) on a USB stick

Wireless network did not come up. `ifconfig` showed the wireless device but no ip.

To get wifi working:
```
systemctl stop wap_supplicant 

wpa_passphrase NAME PASS >>/etc/wpa_supplicant.conf 
wpa_supplicant -B -i wlp2s0b1 -c /etc/wpa_supplicant.conf   # wlp2s0b1 from ifconfig 

cd /nix/store/*dhcp* 
./bin/dhclient wlp2s0b1 

# wait

ip link show
```

Installation:
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

