# VMwareHardenedLoader
VMware Hardened VM detection mitigation loader

For now, only Windows (vista~win10) x64 guests are supported.

It get VMware guest undetected by VMProtect 3.2, Safengine and Themida (anti-vm feature).

## What it does

在虚拟机里开冒险岛

## System Requirements

Windows 10 OS

VMWare Workstation 16

Build VmLoader as x64/Release. (No x86 support for now)

Remember to test-sign "bin/vmloader.sys" if you want to load it in test-sign mode.

# Before

## 虚拟机註冊表修改 nvidia



（1）win+r 输入 regedit 打开注册表



虚拟机注册表修改位置：
（2）ctrl+f 打开查找 输入：
{4D36E968-E325-11CE-BFC1-08002BE10318}

or

计算机\HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0000

找到0000

找到‘vmare’ 开头值 ，请全部修改成 ‘Nvidia’ 

# Installation

## Warning

Do not install vmtools, it will ruin everything!

Use TeamViewer / AnyDesk / mstsc / VNC viewer instead!

## 1st Step: Add following settings into .vmx

```
hypervisor.cpuid.v0 = "FALSE"
board-id.reflectHost = "TRUE"
hw.model.reflectHost = "TRUE"
serialNumber.reflectHost = "TRUE"
smbios.reflectHost = "TRUE"
SMBIOS.noOEMStrings = "TRUE"
isolation.tools.getPtrLocation.disable = "TRUE"
isolation.tools.setPtrLocation.disable = "TRUE"
isolation.tools.setVersion.disable = "TRUE"
isolation.tools.getVersion.disable = "TRUE"
monitor_control.disable_directexec = "TRUE"
monitor_control.disable_chksimd = "TRUE"
monitor_control.disable_ntreloc = "TRUE"
monitor_control.disable_selfmod = "TRUE"
monitor_control.disable_reloc = "TRUE"
monitor_control.disable_btinout = "TRUE"
monitor_control.disable_btmemspace = "TRUE"
monitor_control.disable_btpriv = "TRUE"
monitor_control.disable_btseg = "TRUE"
monitor_control.restrict_backdoor = "TRUE"
```

If you have a SCSI virtual disk at scsi0 slot (first slot) as your system drive, remember to add

```
scsi0:0.productID = "Whatever you want"
scsi0:0.vendorID = "Whatever you want"
```

I use
```
scsi0:0.productID = "Tencent SSD"
scsi0:0.vendorID = "Tencent"
```

## 2nd Step: Modify MAC address

Modify guest's MAC address to whatever except below:
```
	TCHAR *szMac[][2] = {
		{ _T("\x00\x05\x69"), _T("00:05:69") }, // VMWare, Inc.
		{ _T("\x00\x0C\x29"), _T("00:0c:29") }, // VMWare, Inc.
		{ _T("\x00\x1C\x14"), _T("00:1C:14") }, // VMWare, Inc.
		{ _T("\x00\x50\x56"), _T("00:50:56") },	// VMWare, Inc.
	};
```

![mac](https://github.com/hzqst/VmwareHardenedLoader/raw/master/img/4.png)

You could add

```
ethernet0.address = "Some random mac address"
```
Into vmx file instead of modifying MAC address in VMware GUI

I use

```
ethernet0.address = "00:11:56:20:D2:E8"
```

## 3rd Step: Run install.bat in vm guest as Administrator Priviledge

If an error occurs when start service, use DbgView to capture kernel debug output. you can post an issue with DbgView output information and with your ntoskrnl.exe attached.

If no error occurs, then everything works fine.

## Showcase

VMware guest win8.1 x64 with VMProtect 3.2 packed program (anti-vm option enabled)

![before](https://github.com/hzqst/VmwareHardenedLoader/raw/master/img/1.png)
![sigs](https://github.com/hzqst/VmwareHardenedLoader/raw/master/img/2.png)
![after](https://github.com/hzqst/VmwareHardenedLoader/raw/master/img/3.png)

## License
This software is released under the MIT License, see LICENSE.

Some util procedures are from https://github.com/tandasat/HyperPlatform

https://github.com/aquynh/capstone is used to disasm ntoskrnl code.

## TODO

VMware virtual graphic card information could be detected by querying DXGI interface, which could be modified by editing graphic driver files.
