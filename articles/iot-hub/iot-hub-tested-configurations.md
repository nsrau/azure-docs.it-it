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
     ms.date="10/09/2015"
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

**Il programma di certificazione Microsoft Azure per IoT** è il programma per i partner che mette in contatto un ampio ecosistema IoT con Microsoft Azure in modo che sviluppatori e architetti conoscano gli scenari di compatibilità. In particolare, fornisce un elenco attendibile di combinazioni di sistema operativo/dispositivo per iniziare rapidamente a usare un progetto IoT, indipendentemente dal fatto che ci si trovi in fase di modello di prova o pilota. Con le combinazioni certificate di dispositivo e sistema operativo, il progetto IoT può partire rapidamente, riducendo il lavoro e la personalizzazione necessari per garantire la compatibilità dei dispositivi con Azure IoT Suite e l'hub IoT di Azure.

## Dispositivi certificati per IoT

La compatibilità dei dispositivi **certificati per IoT** con gli SDK Azure IoT è stata testata e possono essere usati nell'applicazione IoT. In particolare, la compatibilità viene identificata in base alla piattaforma del sistema operativo e al linguaggio del codice.

#### Elenco di dispositivi

Ogni dispositivo è stato certificato per l'uso dell'SDK nel sistema operativo e nel linguaggio scelti dal produttore del dispositivo. Ad esempio, BeagleBone Black funziona in Debian con il linguaggio C, Javascript e Java. Gli sviluppatori possono quindi compilare le applicazioni in qualsiasi combinazione di linguaggio e sistema operativo sui dispositivi specifici.

|Dispositivo| Sistema operativo testato |Linguaggio|
|:---------|:----------|:----------|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Wind River | JavaScript|
|[Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Javascript, Java|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, Javascript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, Javascript, Java|
|[Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Javascript, Java|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, Javascript|
|[LogicMachine Series](http://openrb.com/products/) |Linux personalizzato | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, Javascript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, Javascript, C#|
|[STM32 Nucleo](http://www.st.com/stm32nucleo) |STM32Cube | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|

Per iniziare a usare questi dispositivi, fare clic [qui](https://azure.microsoft.com/develop/iot/get-started/) o visitare il [repository](https://github.com/Azure/azure-iot-sdks) GitHub e cercare i documenti dei dispositivi in base al linguaggio.

## Passaggi successivi

Per altre informazioni sullo sviluppo di soluzioni che usano dispositivi **certificati per IoT**, fare clic [qui](http://azure.com/iotdev).

<!---HONumber=AcomDC_1210_2015-->