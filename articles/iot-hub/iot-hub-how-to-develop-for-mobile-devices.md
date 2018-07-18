---
title: Sviluppare per dispositivi mobili tramite Azure IoT SDK | Microsoft Docs
description: Guida per gli sviluppatori - Informazioni sullo sviluppo per dispositivi mobili tramite gli SDK per l'hub IoT di Azure.
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634948"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Sviluppare per dispositivi mobili tramite Azure IoT SDK

Gli elementi di Internet delle cose possono comprendere un'ampia gamma di dispositivi con funzionalità diverse: sensori, microcontroller, Smart Device, gateway industriali e persino dispositivi mobili.  Un dispositivo mobile può essere un dispositivo IoT, a cui si inviano i dati di telemetria da dispositivo a cloud ed è gestito dal cloud.  Può anche essere il dispositivo che esegue un'applicazione di servizio backend, che gestisce altri dispositivi IoT.  In entrambi i casi gli [SDK per l'hub IoT di Azure][lnk-sdk-overview] possono essere usati per sviluppare le applicazioni che funzionano sui dispositivi mobili.  

## <a name="develop-for-native-ios-platform"></a>Sviluppare per la piattaforma iOS nativa

Gli SDK per l'hub IoT di Azure offrono supporto per la piattaforma iOS nativa tramite l'SDK C per l'hub IoT di Azure.  Può essere considerato come un SDK per iOS che è possibile incorporare nel progetto Swift oppure Objective C XCode.  Esistono due modi per usare l'SDK C in iOS:
- Usare le librerie CocoaPod direttamente nel progetto XCode.  
- Scaricare il codice sorgente per l'SDK C ed eseguire la compilazione per la piattaforma iOS seguendo le [istruzioni di compilazione][lnk-c-devbox] per MacOS.  

L'SDK C per l'hub IoT di Azure viene scritto in C99 per garantire la massima portabilità su diverse piattaforme.  Il processo di portabilità comporta la scrittura di un livello di adozione sottile per i componenti specifici della piattaforma, che sono disponibili qui per [iOS][lnk-ios-pal].  Le funzionalità dell'SDK C possono essere sfruttate nella piattaforma iOS, incluse le primitive dell'hub IoT di Azure supportate e le funzionalità specifiche dell'SDK, ad esempio i criteri di ripetizione per l'affidabilità della rete.  Anche l'interfaccia dell'SDK per iOS è simile all'interfaccia dell'SDK C per l'hub IoT di Azure.  

Questi documenti illustrano dettagliatamente come sviluppare un'applicazione per un dispositivo o per un servizio in un dispositivo iOS:
- [Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub IoT][lnk-device-ios-quickstart]  
- [Inviare messaggi dal cloud al dispositivo con l'hub IoT][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Sviluppare con le librerie CocoaPod dell'hub IoT di Azure

Gli SDK per l'hub IoT di Azure rilasciano un set di librerie CocoaPod di Objective-C per lo sviluppo in iOS.  Per visualizzare l'elenco più recente delle librerie CocoaPod, vedere [CocoaPods per Microsoft Azure IoT][lnk-cocoapod-list].  Dopo aver incorporato le librerie pertinenti nel progetto XCode, è possibile scrivere il codice relativo all'hub IoT in due modi:
- Funzione Objective C: se il progetto è scritto in Objective-C è possibile chiamare le API direttamente dall'SDK C per l'hub IoT di Azure.  Se il progetto è scritto in Swift è possibile chiamare ``@objc func`` prima di creare la funzione e procedere alla scrittura di tutte le logiche relative all'hub IoT di Azure usando il codice C oppure Objective-C.  Un insieme di esempi che li illustra entrambi è disponibile nella [repository degli esempi][lnk-ios-samples-repo].  
- Incorporare gli esempi C: se è stata scritta un'applicazione per dispositivi C è possibile usarla come riferimento direttamente nel progetto XCode:
    - Aggiungere il file sample.c al progetto XCode da XCode.  
    - Aggiungere il file di intestazione per la dipendenza.  Nel [repository degli esempi][lnk-ios-samples-repo] è incluso un file di intestazione come esempio.  Per altre informazioni, visitare la pagina delle documentazioni di Apple per [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni di riferimento sulle API REST dell'hub IoT][lnk-rest-ref]
* [Codice sorgente dell'SDK C per l'hub IoT di Azure][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
