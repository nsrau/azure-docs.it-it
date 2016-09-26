<properties
 pageTitle="Elenco degli SDK hub IoT Azure | Microsoft Azure"
 description="Informazioni e collegamenti ai vari SDK per dispositivi e servizi hub IoT di Azure."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>  

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/13/2016"
 ms.author="dobett"/>  

# SDK hub IoT

In questo articolo vengono fornite informazioni sui vari [SDK IoT di Microsoft Azure][] con collegamenti a risorse aggiuntive.

## SDK per dispositivi hub IoT

GLI SDK per dispositivi IoT di Microsoft Azure contengono codice che facilita la compilazione dei dispositivi e delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

I seguenti SDK per dispositivi IoT sono disponibili per il download da GitHub:

- [SDK per dispositivi IoT Azure per C][] scritto in ANSI C (C99) per la portabilità e la compatibilità multipiattaforma.
- [SDK per dispositivi IoT Azure per .NET][]
- [SDK per dispositivi IoT Azure per Java][]
- [SDK per dispositivi IoT Azure per Node.js][]
- [Microsoft Azure IoT SDK per dispositivi per Python 2.7][]

### Compatibilità delle piattaforme del sistema operativo e hardware

Per altre informazioni sulla compatibilità con specifici dispositivi hardware, vedere gli articoli seguenti:

- [Compatibilità delle piattaforme del sistema operativo e hardware con gli SDK per dispositivi][OS Platforms and hardware compatibility]
- [Programma Microsoft Azure Certified per IoT][].

## SDK servizio hub IoT

GLi SDK del servizio IoT Microsoft Azure contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire i dispositivi e la sicurezza.

I seguenti IoT SDK per servizi sono disponibili per il download da GitHub:

- [SDK del servizio IoT Azure SDK per Node.js][]
- [SDK del servizio IoT Azure SDK per Java][]

## SDK per gateway IoT di Azure

Questo SDK per gateway IoT di Azure contiene l'infrastruttura e i moduli per creare soluzioni gateway IoT. È possibile estendere l'SDK per la creazione di gateway su misura per qualsiasi scenario end-to-end.

L'[SDK per gateway IoT di Azure][] può essere scaricato da GitHub.

## Documentazione di riferimento API online

Di seguito è riportato un elenco di collegamenti alla documentazione di riferimento all'API online per librerie di dispositivi, servizi e gateway di Azure IoT:

- [Internet of Things (IoT) .NET][]
- [REST hub IoT][]
- [SDK per dispositivi Microsoft Azure IoT per C][]
- [SDK per dispositivi Microsoft Azure IoT per Java][]
- [SDK del servizio IoT di Microsoft Azure per Java][]
- [SDK per dispositivi Microsoft Azure IoT per Node.js][]
- [SDK del servizio IoT di Microsoft Azure per Node.js][]
- [IoT Gateway SDK di Microsoft Azure][]

## Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Esplorare la Gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Gestire hub IoT tramite il portale di Azure][lnk-portal]

[SDK IoT di Microsoft Azure]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[SDK per dispositivi IoT Azure per C]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[SDK per dispositivi IoT Azure per .NET]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[SDK per dispositivi IoT Azure per Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[SDK del servizio IoT Azure SDK per Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[SDK per dispositivi IoT Azure per Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[SDK del servizio IoT Azure SDK per Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[Microsoft Azure IoT SDK per dispositivi per Python 2.7]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[Programma Microsoft Azure Certified per IoT]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[SDK per gateway IoT di Azure]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[Internet of Things (IoT) .NET]: https://msdn.microsoft.com/library/mt488521.aspx
[SDK per dispositivi Microsoft Azure IoT per C]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[SDK per dispositivi Microsoft Azure IoT per Java]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[SDK per dispositivi Microsoft Azure IoT per Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.14/index.html
[REST hub IoT]: https://msdn.microsoft.com/library/mt548492.aspx
[SDK del servizio IoT di Microsoft Azure per Java]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[SDK del servizio IoT di Microsoft Azure per Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.16/index.html
[IoT Gateway SDK di Microsoft Azure]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->