# Dell PowerEdge T30 Hackintosh for macOS 12
This EFI folder is based on the work from https://github.com/cstrouse/Dell-PowerEdge-T30-Hackintosh
![Screen Shot 2022-05-02 at 4 45 42 PM](https://user-images.githubusercontent.com/12702149/166324904-8633ae5f-df20-44ad-96f1-4f53eae4813a.png)

## Hardware
| Part | Model |
| --- | --- |
| CPU | E3-1225 V5 3.30 GHz |
| RAM | Corsair 2x8GB non-ECC |
| BIOS | 1.8.1 (No M.2) | 
| SSD | Crucial MX200 SATA 500GB|
| dGPU | AMD FirePro W5100 (4xDP) |
| iGPU | Intel P530 |
| Wireless | Fenvi T919 |
| Ethernet | Intel I219LM2 |
| Audio | Realtek ALC899 |
## Edit config.plist
Rename either `configAMD.plist`(Using AMD W5100) or `configIntel.plist`(Using Intel P530) to `config.plist` then generate your own serial number.
## BIOS Configuration
Disable `TPM` and `Secure Boot`, set storage to `AHCI`. Keep default for other options.
## UEFI Modifications
1. Boot into OpenCore menu, run `VerifyMsrE2`. It should say `This firmware has LOCKED MSR 0xE2 Register!`
2. Exit and run `modGRUBShell`. Type the following commands: (I have validated these in v1.8.1 BIOS)

* Disable CFG Lock

```setup_var 0xAF 0x0```

* Enable Above 4GB MMIO BIOS Assignment

```setup_var 0x355 0x1```

* Set DVMT pre-alloc to 64MB

```setup_var 0x350 0x2```

* Set DVMT Total Gfx Memory to MAX

```setup_var 0x351 0x3```

3. Shut down then turn on. run `VerifyMsrE2`. It should say `This firmware has UNLOCKED MSR 0xE2 Register!`

For more details see https://github.com/dreamwhite/bios-extraction-guide/tree/master/Dell
## Sleep
Disable sleep as HD530(P530) driver is buggy.
```
sudo pmset -a standby 0
sudo pmset -a autopoweroff 0
sudo rm /var/vm/sleepimage
sudo mkdir /var/vm/sleepimage
sudo pmset -a powernap 0
```
## USB Mapping
The original USB Mapping is totally broken for some reason. I made my `USBPorts.kext` by disabling two front USB 2.0 ports. If you want to create your own, you need to install macOS 11.2.3 or below and create you own USB mapping.
