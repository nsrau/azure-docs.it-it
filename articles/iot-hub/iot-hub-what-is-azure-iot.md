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
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Ambito di questa documentazione

Questi articoli su Azure e IoT si concentrano su due raccolte di risorse che consentono di implementare la propria soluzione IoT basata sulla piattaforma Microsoft IoT.

- Hub IoT Azure
- SDK dispositivo IoT Azure

Si potrebbe anche essere interessati ad [Azure IoT Suite][lnk-iot-suite], una raccolta di soluzioni preconfigurate che consentono di iniziare rapidamente e scalare progetti IoT per scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

### Hub IoT Azure

Hub IoT è un servizio di Azure che riceve dati da dispositivo a cloud su scala dai dispositivi e di indirizzare tali dati a un processore di eventi di flusso. Hub IoT può inoltre inviare comandi da cloud a dispositivo a dispositivi specifici mediante code specifiche per il dispositivo.

Inoltre, il servizio hub IoT include un registro di identità del dispositivo che è possibile utilizzare per eseguire il provisioning dei dispositivi e di gestire i dispositivi che si possono connettere a un hub IoT.

### SDK dispositivo IoT Azure

Microsoft fornisce SDK per dispositivi IoT che è possibile utilizzare per implementare le applicazioni client per l'esecuzione su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria da dispositivo a cloud all’hub IoT e la ricezione di comandi da cloud a dispositivo dall’hub IoT. Quando si utilizzano SDK per dispositivi, è possibile scegliere tra una serie di protocolli di rete diversi per comunicare con l’hub IoT di Azure.

## Passaggi successivi

Per iniziare a usare IoT su Azure, vedere le risorse seguenti:

- [Iniziare a usare l'hub IoT][lnk-getstarted]
- [Centro per sviluppatori Azure IoT][lnk-iotdev]
- [Hub IoT Azure][lnk-iot-hub]
- [Azure IoT Suite][lnk-iot-suite]  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Oct15_HO3-->