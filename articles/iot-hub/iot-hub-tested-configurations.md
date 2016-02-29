<properties
	pageTitle="Compatibilità delle piattaforme del sistema operativo e hardware per | Microsoft Azure"
	description="Vengono riepilogate la compatibilità SDK per dispositivi IoT con la piattaforme del sistema operativo e l’hardware del dispositivo."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/16/2016"
     ms.author="hegate"/>

# Compatibilità delle piattaforme del sistema operativo e hardware con gli SDK per dispositivi

Questo documento descrive la compatibilità degli SDK con diverse piattaforme del sistema operativo, nonché le configurazioni di dispositivi specifici incluse nel [programma di certificazione Microsoft Azure per IoT](#microsoft-azure-certified-for-iot). Se si ha già un dispositivo, controllare l'elenco di dispositivi inclusi nel programma per trovare informazioni sulla compatibilità specifiche del dispositivo. Se non si è certi di quale dispositivo usare, vedere la sezione sulla compatibilità di [piattaforme del sistema operativo e librerie](#os-platforms).


## Piattaforme del sistema operativo

Le librerie di Azure IoT sono state testate nelle piattaforme del sistema operativo seguente:


|Piattaforme del sistema operativo Linux/Unix | Versione|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Piattaforme del sistema operativo Windows | Versione|
|:---------------|:------------:|
|Desktop di Windows| 7, 8, 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Altre piattaforme | Versione|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## Librerie C

L'[SDK per dispositivi Microsoft Azure IoT per C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) è stato testato nelle configurazioni seguenti:

|Piattaforma del sistema operativo| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Desktop di Windows| 7, 8, 10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Librerie Node.js

L'[SDK per dispositivi Microsoft Azure IoT per Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) è stato testato nelle configurazioni seguenti:


|Runtime| Versione|Protocolli|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Librerie Java

L'[SDK per dispositivi Microsoft Azure IoT per Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

L'SDK per servizi Microsoft Azure IoT per Java è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp

L'[SDK per dispositivi Microsoft Azure IoT per .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) è stato testato nelle configurazioni seguenti:

|Piattaforma del sistema operativo| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Desktop di Windows| 7, 8, 10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

Il codice dell'agente gestito richiede Microsoft .NET Framework 4.5


## Certificazione Microsoft Azure per IoT

**Il programma di certificazione Microsoft Azure per IoT** è il programma per i partner che mette in contatto un ampio ecosistema IoT con Microsoft Azure in modo che sviluppatori e architetti conoscano gli scenari di compatibilità. In particolare, fornisce un elenco attendibile di combinazioni di sistema operativo/dispositivo per iniziare rapidamente a usare un progetto IoT, indipendentemente dal fatto che ci si trovi in fase di modello di prova o pilota. Con le combinazioni certificate di dispositivo e sistema operativo, il progetto IoT può partire rapidamente, riducendo il lavoro e la personalizzazione necessari per garantire la compatibilità dei dispositivi con [Azure IoT Suite][lnk-iot-suite] e l'hub IoT di Azure.

## Dispositivi certificati per IoT

La compatibilità dei dispositivi **certificati per IoT** con gli SDK Azure IoT è stata testata e possono essere usati nell'applicazione IoT. In particolare, la compatibilità viene identificata in base alla piattaforma del sistema operativo e al linguaggio del codice.

#### Elenco di dispositivi

Ogni dispositivo è stato certificato per l'uso dell'SDK nel sistema operativo e nel linguaggio scelti dal produttore del dispositivo. Ad esempio, BeagleBone Black funziona in Debian con il linguaggio C, Javascript e Java. Gli sviluppatori possono quindi compilare le applicazioni in qualsiasi combinazione di linguaggio e sistema operativo sui dispositivi specifici.

|Dispositivo| Sistema operativo testato |Lingua|
|:---------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|
|[AAEON BOXER-6614](http://www.aaeon.com/en/p/fanless-embedded-computers-boxer-6614/) |Windows 10 | C#|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|
|[AAEON UP-CHT](http://www.up-board.org/) |ubilinux | C|
|[AAEON UP](http://www.up-board.org/) |Windows 10 | C#|
|[Acme Systems Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|
|[Avalue RIPAC-10P1](http://www.avalue.com.tw/) |Windows 10 | C#|
|[Avalue RITAB-10T1](http://www.avalue.com.tw/product/Intelligent-Systems/Mobile-Solution/Mobile-Solution/RiTab-10T1_2384) |Windows 10 | C#|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Wind River | JavaScript|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|
|[Advantech Co., LTD UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Windows 10 | C#|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino IDE | C|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino IDE | C|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, Javascript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, Javascript, Java|
|[ComponentSoft RFID Tunnel](http://www.componentsoft.com/) |Windows 10 | C#|
|[Dell Edge Gateway serie 5000](http://www.dell.com/IoTgateway) |Ubuntu | Java|
|[e-con Systems Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[Sistemi incorporati serie Logic Machine](http://openrb.com/products/) |Linux personalizzato | C|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|
|[IEI ICECARE-10W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|
|[IEI IVS-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|
|[Ilevia Eve Raspberry](http://www.ilevia.com/overview/) |Debian | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, Javascript|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|
|[MechaTracks 3GPI](http://www.mechatrax.com/products/3gpi) |Debian | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[MiTAC Computing Technology Pluto E220](http://client.mitac.com/products-Embedded-Box-PC-PlutoE220.html) |Windows 10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Pacific Control Systems G2021ES](http://www.pacificcontrols.net/products/G2021ES-Gateway.html) |Ubuntu | C|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, Javascript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, Javascript, C#|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Javascript, Java|
|[Trex NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|
|[Trueverit V4](http://www.trueverit.com/) |Linux personalizzato | C|
|[USISH EDA8909](http://www.usish.com/) |Windows 10 | C#|

[Iniziare a usare questi dispositivi](https://azure.microsoft.com/develop/iot/get-started/) o visitare il [repository](https://github.com/Azure/azure-iot-sdks) GitHub e cercare i documenti dei dispositivi in base al linguaggio.

## Passaggi successivi

Altre informazioni sullo sviluppo di soluzioni che usano [dispositivi certificati per IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0218_2016-->