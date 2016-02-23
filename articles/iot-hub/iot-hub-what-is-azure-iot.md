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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Passaggi successivi

Hub IoT di Azure è un servizio che consente comunicazioni bidirezionali affidabili e sicure tra il back-end dell'applicazione e milioni di dispositivi. Consente all'applicazione back-end di ricevere dati di telemetria su larga scala dai dispositivi, instradare i dati a un processore di eventi di flusso e inviare comandi da cloud a dispositivo a specifici dispositivi. È possibile usate l'hub IoT per implementare il back-end della propria soluzione. L'hub IoT include anche un registro delle identità dei dispositivi che è possibile usare per effettuare il provisioning dei dispositivi, le credenziali di sicurezza e i relativi diritti per connettersi all'hub. Per ulteriori informazioni, vedere:

- [Che cos'è IoT Hub?][lnk-iot-hub]
- [Iniziare a usare l'hub IoT][lnk-getstarted]

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si utilizzano gli SDK, è possibile scegliere tra una serie di protocolli di rete per comunicare con l’hub IoT. Per ulteriori informazioni, vedere [Informazioni sugli SDK del dispositivo][lnk-device-sdks].

Si potrebbe anche essere interessati a [Azure IoT Suite][lnk-iot-suite], che è una raccolta di soluzioni preconfigurate. IoT Suite consente di iniziare rapidamente e scalare progetti IoT per indirizzare scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0218_2016-->