---
title: 'Gateway per dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: Azure IoT Edge può essere usato per creare un dispositivo gateway trasparente, opaco o proxy che invia i dati da più dispositivi downstream al cloud o li elabora in locale.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017023"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Come usare un dispositivo Azure IoT Edge come gateway

I gateway nelle soluzioni IoT Edge forniscono connettività dei dispositivi e analisi perimetrale ai dispositivi Internet che altrimenti non avrebbero le stesse funzionalità. Azure IoT Edge può essere usato per soddisfare qualsiasi esigenza di un gateway Internet delle cose, sia per quanto riguarda la connettività, l'identità o l'analisi perimetrale. I modelli di gateway descritti in questo articolo fanno riferimento solo alle caratteristiche di identità e connettività dei dispositivi downstream e non al modo in cui i dati dei dispositivi vengono elaborati nel gateway.

## <a name="patterns"></a>Modelli

Sono disponibili tre modelli per l'uso di un dispositivo IoT Edge come gateway: trasparente, conversione di protocollo e conversione di identità.

Una differenza fondamentale tra i modelli è che un gateway trasparente passa messaggi tra i dispositivi downstream e l'hub Internet senza che sia necessaria alcuna elaborazione aggiuntiva. La conversione di protocollo e di identità, tuttavia, richiede l'elaborazione sul gateway per consentire la comunicazione.

Qualsiasi gateway può usare moduli di IoT Edge per eseguire analisi o pre-elaborazione sul perimetro prima di passare i messaggi dai dispositivi downstream all'hub Internet.

![Diagramma - Modelli di gateway trasparente, di protocollo e di identità](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Modello trasparente

In un modello di gateway *trasparente* , i dispositivi che teoricamente potrebbero connettersi all'hub Internet possono connettersi a un dispositivo gateway. I dispositivi downstream hanno le proprie identità dell'hub IoT e usano il protocollo MQTT, AMQP o HTTP. Il gateway passa semplicemente le comunicazioni tra i dispositivi e l'hub IoT. Sia i dispositivi che gli utenti che interagiscono con loro tramite l'hub Internet non sanno che un gateway sta mediando le comunicazioni. Questa mancanza di consapevolezza significa che il gateway è considerato *trasparente*.

Il runtime di IoT Edge include funzionalità gateway trasparenti. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).

### <a name="protocol-translation-pattern"></a>Modello di conversione del protocollo

Un gateway di *conversione del protocollo* è noto anche come gateway *opaco* , a differenza del modello di gateway trasparente. In questo modello, i dispositivi che non supportano MQTT, AMQP o HTTP possono usare un dispositivo gateway per inviare i dati all'hub Internet per conto dell'utente. Il gateway riconosce il protocollo usato dai dispositivi downstream ed è l'unico dispositivo che ha un'identità nell'hub IoT. Tutte le informazioni sembrano provenire da un unico dispositivo, il gateway. I dispositivi downstream devono incorporare informazioni di identificazione aggiuntive nei messaggi se le applicazioni cloud vogliono analizzare i dati in base ai singoli dispositivi. Le primitive dell'hub IoT, come gemelli e metodi, sono inoltre disponibili solo per il dispositivo gateway, non per i dispositivi downstream.

Il runtime di IoT Edge non include le funzionalità di conversione del protocollo. Questo modello richiede moduli personalizzati o di terze parti spesso specifici dell'hardware e del protocollo utilizzati. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contiene diversi moduli di conversione del protocollo tra cui scegliere.

### <a name="identity-translation-pattern"></a>Modello di conversione identità

In un modello di gateway per la *conversione delle identità* , i dispositivi che non possono connettersi all'hub Internet possono connettersi a un dispositivo gateway. Il gateway fornisce la conversione di protocollo e di identità dell'hub IoT per conto dei dispositivi downstream. Il gateway è sufficientemente intelligente da comprendere il protocollo usato dai dispositivi downstream, fornire l'identità e convertire le primitive dell'hub IoT. I dispositivi downstream vengono visualizzati nell'hub IoT come dispositivi di prima classe con gemelli e metodi. Un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio.

