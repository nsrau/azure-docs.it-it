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

L’hub IoT di Azure è un servizio di Azure che riceve dati di telemetria su scala dai dispositivi e indirizza tali dati a un processore di eventi di flusso. È possibile utilizzare l’hub IoT per implementare il proprio back-end di soluzione. IoT Hub può inoltre inviare comandi dal cloud al dispositivo a dispositivi specifici. Inoltre, l’hub IoT include un registro di identità del dispositivo che è possibile utilizzare per eseguire il provisioning dei dispositivi e per gestire i dispositivi che si possono connettere all’hub. Per altre informazioni, vedere:

- [Che cos'è IoT Hub?][lnk-iot-hub]
- [Iniziare a usare l'hub IoT][lnk-getstarted]

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si utilizzano gli SDK, è possibile scegliere tra una serie di protocolli di rete per comunicare con l’hub IoT. Per ulteriori informazioni, vedere [Informazioni sugli SDK del dispositivo][lnk-device-sdks].

Si potrebbe anche essere interessati a [Azure IoT Suite][lnk-iot-suite], che è una raccolta di soluzioni preconfigurate. IoT Suite consente di iniziare rapidamente e scalare progetti IoT per indirizzare scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->