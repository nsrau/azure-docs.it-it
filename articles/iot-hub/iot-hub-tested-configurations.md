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
     ms.date="09/27/2016"
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

|Piattaforme del sistema operativo Android | Versione|
|:---------------|:------------:|
|Android| > 4.2|

|Piattaforme del sistema operativo Windows | Versione|
|:---------------|:------------:|
|Desktop di Windows| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Altre piattaforme | Versione|
|:---------------|:------------:|
|Arduino | IDE 1.6.8 |
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## Librerie C

L'[SDK per dispositivi Microsoft Azure IoT per C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) è stato testato nelle configurazioni seguenti:

|Piattaforma del sistema operativo| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Arduino| IDE 1.6.8 | HTTPS |
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|mbed OS| 2\.0 | HTTPS, AMQP, MQTT |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Desktop di Windows| 10 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Librerie Node.js

L'[SDK per dispositivi Microsoft Azure IoT per Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP su WebSocket |

L'SDK per servizi Microsoft Azure IoT per Node.js è stato testato nelle configurazioni seguenti:

|Runtime| Versione|
|:---------|:----------:|
|Node.js| 4\.1.0 |


## Librerie Java

L'[SDK per dispositivi Microsoft Azure IoT per Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|
|Android| API 15 | HTTPS, MQTT |

L'SDK per servizi Microsoft Azure IoT per Java è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

L'[SDK per dispositivi Microsoft Azure IoT per .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) è stato testato nelle configurazioni seguenti:

|Piattaforma del sistema operativo| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Desktop di Windows| 10 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Windows IoT Core|10 | HTTPS |
|PCL (Xamarin, Mono, UWP, WP8.1, Win8.1)| | HTTPS, AMQP, MQTT, AMQP su WebSocket |

L'SDK per servizi Microsoft Azure IoT per CSharp è stato testato nelle configurazioni seguenti:

|Runtime| Versione|
|:---------|:----------:|
|.Net| 4\.5 |
|UWP| |

Il codice dell'agente gestito richiede Microsoft .NET Framework 4.5


## Librerie Python

L'[Azure IoT SDK per dispositivi di Microsoft per Python](https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Python | 2\.7.x, 3.4.x, 3.5.x | HTTPS, AMQP, MQTT |


## Certificazione Microsoft Azure per IoT

**Il programma di certificazione Microsoft Azure per IoT** è il programma per i partner che mette in contatto un ampio ecosistema IoT con Microsoft Azure in modo che sviluppatori e architetti conoscano gli scenari di compatibilità. In particolare, fornisce un elenco attendibile di combinazioni di sistema operativo/dispositivo per iniziare rapidamente a usare un progetto IoT, indipendentemente dal fatto che ci si trovi in fase di modello di prova o pilota. Con le combinazioni certificate di dispositivo e sistema operativo, il progetto IoT può partire rapidamente, riducendo il lavoro e la personalizzazione necessari per garantire la compatibilità dei dispositivi con [Azure IoT Suite][lnk-iot-suite] e l'hub IoT di Azure.

## Dispositivi certificati per IoT

La compatibilità dei dispositivi **certificati per IoT** con gli SDK Azure IoT è stata testata e possono essere usati nell'applicazione IoT. In particolare, la compatibilità viene identificata in base alla piattaforma del sistema operativo e al linguaggio del codice.

