---
title: Informazioni su come usare un dispositivo Azure IoT Edge come gateway per altri dispositivi | Microsoft Docs
description: "Azure IoT Edge può essere usato per creare un dispositivo gateway trasparente, opaco o proxy che invia i dati da più dispositivi downstream al cloud o li elabora in locale."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3f2f9258b97d4886f41a2b991ff4de7e16379245
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Come usare un dispositivo Azure IoT Edge come gateway - anteprima

Nelle soluzioni IoT i gateway vengono usati per uno scopo specifico della soluzione e offrono funzionalità per la connettività dei dispositivi e l'analisi perimetrale. Azure IoT Edge consente di soddisfare qualsiasi esigenza per un gateway IoT, indipendentemente dal fatto che sia correlata alla connettività, alla gestione delle identità o all'analisi perimetrale. I modelli di gateway descritti in questo articolo fanno riferimento solo alle caratteristiche di identità e connettività dei dispositivi downstream e non al modo in cui i dati dei dispositivi vengono elaborati nel gateway.

## <a name="patterns"></a>Modelli
Esistono tre modelli per l'uso di un dispositivo IoT Edge come gateway: trasparente, conversione di protocollo e conversione di identità:
* **Trasparente**: i dispositivi che in teoria possono connettersi all'hub IoT supportano anche la connessione a un dispositivo gateway. Ciò implica che i dispositivi downstream abbiano specifiche identità nell'hub IoT e usino uno dei protocolli MQTT, AMQP e HTTP. Il gateway passa semplicemente le comunicazioni tra i dispositivi e l'hub IoT. I dispositivi non sono consapevoli di comunicare con il cloud tramite un gateway e gli utenti che interagiscono con i dispositivi nell'hub IoT non sono a conoscenza del dispositivo gateway intermedio. Il gateway è quindi trasparente. Vedere la procedura [Creare un gateway trasparente] [ lnk-iot-edge-as-transparent-gateway] per informazioni specifiche sull'uso di un dispositivo IoT Edge come gateway trasparente.
* **Conversione di protocollo**: i dispositivi che non supportano MQTT, AMQP o HTTP usano un dispositivo gateway per inviare dati all'hub IoT. Il gateway è abbastanza intelligente da identificare il protocollo usato dai dispositivi downstream, ma è l'unico dispositivo con un'identità nell'hub IoT. Tutte le informazioni sembrano provenire da un unico dispositivo, il gateway. I dispositivi downstream devono pertanto incorporare informazioni di identificazione aggiuntive nei messaggi se le applicazioni cloud prevedono di elaborare i dati per singolo dispositivo. Inoltre, le primitive dell'hub IoT, come i dispositivi gemelli e i metodi, sono disponibili solo per il dispositivo gateway e non per i dispositivi downstream.
* **Conversione di identità**: i dispositivi che non possono connettersi all'hub IoT si connettono a un dispositivo gateway che fornisce la conversione di protocollo e identità dell'hub IoT per conto dei dispositivi downstream. Il gateway è abbastanza intelligente da identificare il protocollo usato dai dispositivi downstream, fornire loro l'identità e convertire le primitive dell'hub IoT. I dispositivi downstream appaiono nell'hub IoT come dispositivi di prima classe con dispositivi gemelli e metodi. Un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio.

![Diagrammi dei modelli di gateway][1]

## <a name="use-cases"></a>Casi d'uso
Tutti i modelli di gateway offrono i vantaggi seguenti:
* **Analisi perimetrale**: usare i servizi di intelligenza artificiale in locale per elaborare i dati provenienti dai dispositivi downstream senza inviare al cloud i dati di telemetria completi. Trovare e gestire le informazioni dettagliate in locale e inviare solo un subset di dati all'hub IoT. 
* **Isolamento dei dispositivi downstream**: il dispositivo gateway può proteggere tutti i dispositivi downstream dall'esposizione a Internet. Può essere posizionato tra una rete OT priva di connettività e una rete IT con accesso al Web. 
* **Multiplexing delle connessioni**: tutti i dispositivi connessi all'hub IoT tramite un dispositivo IoT Edge usano la stessa connessione sottostante.
* **Smorzamento del traffico**: il dispositivo IoT Edge esegue automaticamente il backoff esponenziale in caso di limitazione dell'hub IoT, salvando al contempo i messaggi in locale. Ciò rende la soluzione resiliente ai picchi di traffico.
* **Supporto offline limitato**: il dispositivo gateway archivia in locale i messaggi che non possono essere recapitati all'hub IoT.

Un gateway che esegue la conversione di protocollo può anche fornire l'analisi perimetrale, l'isolamento dei dispositivi, lo smorzamento del traffico e il supporto offline a dispositivi nuovi ed esistenti con vincoli di risorse. Molti dispositivi esistenti generano dati che consentono di ottenere una visione più approfondita dei dati aziendali, ma non sono stati progettati tenendo presente la connettività cloud. I gateway opachi consentono di individuare e sfruttare questi dati in una soluzione IoT end-to-end.

Un gateway che esegue la conversione di identità offre i vantaggi della conversione di protocollo e supporta inoltre la gestibilità completa dei dispositivi downstream dal cloud. Tutti i dispositivi della soluzione IoT appaiono nell'hub IoT indipendentemente dal protocollo usato.

## <a name="cheat-sheet"></a>Tabella di riepilogo
Questa tabella di riepilogo mette a confronto le primitive dell'hub IoT quando vengono usati gateway trasparenti, opachi e proxy.

| &nbsp; | Gateway trasparente | Conversione di protocollo | Conversione di identità |
|--------|-------------|--------|--------|
| Identità archiviate nel registro delle identità dell'hub IoT | Identità di tutti i dispositivi connessi | Solo l'identità del dispositivo gateway | Identità di tutti i dispositivi connessi |
| Dispositivo gemello | Ogni dispositivo connesso ha il proprio dispositivo gemello | Solo il gateway ha dispositivi e moduli gemelli | Ogni dispositivo connesso ha il proprio dispositivo gemello |
| Metodi diretti e messaggi da cloud a dispositivo | Il cloud può indirizzare ogni singolo dispositivo connesso | Il cloud può indirizzare solo il dispositivo gateway | Il cloud può indirizzare ogni singolo dispositivo connesso |
| [Quote e limitazioni dell'hub IoT][lnk-iothub-throttles-quotas] | Si applicano a ogni dispositivo | Si applicano al dispositivo gateway | Si applicano a ogni dispositivo |

Quando si usa un modello di gateway opaco (conversione di protocollo), tutti i dispositivi connessi tramite tale gateway condividono la stessa coda da cloud a dispositivo, che può contenere al massimo 50 messaggi. Ne consegue che il modello di gateway opaco va usato solo quando i dispositivi che si connettono tramite ogni gateway sul campo sono pochi e il relativo traffico da cloud a dispositivo è limitato.

## <a name="next-steps"></a>Passaggi successivi
Usare un dispositivo IoT Edge come [gateway trasparente][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
