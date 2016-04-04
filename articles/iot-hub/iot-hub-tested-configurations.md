<properties
	pageTitle="Compatibilità delle piattaforme del sistema operativo | Microsoft Azure"
	description="Viene riepilogata la compatibilità SDK per dispositivi IoT con la piattaforme del sistema operativo."
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
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Compatibilità delle piattaforme del sistema operativo con gli SDK per dispositivi

In questo documento viene descritta la compatibilità SDK con diverse piattaforme del sistema operativo. Se non si è certi di quale dispositivo usare, vedere la sezione sulla compatibilità di [piattaforme del sistema operativo e librerie](#os-platforms) in questo articolo.

## Programma Microsoft Azure Certified per IoT

Se si ha già un dispositivo, controllare l'elenco di dispositivi inclusi nel programma [Microsoft Azure Certified per IoT][lnk-certified] per trovare informazioni sulla compatibilità specifiche del dispositivo. Il programma di certificazione Microsoft Azure per IoT è il programma per i partner che mette in contatto un ampio ecosistema IoT con Microsoft Azure in modo che sviluppatori e architetti conoscano gli scenari di compatibilità. In particolare, fornisce un elenco attendibile di combinazioni di sistema operativo/dispositivo per iniziare rapidamente a usare un progetto IoT, indipendentemente dal fatto che ci si trovi in fase di modello di prova o pilota

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
|Desktop di Windows| 10 |
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
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Desktop di Windows| 10 | HTTPS, AMQP, MQTT, AMQP su WebSocket |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Librerie Node.js

L'[SDK per dispositivi Microsoft Azure IoT per Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) è stato testato nelle configurazioni seguenti:


|Runtime| Versione|Protocolli|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP su WebSocket |



## Librerie Java

L'[SDK per dispositivi Microsoft Azure IoT per Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) è stato testato nelle configurazioni seguenti:

|Runtime| Versione|Protocolli|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

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

Il codice dell'agente gestito richiede Microsoft .NET Framework 4.5


## Passaggi successivi

- Altre informazioni sul programma [Microsoft Azure Certified per IoT][lnk-certified].
- Altre informazioni sullo sviluppo di soluzioni che usano [dispositivi certificati per IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->