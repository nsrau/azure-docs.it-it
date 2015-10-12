<properties
	pageTitle="Compatibilità delle piattaforme del sistema operativo e hardware per Azure IoT | Microsoft Azure"
	description="Compatibilità delle piattaforme del sistema operativo e hardware"
	services="iot-hub"
	documentationCenter="na"
	authors="hegate"
	manager="jamesosb"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="hegate"/>

# Compatibilità delle piattaforme del sistema operativo e hardware

Questo documento descrive la compatibilità degli SDK con diverse piattaforme del sistema operativo, nonché le configurazioni di dispositivi specifici incluse nel [programma di certificazione Microsoft Azure per IoT](#certified). Se si ha già un dispositivo, controllare l'elenco di dispositivi inclusi nel programma per trovare informazioni sulla compatibilità specifiche del dispositivo. Se non si è certi di quale dispositivo usare, vedere la sezione sulla compatibilità di [piattaforme del sistema operativo e librerie](#platforms).


## Piattaforme del sistema operativo

Le librerie Azure IoT sono state testate nelle piattaforme del sistema operativo seguenti:


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
|mbed OS| 2\.0 |
|TI-RTOS | 2\.x |



## Librerie C

L'[SDK per dispositivi Microsoft Azure IoT per C](c/device/readme.md) è stato testato nelle configurazioni seguenti:

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
L'[SDK per dispositivi Microsoft Azure IoT per Node.js](node/device/readme.md) è stato testato nelle configurazioni seguenti:


|Runtime| Versione|Protocolli|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Librerie Java
L'[SDK per dispositivi Microsoft Azure IoT per Java](java/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

L'[SDK per servizi Microsoft Azure IoT per Java](java/service/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp
L'[SDK per dispositivi Microsoft Azure IoT per .NET](csharp/readme.md) è stato testato nelle configurazioni seguenti:

|Piattaforma del sistema operativo| Versione|Protocolli|
|:---------|:----------:|:----------:|
|Desktop di Windows| 7, 8, 10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

Il codice dell'agente gestito richiede Microsoft .NET Framework 4.5


## Certificazione Microsoft Azure per IoT

Il programma di certificazione Microsoft Azure per IoT è il programma per i partner che mette in contatto un ampio ecosistema IoT con Microsoft Azure in modo che sviluppatori e architetti conoscano gli scenari di compatibilità. In particolare, fornisce un elenco attendibile di combinazioni di sistema operativo/dispositivo per iniziare rapidamente a usare un progetto IoT, indipendentemente dal fatto che ci si trovi in fase di modello di prova o pilota. Con le combinazioni certificate di dispositivo e sistema operativo, il progetto IoT può partire rapidamente, riducendo il lavoro e la personalizzazione necessari per garantire la compatibilità dei dispositivi con Azure IoT Suite e l'hub IoT di Azure.


## Dispositivi certificati per IoT

La compatibilità dei dispositivi certificati per IoT con gli SDK Azure IoT è stata testata e possono essere usati nell'applicazione IoT. In particolare, la compatibilità viene identificata in base alla piattaforma del sistema operativo e al linguaggio del codice.

#### Elenco di dispositivi

 Ogni dispositivo è stato certificato per l'uso dell'SDK nel sistema operativo e nel linguaggio scelti dal produttore del dispositivo. Ad esempio, BeagleBone Black funziona in Debian con il linguaggio C, Javascript e Java. Gli sviluppatori potranno quindi compilare le applicazioni in qualsiasi combinazione di linguaggio e sistema operativo sui dispositivi specifici.

## Passaggi successivi

Per altre informazioni sullo sviluppo di soluzioni che usano dispositivi certificati per IoT, fare clic [qui](http://azure.com/iotdev).

|Dispositivo| Sistema operativo testato |Linguaggio|
|:---------|:----------|:----------|
|Raspberry Pi 2| Raspbian | C, Javascript, Java |
|Raspberry Pi 2| Windows 10 IoT Core| C, Javascript, C#|
|BeagleBone Black| Debian |C, Javascript, Java|
|BeagleBone Green|Debian |C, Javascript, Java|
|TI CC3200 | TI-RTOS 2.x|C|
|Intel Edison |Yocto |C, Javascript|
|Minnowboard Max |Windows 7, 8, 10 |C#|
|Arrow Dragonboard |Windows 10 IoT Core | C#|
|Freescale FRDM K64 |mbedOS | C|

<!---HONumber=Oct15_HO1-->