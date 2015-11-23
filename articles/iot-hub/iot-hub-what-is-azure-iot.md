<properties
 pageTitle="Soluzioni di Azure per Internet delle cose | Microsoft Azure"
 description="Una panoramica di IoT in Azure, inclusa un'architettura della soluzione di esempio e la sua relazione con l’hub IoT di Azure, gli SDK per dispositivi e le soluzioni preconfigurate"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Passaggi successivi

È possibile usare Azure IoT Hub, un servizio di Azure che riceve i dati di telemetria a livello di scalabilità dai dispositivi ed instrada i dati a un processore di eventi di flusso, per implementare la soluzione di back-end. IoT Hub può inoltre inviare comandi dal cloud al dispositivo a dispositivi specifici. Inoltre, hub IoT include un registro di identità del dispositivo che è possibile utilizzare per eseguire il provisioning dei dispositivi e per gestire i dispositivi che si possono connettere all’hub. Per ulteriori informazioni, vedere:

- [Che cos'è IoT Hub?][lnk-iot-hub]
- [Iniziare a usare l'hub IoT][lnk-getstarted]

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si utilizzano SDK per dispositivi, è possibile scegliere tra una serie di protocolli di rete per comunicare con l’hub IoT. Per altre informazioni, vedere [Quali sono gli SDK dei dispositivi?][lnk-device-sdks].

Si potrebbe anche essere interessati ad [Azure IoT Suite][lnk-iot-suite], una raccolta di soluzioni preconfigurate che consentono di iniziare rapidamente e scalare progetti IoT per scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->