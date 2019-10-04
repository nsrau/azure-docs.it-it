---
title: 'Gateway per dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: Azure IoT Edge può essere usato per creare un dispositivo gateway trasparente, opaco o proxy che invia i dati da più dispositivi downstream al cloud o li elabora in locale.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3a49c4333838652f7063d6a89cfd8cceace1cf8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054190"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Come usare un dispositivo Azure IoT Edge come gateway

I gateway nelle soluzioni IoT Edge forniscono connettività dei dispositivi e analitica edge ai dispositivi IoT che altrimenti non sarebbero necessario tali funzionalità. Azure IoT Edge consente di soddisfare qualsiasi esigenza per un gateway IoT, indipendentemente dal fatto che sia correlata alla connettività, alla gestione delle identità o all'analisi perimetrale. I modelli di gateway descritti in questo articolo fanno riferimento solo alle caratteristiche di identità e connettività dei dispositivi downstream e non al modo in cui i dati dei dispositivi vengono elaborati nel gateway.

## <a name="patterns"></a>Modelli

Esistono tre modelli per l'uso di un dispositivo IoT Edge come gateway: trasparente, conversione di protocollo e conversione di identità:
* **Trasparente**: i dispositivi che in teoria possono connettersi all'hub IoT supportano anche la connessione a un dispositivo gateway. I dispositivi downstream hanno specifiche identità nell'hub IoT e usano uno dei protocolli MQTT, AMQP e HTTP. Il gateway passa semplicemente le comunicazioni tra i dispositivi e l'hub IoT. I dispositivi sono a conoscenza che stanno comunicando con il cloud tramite un gateway e un utente che interagisce con i dispositivi nell'IoT Hub è a conoscenza del dispositivo gateway intermedio. Il gateway è quindi trasparente. Vedere come [creare un gateway trasparente](how-to-create-transparent-gateway.md) per informazioni specifiche sull'uso di un dispositivo IoT Edge come gateway trasparente.
* **Conversione di protocollo**: nota anche come modello di gateway opaco. I dispositivi che non supportano MQTT, AMQP o HTTP usano un dispositivo gateway per inviare dati all'hub IoT. Il gateway riconosce il protocollo usato dai dispositivi downstream ed è l'unico dispositivo dispone di un'identità nell'IoT Hub. Tutte le informazioni sembrano provenire da un unico dispositivo, il gateway. I dispositivi downstream devono incorporare informazioni di identificazione aggiuntive nei messaggi se le applicazioni cloud prevedono di analizzare i dati per singolo dispositivo. Inoltre, le primitive dell'hub IoT, come i dispositivi gemelli e i metodi, sono disponibili solo per il dispositivo gateway e non per i dispositivi downstream.
* **Conversione di identità**: i dispositivi che non possono connettersi all'hub IoT si connettono invece a un dispositivo gateway. Il gateway fornisce la conversione di protocollo e identità dell'hub IoT per conto dei dispositivi downstream. Il gateway è abbastanza intelligente da identificare il protocollo usato dai dispositivi downstream, fornire loro l'identità e convertire le primitive dell'hub IoT. I dispositivi downstream appaiono nell'hub IoT come dispositivi di prima classe con dispositivi gemelli e metodi. Un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio.

![Diagramma - Modelli di gateway trasparente, di protocollo e di identità](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casi d'uso
Tutti i modelli di gateway offrono i vantaggi seguenti:
* **Analitica nei dispositivi perimetrali** – servizi di intelligenza artificiale di uso in locale per elaborare i dati provenienti dai dispositivi downstream senza inviare i dati di telemetria di fedeltà nel cloud. Trovare e gestire le informazioni dettagliate in locale e inviare solo un subset di dati all'hub IoT. 
* **Isolamento dei dispositivi downstream**: il dispositivo gateway può proteggere tutti i dispositivi downstream dall'esposizione a Internet. Può essere posizionato tra una rete OT priva di connettività e una rete IT con accesso al Web. 
* **Multiplexing delle connessioni**: tutti i dispositivi connessi all'hub IoT tramite un gateway IoT Edge usano la stessa connessione sottostante.
* **Smorzamento del traffico**: il dispositivo IoT Edge esegue automaticamente il backoff esponenziale se l'hub IoT limita il traffico, salvando al contempo i messaggi in locale. Questo vantaggio rende la soluzione resiliente ai picchi di traffico.
* **Supporto offline** : il dispositivo gateway archivia i messaggi e gli aggiornamenti dei dispositivi gemelli non possono essere recapitati all'IoT Hub.

Un gateway che esegue la conversione di protocollo può anche offrire l'analisi perimetrale, l'isolamento dei dispositivi, lo smorzamento del traffico e il supporto offline a dispositivi nuovi ed esistenti con vincoli di risorse. Molti dispositivi esistenti generano dati che consentono di ottenere una visione più approfondita dei dati aziendali, ma non sono stati progettati tenendo presente la connettività cloud. I gateway opachi consentono questi dati di essere sbloccate e utilizzato in una soluzione IoT.

Un gateway che esegue la conversione di identità offre i vantaggi della conversione di protocollo e supporta inoltre la gestibilità completa dei dispositivi downstream dal cloud. Tutti i dispositivi della soluzione IoT appaiono nell'hub IoT indipendentemente dal protocollo usato.

## <a name="cheat-sheet"></a>Tabella di riepilogo
Questa tabella di riepilogo mette a confronto le primitive dell'hub IoT quando vengono usati gateway trasparenti, opachi (protocollo) e proxy.

| &nbsp; | Gateway trasparente | Conversione di protocollo | Conversione di identità |
|--------|-------------|--------|--------|
| Identità archiviate nel registro delle identità dell'hub IoT | Identità di tutti i dispositivi connessi | Solo l'identità del dispositivo gateway | Identità di tutti i dispositivi connessi |
| Dispositivo gemello | Ogni dispositivo connesso ha il proprio dispositivo gemello | Solo il gateway ha dispositivi e moduli gemelli | Ogni dispositivo connesso ha il proprio dispositivo gemello |
| Metodi diretti e messaggi da cloud a dispositivo | Il cloud può indirizzare ogni singolo dispositivo connesso | Il cloud può indirizzare solo il dispositivo gateway | Il cloud può indirizzare ogni singolo dispositivo connesso |
| [Quote e limitazioni dell'hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Si applicano a ogni dispositivo | Si applicano al dispositivo gateway | Si applicano a ogni dispositivo |

Quando si usa un modello di gateway opaco (conversione di protocollo), tutti i dispositivi connessi tramite tale gateway condividono la stessa coda da cloud a dispositivo, che può contenere al massimo 50 messaggi. Ne consegue che il modello di gateway opaco va usato solo quando i dispositivi che si connettono tramite ogni gateway sul campo sono pochi e il relativo traffico da cloud a dispositivo è limitato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare un gateway trasparente: 

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream all'IoT Hub di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)