Non è possibile trovare il dispositivo desiderato nell'elenco Nessun problema. L'SDK IoT di Azure è open source e progettato per essere portabile. Visitare il [repository](https://github.com/Azure/azure-iot-sdks) della libreria del client su GitHub per iniziare e segnalare eventuali problemi durante la connessione del dispositivo nella sezione [Issues](https://github.com/Azure/azure-iot-sdks/issues) (Problemi).

#### Elenco di dispositivi

Ogni dispositivo è stato certificato per l'uso dell'SDK nel sistema operativo e nel linguaggio scelti dal produttore del dispositivo. Ad esempio, BeagleBone Black funziona in Debian con il linguaggio C, Javascript e Java. Gli sviluppatori possono quindi compilare le applicazioni in qualsiasi combinazione di linguaggio e sistema operativo sui dispositivi specifici.

|Dispositivo| Sistema operativo testato |Lingua|Istruzioni|
|:---------|:----------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-acp-1104-csharp.md)|
|[AAEON BOXER-6614](http://www.aaeon.com/en/p/fanless-embedded-computers-boxer-6614/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-boxer-6614-csharp.md)|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-gene-bt05-csharp.md)|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-gene-bt05-c.md)|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-PICO-BT01-c.md)|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-%20pico-bt01-csharp.md)|
|[AAEON UP](http://www.up-board.org/) |ubilinux | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubilinux-up-board.md)|
|[AAEON UP](http://www.up-board.org/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aaeon-up-csharp.md)|
|[Acme Systems Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-arietta-g25-c.md)|
|[Adafruit Feather M0 Wifi](https://www.adafruit.com/product/3010) |Arduino IDE(RTOS) | Arduino,C|[Introduzione](https://azure.microsoft.com/documentation/samples/iot-hub-c-m0wifi-getstartedkit/)|
|[Adafruit Feather Huzzah](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/overview) |Arduino IDE(RTOS) | Arduino,C|[Introduzione](https://azure.microsoft.com/documentation/samples/iot-hub-c-huzzah-getstartedkit/)|
|[ADLINK IMB-M43](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1600&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_ ATX&utm\_source=#) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-imb-m43-csharp.md)|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxe-200-csharp.md)|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-mxe-200-csharp.md)|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Wind River | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-mxe-202i-nodejs.md)|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxe-5400-csharp.md)|
|[ADLINK MXC-6300](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1242&seq=&id=&sid=&category=Fanless-Embedded-Computer_Expandable-Embedded-Computer&utm_source=) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxc-6300-csharp.md)|
|[ADLINK NuPRO-E43](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1634&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_PICMG-1.x-CPU-boards&utm_source=) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nupro-e43-csharp.md)|
|[Advantech Co., AIMB-203](http://www.advantech.com/products/68ccaea2-9ff5-4f85-97f2-3d11244b0a08/aimb-203/mod_a46984f8-caa9-433a-a720-e1815a8c16a3) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-203-csharp.md)|
|[Advantech Co., AIMB-215](http://www.advantech.com/products/f910a32b-088c-4f15-8a94-f130f22bc1b5/aimb-215/mod_2fd07ef0-7445-4131-a3b0-379548b78e27) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-215-csharp.md)|
|[Advantech Co., AIMB-501](http://www.advantech.com/products/1-2jkkgc/aimb-501/mod_3ed96dd6-792f-431d-b3e7-c524380b15d6) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-501-csharp.md)|
|[Advantech Co., AIMB-503](http://www.advantech.com/products/1-2jkkgc/aimb-503/mod_5db22cce-25c7-488d-a5a4-f76a4061161b) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-503-csharp.md)|
|[Advantech Co., ARK-1123H](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123h/mod_590fcfdc-278c-41b1-a8c7-d29152cd96d7) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ark-1123h-csharp.md)|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ark-2121l-csharp.md)|
|[Advantech Co., DPX-S430](http://www.advantech.com/products/standalone_(s_series)/dpx-s430/mod_2c298749-cd5f-4c67-928a-0325836dd991) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dpx-s430-csharp.md)|
|[Advantech Co., DPX-S435](http://www.advantech.com/products/standalone_(s_series)/dpx-s435/mod_fe4f6841-5900-44fe-8912-d86de7d15c65) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dpx-s435-csharp.md)|
|[Advantech Co., DS-060](http://www.advantech.com/products/85d19842-5892-4e78-bf14-0d4d6ce36e04/ds-060/mod_c739322c-c2c7-43f1-9f4b-8483fea4e3d7) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ds-060-csharp.md)|
|[Advantech Co., DS-370](http://www.advantech.com/products/5408f56b-e978-41fa-9509-0b9fcd27e902/ds-370/mod_ada71950-a0b5-4ee2-91b2-9aed2d1c6af8) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ds-370-csharp.md)|
|[Advantech Co., MIO-2261](http://www.advantech.com/products/mi%7B%7B--o_ultra_single_board_computers/mio-2261/mod_a06b85aa-8734-42c1-8ff7-a3854bf1eadf) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-2261-csharp.md)|
|[Advantech Co., MIO-2263](http://www.advantech.com/products/mi%7B%7B--o_ultra_single_board_computers/mio-2263/mod_3c9191fb-edf1-49a5-a58c-b4734867ecda) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-2263-csharp.md)|
|[Advantech Co., MIO-5250](http://www.advantech.com/products/bc6786b7-b69b-4fdd-9b63-79180dbd5dcb/mio-5250/mod_eea5e107-b1e6-4989-a8e0-03a042f5dbef) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-5250-csharp.md)|
|[Advantech Co., MIO-5251](http://www.advantech.com/products/bc6786b7-b69b-4fdd-9b63-79180dbd5dcb/mio-5251/mod_ff8534cc-e858-40ba-9bc2-386f19bfee4a) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-5251-csharp.md)|
|[Advantech Co., PCM-3356](http://www.advantech.com/products/1-2jkltu/pcm-3356/mod_0706f4d5-2e44-473a-a7b7-53bd1a7bd1a0) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-3356-csharp.md)|
|[Advantech Co., PCM-9365](http://www.advantech.com/products/1-2jkd1i/pcm-9365/mod_5051a369-13cb-4cda-a44b-cc34e37d6196) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-9365-csharp.md)|
|[Advantech Co., PCM-9376](http://www.advantech.com/products/1-2jkd1i/pcm-9376/mod_9870f332-c45f-4ac1-acf0-3407038c0d4a) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-9376-csharp.md)|
|[Advantech Co., SOM-5893](http://www.advantech.com/products/1-e0e7i7/som-5893/mod_7f19c5d2-2fee-4b7f-a551-43c826c20c25) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-som-5893-csharp.md)|
|[Advantech Co., SOM-6867](http://www.advantech.com/products/1-34h3r7/som-6867/mod_4cbd463d-32cd-4440-921a-07b2421ec744) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-som-6867-csharp.md)|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-ark-2121l-csharp.md)|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-ark-1123c-csharp.md)|
|[Advantech Co., UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-uno-1372g-csharp.md)|
|[Advantech Co., UNO-1372G](http://www.advantech.tw/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-advantech-uno-1372g-c.md)|
|[Advantech Co., UNO-2272G](http://www.advantech.tw/products/1-2mlj9a/uno-2272g/mod_2f889619-f9ba-4735-a432-7ac7a08669c4) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-advantech-uno-2272g-jxae-c.md)|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-utx-3115-csharp.md)|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-trek-674-csharp.md)|
|[Alleantia IoT SCADA SERVER](http://www.alleantia.com/en/iot-scada-server-2) |Ubuntu | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-IoT-scada-server-java.md)|
|[Allwinner Technology Banana Pi BPI-M64](http://www.banana-pi.com/eacp_view.asp?id=95) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-banana-pi-bpi-m64-csharp.md)|
|[Allwinner Technology Pine64](https://www.pine64.com/product#features) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-pine64-csharp.md)|
|[FATBOX G3 amplificato](http://www.amplified.com.au/#!4gltegateway/c192n) |OpenWRT Linux | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-openwrt-fatbox-g3-c.md)|
|[AOPEN Inc. eTILE15-FB](http://www.aopen.com/global/shop/products/etile-15m) |Windows 10 IoT Enterprise LTSB | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile15-fb-csharp.md)|
|[AOPEN Inc. ETILE19-FB](http://www.aopen.com/global/shop/products/etile-19m) |Windows 10 IoT Enterprise LTSB | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile19-fb-csharp.md)|
|[AOPEN Inc. ETILE22-FW](http://www.aopen.com/global/shop/products/etile-22m) |Windows 10 IoT Enterprise LTSB | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile22-fw-csharp.md)|
|[Arbor IEC-3300](http://www.arbor-technology.com/tw/Product/Pro/Model/IEC-3300_6) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-Arbor-IEC-3300-csharp.md)|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino IDE | Arduino, C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/arduinoide-arduino-wifi101-c.md)|
|[Arduino Yun](https://www.arduino.cc/en/Main/ArduinoBoardYun) |Arduino IDE | Arduino, C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-arduino-yun-c.md)|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino IDE | Arduino, C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/arduinoide-arduino-wifi101-c.md)|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-dragonboard-410c-csharp.md)|
|[Atmark Armadillo-IoT Gateway G2](http://armadillo.atmark-techno.com/armadillo-iot/specs) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-armadillo-IoT_GW_G2-c.md)|
|[Atmark Armadillo-IoT Gateway G2](http://armadillo.atmark-techno.com/armadillo-iot-g2/specs) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gateway-g2-linux-java.md)|
|[Atmark Armadillo-IoT Gateway G3](http://armadillo.atmark-techno.com/armadillo-iot-g3) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3-c.md)|
|[Atmark Armadillo-IoT Gateway G3](http://armadillo.atmark-techno.com/armadillo-iot-g3) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3-java.md)|
|[Atmark Armadillo-IoT Gateway G3L](http://armadillo.atmark-techno.com/armadillo-iot-g3l) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3l-java.md)|
|[Atmark Armadillo-IoT Gateway G3L](http://armadillo.atmark-techno.com/armadillo-iot-g3l) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gateway-g3l-c.md)|
|[Avalue EPC-BTCR](http://www.avalue.com.tw/Product/Embedded-Computing/Fanless-Industrial-System/Embedded-Computing-System/EPC-BTCR_2323) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Windows10-epc-btcr-csharp.md)|
|[Avalue RIPAC-10P1](http://www.avalue.com.tw/product/Intelligent-Systems/POS-Terminal/POS-hardware-systems/RiPac-10P1_2399) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-ripac-10p1-csharp.md)|
|[Avalue RITAB-10T1](http://www.avalue.com.tw/product/Intelligent-Systems/Mobile-Solution/Mobile-Solution/RiTab-10T1_2384) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-ritab-10t1-csharp.md)|
|[Avalue OFT-07W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-07W01_2394) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-07w01-csharp.md)|
|[Avalue OFT-10W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-10W01_2324) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-10w01-csharp.md)|
|[Avalue OFT-15W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-15W01_2325) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-15w01-csharp.md)|
|[Avalue OFT-21W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-21W01_2326) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-21w01-csharp.md)|
|[Axiomtek eBOX560-300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=17308&upcat=144) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ebox560-300-csharp.md)|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-axiomtek-ico300-csharp.md)|
|[Base Technology QuiX Sensor Gateway](https://www.quix.jp/iot/iotqsx/) |Raspbian | Python|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-quix-sensor-gateway-python.md)|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-beaglebone-black-c.md)|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-beaglebone-black-java.md)|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-beaglebone-green-c.md)|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-beaglebone-green-java.md)|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Bitjoule Magic Box](http://www.bjbox.io/) |Raspbian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-bitjoule-magic-box-c.md)|
|[Bluebird BM180](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=508) |Windows 10 IoT Mobile Enterprise | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-BM180-csharp.md)|
|[Bluebird BP30](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=509) |Windows 10 IoT Mobile Enterprise | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-BP30-csharp.md)|
|[Bluebird EF400](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=519) |Windows 10 IoT Mobile Enterprise| C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF400-csharp.md)|
|[Bluebird EF500](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=511) |Windows 10 IoT Mobile Enterprise | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF500-csharp.md)|
|[Bluebird EF500R](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=511) |Windows 10 IoT Mobile Enterprise | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF500R-csharp.md)|
|[Clientron IT800](http://www.clientron.com/en/goods.php?act=view&no=38) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-it800-c.md)|
|[Clientron PT6500](http://www.clientron.com/en/goods.php?act=view&no=20) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-PT6500-c.md)|
|[ComponentSoft RFID Tunnel](http://www.componentsoft.com/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-rfid-tunnel-csharp.md)|
|[Contec BX-320](http://www3.contec.co.jp) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-BX-320-csharp.md)|
|[Contec BX-830](http://www3.contec.co.jp) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-BX-830-csharp.md)|
|[Contec CPS-MC341-ADSC2](http://www3.contec.co.jp/B2B/ConIWCatProductPage_B2B.process?Merchant_Id=1&Section_Id=0&Catalog_Id=0&Product_Id=2537) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-CPS-MC341-ADSC2-c.md)|
|[DataVan Glamor Series](http://www.datavan.com.tw/zh_hant/product_4.html) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-glamor-series-csharp.md)|
|[Dell Edge Gateway serie 5000](http://www.dell.com/IoTgateway) |Ubuntu | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dell-edge-gateway-5000-series-java.md)|
|[DFI EC700-BT](http://www.dfi.com.tw/Upload/Product/Documents/BT700.pdf) |Windows 10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows7-ec700-bt-c.md)|
|[DUX Inc HFBX-100](http://dux.jp/product/hfbx-100.html) |Windows10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-HFBX-100-csharp.md)|
|[e-con Systems Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-almach-c.md)|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-ankaa-c.md)|
|[Ecomott Cloud Logger LTE](http://www.ecomott.co.jp/product/cloudloggerlte.html) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-cloud-logger-lte-c.md)|
|[Sistemi incorporati serie Logic Machine](http://openrb.com/products/) |Linux personalizzato | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-logicmachine-c.md)|
|[Fitivision FDG-1000](http://www.fitivision.com/) |Linux | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-honeybee-c.md)|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-freescale-k64f-c.md)|
|[Tablet FUJITSU STYLISTIC con UBIQUITOUSWARE](http://www.fmworld.net/biz/uware/) |Windows10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-arrows-tab-csharp.md)|
|[Hanasis HW-5000](http://hanasis.com/index_eng_test/hanasis/product/pos/pos_hw-5000.php) |Windows 10 IoT Enterprise 2015 LTSB| C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-2015-ltsb-hw-5000-c.md)|
|[Hitachi HF-W/IoT](http://www.hitachi-ics.co.jp/product/iot_ctr/index.html) |Windows 10| C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-HF-WIoT-c.md)|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-8.1-embedded-industry-enterprise-el10-csharp.md)|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-8.1-embedded-industry-enterprise-el20-csharp.md)|
|[iBase CSB200-897](http://www.ibase-usa.com/english/ProductDetail/EmbeddedComputing/CSB200-897) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-csb200-897-csharp.md)|
|[iBase SI-12](http://www.ibase.com.tw/english/ProductDetail/DigitalSignagePlayer/SI-12) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-si-12-csharp.md)|
|[IEI ECW-281B-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?keyword=ECW-281&gid=09049552811981014603&cid=0D182494345754583862&id=0F330533395123928332#.Vufhbvl97Dc) |Windows 10| C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-ecw-281b-bt-csharp.md)|
|[IEI ICECARE-10W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-icecare-10w-csharp.md)|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-drpc-120-csharp.md)|
|[IEI IVS-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ivs-100-bt-csharp.md)|
|[IEI TANK-801-BT](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182496000560957303&id=0E316420627555447071#.VuZ1evl97IV) |Windows 10| C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-tank-801-bt-csharp.md)|
|[IEI uIBX-230-BT](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E317335590193360443) |Windows10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-uibx-230-bt-csharp.md)|
|[Ilevia Eve Server](http://www.ilevia.com/eve-server/) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-eve-raspberry-c.md)|
|[Innovactive IoT.NET Field Gateway](http://www.innovactive.it/microsoft-azure-certified-for-iot) |.NET Micro Framework | C# | [Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/dotnet-microframework-iot-net-field-gateway-csharp.md)|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/yocto-intel-edison-c.md)|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Inventec Corp Avatar](http://www.ioeworld.net/) |Android | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/android-avatar-dev-board-java.md)|
|[Inventec Corp Avatar](http://www.ioeworld.net/) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-avatar-csharp.md)|
|[iWave iW-RainboW-G15M](http://www.iwavesystems.com/i-mx6-qseven-som.html) |WEC2013 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/wec2013-iw-rainbow-G15m-c.md)|
|[Keith & Koep Myon](http://keith-koep.com/en/products/products-som/myon-1-features-snapdragon-410/) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-myon-csharp.md)|
|[Keith & Koep TrizepsVII](http://keith-koep.com/en/products/products-trizeps/trizeps-vii-features/) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-trizeps-vii-c.md)|
|[Lanner LEC-2580 IPC](http://www.lannerinc.com/products/embedded-box-pcs/industrial-automation/lec-2580) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-lec-2580-ipc-csharp.md)|
|[Lanner LEC-6030](http://www.lannerinc.com/products/industrial-communication-platforms/industrial-cyber-security-box-pc/lec-6030) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-lec-6030e-csharp.md)|
|[Lanner NCA-1010](http://www.lannerinc.com/products/network-appliances/x86-desktop-network-appliances/nca-1010) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-nca-1010-csharp.md)|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Debian-meshlium-java.md)|
|[MDS Technology IDM-IWP-HW-2](http://mdstec.com/en/contents/?no=326) |Windows | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-idm-iwp-hw-2-java.md)|
|[MechaTracks 3GPI](http://www.mechatrax.com/products/3gpi) |Raspbian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-3gpi-c.md)|
|[Micro-Star Int'l Co. Ltd. NB31](https://www.msi.com/product/tablet/NB31.html#hero-overview) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nb31-csharp.md)|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-minnowboard-max-csharp.md)|
|[MiTAC Computing Technology Pluto E220](http://client.mitac.com/products-Embedded-Box-PC-PlutoE220.html) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pluto-e220-csharp.md)|
|[Motion Control Henry Board](http://www.runele.com/ca1/38/p-r-s/) |Yocto | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-henry-board-javascript.md)|
|[Moxa ioPAC5542-C](http://www.moxa.com.tw/Product/ioPAC_5500.htm) |Linux | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-iopac5542-c-c.md)|
|[MSC Technologies Baytrail Family](https://www.msc-technologies.eu/de/support/boards/qseven/msc-q7-bt.html) |Linux (MSC-LDK) | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-msc-ldk-baytrail-c.md)|
|[Nexcom NDiS B535](http://www.nexcom.com.tw/Products/multi-media-solutions/digital-signage-player/high-performance-player/digital-signage-player-ndis-b535) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-ndis-b535-csharp.md)|
|[Nexcom NIFE100](http://www.nexcom.com.tw/Products/industrial-computing-solutions/pc-based-factory-automation/industr) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NIFE100-csharp.md)|
|[Nexcom NIFE200](http://www.nexcom.com.tw/Products/industrial-computing-solutions/pc-based-factory-automation/industr) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NIFE200-csharp.md)|
|[Nexcom NISE50](http://www.nexcom.com.tw/Products/industrial-computing-solutions/industrial-fanless-computer/atom-co) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NISE50-csharp.md)|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-iot-core-nexcom-nise50-csharp.md)|
|[Nexcom PDSB 325](http://www.nexcom.com/Products/multi-media-solutions/bulletin-board-solutions/media-player-appliance/network-player-pdsb-325) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-pdsb-325-csharp.md)|
|[Nexcom VTC1010](http://www.nexcom.com.tw/Products/mobile-computing-solutions/in-vehicle-pc/in-vehicle-pc/in-vehicle-) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-VTC1010-csharp.md)|
|[Nexcom VTC6210](http://www.nexcom.com.tw/Products/mobile-computing-solutions/train-pc/train-pc/transportation-comput) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-VTC6210-csharp.md)|
|[NMR ND13](https://www.icp-nmr.com/news/IPC/msi-tablet.html) |Windows 10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nd13-c.md)|
|[Iomote sensor box](http://www.iomote.com/iomotesensorbox.php) |OpenWRT | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-iomote-sensor-box-javascript%20.md)|
|[Pacific Control Systems G2021ES](http://www.pacificcontrols.net/products/G2021ES-Gateway.html) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-g2021es-c.md)|
|[PANASONIC Toughpad FZ-F1](http://www.panasonic.com/global/home.html) |Windows 10 IoT Mobile Enterprise | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-enterprise-fz-f1-csharp.md)|
|[Partner Tech EM-300](http://www.partner.com.tw/product/default.asp?prober=134) |Windows 10| C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-EM-300-c.md)|
|[PFU AR2100-model-120K](http://www.pfu.fujitsu.com/prodes/product/ar/ar2100_120k.html) |Windows 10 |C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-AR2100-model-120K-c.md)|
|[PFU AR2200-model-120K](http://www.pfu.fujitsu.com/prodes/product/ar/ar2200_120k.html) |Windows 10 |C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-AR2200-model-120K-c.md)|
|[Plat'Home OpenBlocks IoT BX1G](https://www.plathome.com/en/bx1g/bx1/) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-openblocks-iot-bx1g-c.md)|
|[Protech SE-8124](http://www.protech-usa.net/new_web/Products/product_en.asp?P_id=546&P_typeNo=4&P_subtypeNo=2#tab-1) | Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Windows10-epc-btcr-csharp.md)|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-raspberrypi2-c.md)|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample.md)|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-raspberrypi2-c.md)|
|[Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample.md)|
|[Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Windows 10 IoT Core| C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Renesas Electronics Corporation GR-PEACH](http://gadget.renesas.com/en/product/peach.html) |Mbed | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-renesas-gr-peach-c.md)|
|[RICOH IT9](http://industry.ricoh.com/fbx_board/it9/index.html) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-IT9-csharp.md)|
|[Sam4s TITAN-500](http://sam4s.co.kr/english/service/product/product_detail.asp?product_num=100073) |Windows 10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-sam4s-titan-500-c.md)|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/fedora-samsung-artik-c.md)|
|[SADE IoT Cloud Gateway](http://sade.io/sade-iot-cloud-family) |mbed 2.0 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-sade-iot-cloud-gateway-c.md)|
|[SECO SBC-A80-eNUC](http://www.seco.com/prods/eu/sbc-a80-enuc.html) |Windows10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-sbc-a80-enuc-c.md)|
|[SECO SBC-A80-eNUC](http://www.seco.com/prods/eu/sbc-a80-enuc.html) |Ubuntu | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-sbc-a80-enuc-c.md)|
|[SERAKU Midori Box](https://midori-cloud.net/service.html) |Debian | Python|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-midoribox-python.md)|
|[SinTau SrL GROPIUS](http://www.sintau.it/index.php?option=com_virtuemart&view=productdetails&virtuemart_product_id=6&virtuemart_category_id=1&Itemid=360&lang=it) |Debian | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-gropius-c.md)|
|[SinTau SrL GROPIUS](http://www.sintau.it/index.php?option=com_virtuemart&view=productdetails&virtuemart_product_id=6&virtuemart_category_id=1&Itemid=360&lang=it) |Debian | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-gropius-java.md)|
|[Smarthesia PasSy Gateway](http://www.smarthesia.com/#!home-page/gj699) |Yocto| JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/yocto-PasSy-Gateway-nodejs.md)|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-sotec-cloudplug-c.md)|
|[Sparkfun ThingDev](https://www.sparkfun.com/products/13711) |Arduino IDE(RTOS)| Arduino, C|[Introduzione](https://azure.microsoft.com/documentation/samples/iot-hub-c-thingdev-getstartedkit/)|
|[Spreadtrum SC98xx](http://www.spreadtrum.com/en/SC9830A.html) |Android Marshmallow (v6.0.1)| Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/android-sc98xx-board-java.md)|
|[Supermicro SYS-E200-8B](https://www.supermicro.com.tw/products/system/Mini-ITX/SYS-E200-8B.cfm) |Windows 10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-sys-e200-8b-c.md)|
|[Supermicro SYS-E200-9B](https://www.supermicro.com.tw/products/system/Mini-ITX/SYS-E200-9B.cfm) |Windows 10 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-sys-e200-9b-c.md)|
|[Techman Robot Inc. TM5](http://tm-robot.com/robot/) |Windows Embedded Standard 7 | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-embedded-standard-7-tm5-c.md)|
|[Thecus Technology Corp. W2810PRO](http://wss.thecus.com/product_W2810PRO.php) |Windows Storage Server 2012 R2 Essentials | C#|[Guida introduttiva](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-storage-server-2012-r2-essentials-thecus w2810pro-csharp.md)|
|[Thecus Technology Corp. W5810](http://wss.thecus.com/product_W5810.php) |Windows Storage Server 2012 R2 Essentials | C#|[Guida introduttiva](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-storage-server-2012-r2-essentials-thecus w5810-csharp.md)|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ti-rtos-ti-cc3200-c.md)|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Java|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | JavaScript|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toshiba FAB-s320](http://www.toshiba-tops.co.jp/embedded/fab_p/index_j.html) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-FAB-s320-csharp.md)|
|[Trex NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-trex-ngp-csharp.md)|
|[Trueverit V4](http://www.trueverit.com/) |Linux personalizzato | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-trueverit-v4-c.md)|
|[USISH EDA8909](http://www.usish.com/) |Windows 10 | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-eda8909-csharp.md)|
|[VaneLife VHo1-X](http://www.vanelife.com/product/detail/2) |Linux Beaglebone | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-vho1-x-c.md)|
|[Vantron VT-M2M-BTA-DE](http://vantrontech.com/hardwares/VT-M2M-BTA-DE.htm) |Windows 10 IoT Core | C#|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-vt-m2m-bta-de-csharp.md)|
|[Vantron VT-M2M-LV](http://vantrontech.com/hardwares/VT-M2M-LV.htm) |Linux personalizzato | Python|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-vt-m2m-lv-python.md)|
|[YASKAWA MMLink-GW](http://www.ysknet.co.jp/product/type/networkboard/mmlink-gw/index.html) |Linux | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-MMLink-GW-c.md)|
|[Zerotech SMART](http://www.zerotech.com/smart-en.html) |Linaro | C|[Introduzione](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linaro-SMART-c.md)|

[Iniziare a usare questi dispositivi](https://azure.microsoft.com/develop/iot/get-started/) o visitare il [repository](https://github.com/Azure/azure-iot-sdks) GitHub e cercare i documenti dei dispositivi in base al linguaggio.

## Passaggi successivi

Altre informazioni sullo sviluppo di soluzioni che usano [dispositivi certificati per IoT](http://azure.com/iotdev).

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

- [Supportare protocolli aggiuntivi][lnk-protocols]
- [Eseguire il confronto con Hub eventi][lnk-compare]
- [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Gestire hub IoT tramite il portale di Azure][lnk-portal]


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->