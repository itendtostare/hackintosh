# Hackintosh-i7-10700K-Gigabyte-z490i-RX-5700-XT-Catalina-10.15.6
Hackintosh build with Opencore

I put together my first Hackintosh dual boot SFFPC, and although I found it simpler than I thought it would be it was not easy. I didn't see any guide or successes with my motherboard and specific build, so I put together one with tips and tricks, optimised for the hardware I used. 

Hardware
- Intel i7-10700K
- Gigabyte z490i Aorus Ultra:
 - Audio: Realtek ALC1220-VB
 - 2.5Gbit Ethernet: Intel I225-v
- RAM: 32GB Corsair Vengeance LPX 3200 DDR4
- GPU: MSI Radeon™ RX 5700 XT 50th Anniversary
- SMBIOS: iMac20,1
- BIOS version:  F5C (very important to flash this BIOS as it enables us to unlock CFG directly in the BIOS settings)
- Storage: 2x Samsung SSD 970 EVO Plus 1TB
- Case: Louqe Ghost S1

Working
- macOS Catalina10.15.6, Opencore 06.2 and Windows dual boot 
- Wifi and Bluetooth (via OpenIntelWireless community as replacing the onboard Intel WiFi-card doesn’t work. See note below)
- Audio: Realtek ALC1220-VB (AppleALC.kext, layout-id=7, device-id=0xA170, FakeID.kext, FakePCIID_Intel_HDMI_Audio.kext)
- USB, all external ports, have not mapped internal ports
- 2.5Gbit Ethernet (Intel I225-V)
- iGPU UHD630 (but no HDMI-output yet)
- Sleep/Wake
- Shutdown
- Restart
- Services (iCloud, iMessage)
- Continuity
- DRM
- Find My Mac
- Native VRAM
- Sidecar

Not working so far
- iGPU UHD630 HDMI-Output
- Airdrop
- Handoff

Notes on z490 ITX motherboard (SFFPC build)
Building in a small environment brings its set of drawbacks and choices, one of them is how to distribute your M.2. As per u/papadiche post , only the AsRock motherboards have CNVI enabled (which means you can swap the WiFi module for one that works natively on Apple products). 
In my situation, I decided to not go with those, because I wanted to be able to fit the Noctua NH-L12 Ghost S1 cooler, which is incompatible with the ASRock Z490 Phan ITX/TB3. The ASRock Z490M-ITX/ac was compatible, but has worse VRM and its less future proof if I want to size up CPU down the line. 
I also preferred using the two M.2 slots to have a dual boot install, one with Windows and one with macOS, instead of devolving one M.2 slot for native Bluetooth and WiFi connectivity. I made this decision after I found out that OpenIntelWireless community is making strides to enable the BT and WiFi with our intel wireless module on our motherboard (which works like a charm).

What I used
- Opencore 0.6.1 and Dortania Guide
- Watched  u/SchmockLord1912 guide on his YouTube channel  (it s in German)
- This USB stick, which comes super handy when moving stuff from a Mac to a PC, and also to test ports in post install
- Hackintool, Kext Updater, PLIST Editor, GenSMBIOS
- Lots of googling around!


Detailed step by step Install for this build

Installation steps
Firs of all, I plugged in only the front M.2 stick during my build, put everything together and installed Windows. I then plugged in the second M.2 to install macOS on it. I m not sure if this step is 100% required, but I have not had any issues with the two OS's, so it is reccomended.

- I downloaded Catalina from the Appstore on my MacBook, plugged in the USB stick and formatted
- I then open the Terminal and enter this command to create the installer (Replace "MyVolume" with your USB-sticks name if you named it differently)
sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
- Waited a bit for it to end and checked that the "Install macOS Catalina" was on the USB
- Mounted the EFI-partition of the USB stick using Hackintool, under 'Disks' menu, by clicking the double arrow.
- I downloaded Kext Updater and downloaded fresh copies of OpenCore and the Kext I needed, here below is a screenshot of my EFI partition pre-install.

On making the Ethernet work during install
Our main issue with this build is that we don't have Ethernet OOB, and IntelMausi kext won't work as it does not support our hardware, hence we won't be able to install macOS without a workaround. After some digging, I found out that we can use RehabMan's FAKEPCIID + FAKEPCIID_Intel_Intel_I225-V to enable Ethernet, which I did.

- I triple checked all the ACPI's, Kext, Drivers and checked the config.plist on the Sanity Checker, moved everything on the USB stick EFI partition inside it s EFI folder.

- Rebooted in BIOS, changed the settings. Also important note, if you have the latest BIOS, or flashed the latest BIOS, you can change settings on the Kernel Quirks for AppleCpuPmCfgLock and AppleXcpmCfgLock, neat!

- Rebooted once again and booted via the USB stick, at some point after a restart, the system will tell you something something about the Internet connections, you can safely say Do it later and it will finish the installation. Congrats!

Post Install

Coolx3, now you can move your EFI partition to the actual EFI partition in your HD mounting it with Hackintool, so you can boot without a USB stick, and start optimising your system! I added a GUI boot menu, boot chime, FileVault and stuff, you can find all the info in the Dortania Post Install Guide. Here below some clarifications on post install fine tuning. Here below my post install EFI

Sleep/Wake: I used the settings for and Energy Savings Settings from u/SchmockLord1912, you can find those in his GitHub

USB: I use USBInjectAll.kext and created my own SSDT-EC-USBX.aml and SSDT-UIAC.aml using Hackintool 3.4.0. All external ports are enabled and I don't use the internal ones. SSDT's are in my GitHub.

iGPU UHD630+ RX 5700XT: I have managed to enable the iGPU UHD630 but I couldn't get the HDMI-output working, using it in headless mode at the moment. I also added some kexts to optimise the RX 5700, which show un uptake of 30% in benchmarks.

Audio Realtek ALC1220-VB: u/SchmockLord1912 has done the heavy lifting, you will need AppleALC.kext, FakeID.kext, FakePCIID_Intel_HDMI_Audio.kext, layout-id=7, device-id=0xA170

Wi-Fi/ Bluetooth: You will need IntelBluetoothFirmware.kext, IntelBluetoothInjector.kext, itlwmx.kext and Heliport, you can get them via the Kext Updater, or google. I set Heliport up to open on Start up so I get Wi-Fi straight away.
