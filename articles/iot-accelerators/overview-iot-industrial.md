---
title: Panoramica dell'IoT industriale di Azure | Microsoft Docs
description: Panoramica dell'IoT industriale
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b4fbfc1d9c9441c6c9f12987de5feb1a90e9f17f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256380"
---
# <a name="what-is-industrial-iot-iiot"></a>Informazioni sull'IoT industriale (IIoT)

IIoT è l'Internet delle cose per il settore industriale. Ottimizza l'efficienza industriale tramite l'applicazione dell'IoT nel settore produttivo. 

## <a name="improve-industrial-efficiencies"></a>Migliorare l'efficienza industriale

Migliorare la redditività e la produttività operative con un acceleratore di soluzione di fabbrica connessa. Connettere e monitorare le apparecchiature industriali e i dispositivi sul cloud, inclusi i computer già in funzione in fabbrica. Analizzare i dati dell'IoT per ottenere informazioni dettagliate utili per migliorare le prestazioni dell'intera fabbrica.

Ridurre le procedure lunghe e laboriose di accesso alle macchine della fabbrica con la gestione dispositivi OPC UA (OPC Twin) di Azure IoT e dedicare il tempo allo sviluppo di soluzioni IIoT. Semplificare la gestione dei certificati e l'integrazione di risorse aziendali con la gestione certificati OPC UA (OPC Vault) di Azure IoT con la certezza che la connettività delle risorse sia protetta. Questi microservizi forniscono un'API di tipo REST in aggiunta ai [componenti dell'IoT industriale di Azure](https://github.com/Azure/azure-iiot-opc-ua). L'API di servizi offre il controllo della funzionalità dei moduli edge. 

![Panoramica dell'IoT industriale](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Per altre informazioni sui servizi di IoT industriale di Azure, vedere il [repository ](https://github.com/Azure/azure-iiot-services) GitHub.
Se si ha poca familiarità con il funzionamento dei moduli IoT Edge di Azure, iniziare con gli articoli seguenti:
- [Informazioni su Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Moduli IoT Edge di Azure](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Connected factory

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) è un'implementazione dell'architettura di riferimento di IoT industriale di Microsoft Azure che può essere personalizzata per soddisfare requisiti di business specifici. Il codice completo della soluzione è open source ed è disponibile nel repository dell'acceleratore di soluzione Connected Factory di GitHub. È possibile usarlo come punto di partenza per un prodotto commerciale e distribuire una soluzione predefinita nella sottoscrizione di Azure in pochi minuti. 

## <a name="factory-floor-connectivity"></a>Connettività della fabbrica

Il servizio di gestione dispositivi OPC UA di Azure IoT, anche detto OPC Twin, è un componente dell'IIoT che automatizza l'individuazione e la registrazione dei dispositivi e offre il controllo remoto dei dispositivi industriali tramite API REST. OPC Twin usa Azure IoT Edge e l'hub IoT per connettere il cloud e la rete della fabbrica. OPC Twin consente agli sviluppatori di IIoT di concentrarsi sullo sviluppo di applicazioni IIoT senza preoccuparsi dell'accesso sicuro ai sistemi locali.

## <a name="security"></a>Security

Il servizio di gestione certificati OPC UA di Azure IoT, o OPC Vault, è un'implementazione di GDS (Global Discovery Server) OPC UA che consente di configurare, registrare e gestire il ciclo di vita dei certificati per le applicazioni server e client OPC UA nel cloud. OPC Vault semplifica l'implementazione e la manutenzione della connettività sicura delle risorse negli ambienti industriali. Automatizzando la gestione dei certificati, OPC Vault libera gli operatori della fabbrica dai complessi processi manuali associati alla connettività e alla gestione dei certificati.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver letto un'introduzione all'IoT industriale e ai relativi componenti, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Informazioni su OPC Twin](overview-opc-twin.md)