Il runtime di IoT Edge non include le funzionalità di conversione delle identità. Questo modello richiede moduli personalizzati o di terze parti spesso specifici dell'hardware e del protocollo utilizzati. Per un esempio che usa il modello di conversione delle identità, vedere [Azure IOT Edge Starter Kit LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Casi d'uso

Tutti i modelli di gateway offrono i vantaggi seguenti:

* **Analisi alla periferia** : usare i servizi di intelligenza artificiale localmente per elaborare i dati provenienti dai dispositivi downstream senza inviare i dati di telemetria con la massima fedeltà al cloud. Trovare e gestire le informazioni dettagliate in locale e inviare solo un subset di dati all'hub IoT.
* **Isolamento dei dispositivi downstream**: il dispositivo gateway può proteggere tutti i dispositivi downstream dall'esposizione a Internet. Può essere posizionato tra una rete OT priva di connettività e una rete IT con accesso al Web.
* **Multiplexing delle connessioni**: tutti i dispositivi connessi all'hub IoT tramite un gateway IoT Edge usano la stessa connessione sottostante.
* **Smorzamento del traffico**: il dispositivo IoT Edge esegue automaticamente il backoff esponenziale se l'hub IoT limita il traffico, salvando al contempo i messaggi in locale. Questo vantaggio rende la soluzione resiliente ai picchi di traffico.
* **Supporto offline** : il dispositivo gateway archivia i messaggi e gli aggiornamenti dei dispositivi gemelli che non possono essere recapitati all'hub Internet.

Un gateway che esegue la conversione di protocollo può supportare i dispositivi esistenti e i nuovi dispositivi con vincoli di risorse. Molti dispositivi esistenti generano dati che consentono di ottenere una visione più approfondita dei dati aziendali, ma non sono stati progettati tenendo presente la connettività cloud. I gateway opachi consentono di sbloccare i dati e usarli in una soluzione Internet delle cose.

Un gateway che esegue la conversione di identità offre i vantaggi della conversione di protocollo e supporta inoltre la gestibilità completa dei dispositivi downstream dal cloud. Tutti i dispositivi della soluzione IoT appaiono nell'hub IoT indipendentemente dal protocollo usato.

## <a name="cheat-sheet"></a>Tabella di riepilogo

Questa tabella di riepilogo mette a confronto le primitive dell'hub IoT quando vengono usati gateway trasparenti, opachi (protocollo) e proxy.

| Primitiva | Gateway trasparente | Conversione di protocollo | Conversione di identità |
|--------|-------------|--------|--------|
| Identità archiviate nel registro delle identità dell'hub IoT | Identità di tutti i dispositivi connessi | Solo l'identità del dispositivo gateway | Identità di tutti i dispositivi connessi |
| Dispositivo gemello | Ogni dispositivo connesso ha il proprio dispositivo gemello | Solo il gateway ha dispositivi e moduli gemelli | Ogni dispositivo connesso ha il proprio dispositivo gemello |
| Metodi diretti e messaggi da cloud a dispositivo | Il cloud può indirizzare ogni singolo dispositivo connesso | Il cloud può indirizzare solo il dispositivo gateway | Il cloud può indirizzare ogni singolo dispositivo connesso |
| [Quote e limitazioni dell'hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Si applicano a ogni dispositivo | Si applicano al dispositivo gateway | Si applicano a ogni dispositivo |

Quando si usa un modello di gateway opaco (conversione di protocollo), tutti i dispositivi connessi tramite tale gateway condividono la stessa coda da cloud a dispositivo, che può contenere al massimo 50 messaggi. Ne consegue che il modello di gateway opaco va usato solo quando i dispositivi che si connettono tramite ogni gateway sul campo sono pochi e il relativo traffico da cloud a dispositivo è limitato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui tre passaggi per configurare un gateway trasparente:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
