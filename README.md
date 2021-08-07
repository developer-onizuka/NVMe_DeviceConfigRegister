# 1. Check lspci with "-vvv" options
```
$ lspci |grep -i nvme
06:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM961/PM961

$ sudo lspci -x -vvv -s 06:00.00
06:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM961/PM961 (prog-if 02 [NVM Express])
	Subsystem: Samsung Electronics Co Ltd NVMe SSD Controller SM961/PM961
	Physical Slot: 0-5
	Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR+ FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0
	Interrupt: pin A routed to IRQ 22
	NUMA node: 0
	Region 0: Memory at fd600000 (64-bit, non-prefetchable) [size=16K]
	Capabilities: [40] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0-,D1-,D2-,D3hot-,D3cold-)
		Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [50] MSI: Enable- Count=1/32 Maskable- 64bit+
		Address: 0000000000000000  Data: 0000
	Capabilities: [70] Express (v2) Endpoint, MSI 00
		DevCap:	MaxPayload 256 bytes, PhantFunc 0, Latency L0s unlimited, L1 unlimited
			ExtTag- AttnBtn- AttnInd- PwrInd- RBE+ FLReset+ SlotPowerLimit 25.000W
		DevCtl:	CorrErr+ NonFatalErr+ FatalErr+ UnsupReq+
			RlxdOrd+ ExtTag- PhantFunc- AuxPwr- NoSnoop+ FLReset-
			MaxPayload 128 bytes, MaxReadReq 512 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #0, Speed 8GT/s, Width x4, ASPM L1, Exit Latency L1 <64us
			ClockPM+ Surprise- LLActRep- BwNot- ASPMOptComp+
		LnkCtl:	ASPM Disabled; RCB 64 bytes Disabled- CommClk+
			ExtSynch- ClockPM+ AutWidDis- BWInt- AutBWInt-
		LnkSta:	Speed 8GT/s (ok), Width x4 (ok)
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		DevCap2: Completion Timeout: Range ABCD, TimeoutDis+, NROPrPrP-, LTR+
			 10BitTagComp-, 10BitTagReq-, OBFF Not Supported, ExtFmt-, EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS-, TPHComp-, ExtTPHComp-
			 AtomicOpsCap: 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis-, LTR+, OBFF Disabled
			 AtomicOpsCtl: ReqEn-
		LnkCtl2: Target Link Speed: 8GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance De-emphasis: -6dB
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete+, EqualizationPhase1+
			 EqualizationPhase2+, EqualizationPhase3+, LinkEqualizationRequest-
	Capabilities: [b0] MSI-X: Enable+ Count=33 Masked-
		Vector table: BAR=0 offset=00003000
		PBA: BAR=0 offset=00002000
	Capabilities: [100 v2] Advanced Error Reporting
		UESta:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
		UEMsk:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
		UESvrt:	DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+ ECRC- UnsupReq- ACSViol-
		CESta:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr-
		CEMsk:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr+
		AERCap:	First Error Pointer: 00, ECRCGenCap+ ECRCGenEn- ECRCChkCap+ ECRCChkEn-
			MultHdrRecCap- MultHdrRecEn- TLPPfxPres- HdrLogCap-
		HeaderLog: 00000000 00000000 00000000 00000000
	Capabilities: [148 v1] Device Serial Number 00-00-00-00-00-00-00-00
	Capabilities: [158 v1] Power Budgeting <?>
	Capabilities: [188 v1] Latency Tolerance Reporting
		Max snoop latency: 3145728ns
		Max no snoop latency: 3145728ns
	Kernel driver in use: nvme
	Kernel modules: nvme
00: 4d 14 04 a8 06 05 10 00 00 02 08 01 00 00 00 00
10: 04 00 60 fd 00 00 00 00 00 00 00 00 00 00 00 00
20: 00 00 00 00 00 00 00 00 00 00 00 00 4d 14 01 a8
30: 00 00 00 00 40 00 00 00 00 00 00 00 0b 01 00 00
```

# 2. Find the datasheet of your NVMe device and UNDERSTAND IT
According to the spec of Samsung Datasheet below, [Table 48] PCI Express Capability Summary says "78h" is a start address of "PCI Express Device Capabilities". 
We can learn that [14:12] is "Max Read Request Size" and [7:5] is "Max Payload Size" from [Table 52] PCI Express Device Control Register.

https://www.compuram.biz/documents/datasheet/Samsung_PM981_Rev_1_1.pdf

[Table 52] PCI Express Device Control Register
|Bits|Type|Default Value|Description|
| --- | --- | --- | --- |
|15|RW|0|Initiate Function Level Reset|
|14:12|RW|2h|Max Read Request Size|
|11|RW|1|Enable No Snoop|
|10|RWS|0|Aux Power PM Enable (N/A)|
|9|RW|0|Phantom Functions Enable (N/A)|
|8|RW|0|Extended Tag Enable|
|7:5|RW|0h|Max Payload Size|
|4|RW|1|Enable Relaxed Ordering|
|3|RW|0|Unsupported Request Reporting Enable|
|2|RW|0|Fatal Error Reporting Enable|
|1|RW|0|Non-Fatal Error Reporting Enable|
|0|RW|0|Correctable Error Reporting Enable|


# 3. Change the value thru setpci
```
$ sudo setpci -s 06:00.0 0x78.w
281f
```
Value = 281f (Max Read Request=512B)
|Bit|Binary|
| --- | --- |
|15|0|
|14:12|010|

Value = 381f (Max Read Request=1024B)
|Bit|Binary|
| --- | --- |
|15|0|
|14:12|011|

Value = 481f (Max Read Request=2048B)
|Bit|Binary|
| --- | --- |
|15|0|
|14:12|100|

Value = 581f (Max Read Request=4096B)
|Bit|Binary|
| --- | --- |
|15|0|
|14:12|101|

```
$ sudo setpci -s 06:00.0 0x78.w=381f
$ sudo lspci -x -vvv -s 06:00.00 |grep MaxReadReq
			MaxPayload 128 bytes, MaxReadReq 1024 bytes

$ sudo setpci -s 06:00.0 0x78.w=481f
$ sudo lspci -x -vvv -s 06:00.00 |grep MaxReadReq
			MaxPayload 128 bytes, MaxReadReq 2048 bytes

$ sudo setpci -s 06:00.0 0x78.w=581f
$ sudo lspci -x -vvv -s 06:00.00 |grep MaxReadReq
			MaxPayload 128 bytes, MaxReadReq 4096 bytes
```


Value = 283f (MaxPayload=256B)
|Bit|Binary|
| --- | --- |
|7:5|001|
|4|1|

```
$ sudo setpci -s 06:00.0 0x78.w=283f
$ sudo lspci -x -vvv -s 06:00.00 |grep MaxPayload
		DevCap:	MaxPayload 256 bytes, PhantFunc 0, Latency L0s unlimited, L1 unlimited
			MaxPayload 256 bytes, MaxReadReq 512 bytes
```

