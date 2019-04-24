---
title: Sviluppare per dispositivi mobili tramite Azure IoT SDK | Microsoft Docs
description: Guida per gli sviluppatori - Informazioni sullo sviluppo per dispositivi mobili tramite gli SDK per l'hub IoT di Azure.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399369"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Sviluppare per dispositivi mobili tramite Azure IoT SDK

Gli elementi di Internet delle cose possono comprendere un'ampia gamma di dispositivi con funzionalità diverse: sensori, microcontroller, Smart Device, gateway industriali e persino dispositivi mobili.  Un dispositivo mobile può essere un dispositivo IoT, a cui si inviano i dati di telemetria da dispositivo a cloud ed è gestito dal cloud.  Può anche essere il dispositivo che esegue un'applicazione di servizio backend, che gestisce altri dispositivi IoT.  In entrambi i casi gli [SDK per l'hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) possono essere usati per sviluppare le applicazioni che funzionano sui dispositivi mobili.  

## <a name="develop-for-native-ios-platform"></a>Sviluppare per la piattaforma iOS nativa

Gli SDK per l'hub IoT di Azure offrono supporto per la piattaforma iOS nativa tramite l'SDK C per l'hub IoT di Azure.  Può essere considerato come un SDK per iOS che è possibile incorporare nel progetto Swift oppure Objective C XCode.  Esistono due modi per usare l'SDK C in iOS:

* Usare le librerie CocoaPod direttamente nel progetto XCode.  
* Scaricare il codice sorgente per l'SDK C ed eseguire la compilazione per la piattaforma iOS seguendo le [istruzioni di compilazione](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) per macOS.  

L'SDK C per l'hub IoT di Azure viene scritto in C99 per garantire la massima portabilità su diverse piattaforme.  Il processo di portabilità comporta la scrittura di un livello di adozione sottile per i componenti specifici della piattaforma, che sono disponibili qui per [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Le funzionalità dell'SDK C possono essere sfruttate nella piattaforma iOS, incluse le primitive dell'hub IoT di Azure supportate e le funzionalità specifiche dell'SDK, ad esempio i criteri di ripetizione per l'affidabilità della rete.  Anche l'interfaccia dell'SDK per iOS è simile all'interfaccia dell'SDK C per l'hub IoT di Azure.  

Questi documenti illustrano dettagliatamente come sviluppare un'applicazione per un dispositivo o per un servizio in un dispositivo iOS:

* [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-ios.md)  
* [Inviare messaggi dal cloud al dispositivo con l'hub IoT](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Sviluppare con le librerie CocoaPod dell'hub IoT di Azure

Gli SDK per l'hub IoT di Azure rilasciano un set di librerie CocoaPod di Objective-C per lo sviluppo in iOS.  Per visualizzare l'elenco più recente delle librerie CocoaPod, vedere [CocoaPods per Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Dopo aver incorporato le librerie pertinenti nel progetto XCode, è possibile scrivere il codice relativo all'hub IoT in due modi:

* Funzione Objective C: se il progetto è scritto in Objective-C è possibile chiamare le API direttamente dall'SDK C per l'hub IoT di Azure.  Se il progetto è scritto in Swift è possibile chiamare `@objc func` prima di creare la funzione e procedere alla scrittura di tutte le logiche relative all'hub IoT di Azure usando il codice C oppure Objective-C.  Un insieme di esempi che li illustra entrambi è disponibile nel [repository di esempio](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Incorporare gli esempi C: se è stata scritta un'applicazione per dispositivi C, è possibile usarla come riferimento direttamente nel progetto XCode:
    * Aggiungere il file sample.c al progetto XCode da XCode.  
    * Aggiungere il file di intestazione per la dipendenza.  Nel [repository di esempio](https://github.com/Azure-Samples/azure-iot-samples-ios) è incluso un file di intestazione come esempio. Per altre informazioni, visitare la pagina delle documentazioni di Apple per [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Sviluppare per la piattaforma Android
Azure IoT Hub Java SDK supporta la piattaforma Android.  Per la specifica versione API testata, visitare la [pagina del supporto della piattaforma](iot-hub-device-sdk-platform-support.md) e verificare l'aggiornamento più recente.

Questi documenti illustrano dettagliatamente come sviluppare un'applicazione per un dispositivo o per un servizio in un dispositivo Android usando Gradle e Android Studio:

* [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-android.md)  
* [Guida introduttiva: Controllare un dispositivo](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sulle API REST dell'hub IoT](https://docs.microsoft.com/rest/api/iothub/)
* [Codice sorgente dell'SDK C per l'hub IoT di Azure](https://github.com/Azure/azure-iot-sdk-c)
