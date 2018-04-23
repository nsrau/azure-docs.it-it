---
title: Domande frequenti sul monitoraggio remoto Azure IoT Suite | Microsoft Docs
description: Domande frequenti sulla soluzione preconfigurata di monitoraggio remoto IoT Suite
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b0d6dbb567f66537c599d7ac3e4337988b3374c0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite-remote-monitoring-preconfigured-solution"></a>Domande frequenti sulla soluzione preconfigurata di monitoraggio remoto IoT Suite

Vedere anche le [domande frequenti](iot-suite-faq.md) generali.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto costa effettuare il provisioning della nuova soluzione di monitoraggio?

La nuova soluzione preconfigurata offre due opzioni di distribuzione:

* Un'opzione *di base* progettata per sviluppatori che necessitano di costi di distribuzione inferiori o clienti che vogliono compilare una demo o un modello di verifica.
* Un'opzione *standard* progettata per le organizzazioni che vogliono distribuire un'infrastruttura pronta per l'ambiente di produzione.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Come si mantengono ridotti i costi durante lo sviluppo della soluzione?

Oltre a offrire due distribuzioni diverse, la nuova soluzione di monitoraggio remoto include un'impostazione per abilitare o disabilitare on demand tutti i dispositivi simulati. La disabilitazione dei dispositivi di simulazione riduce i dati inseriti nella soluzione e, di conseguenza, il costo complessivo.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual è la differenza tra le opzioni di distribuzione di base e standard? Come si sceglie quella più appropriata?

Ogni opzione di distribuzione soddisfa esigenze diverse. La distribuzione di base è progettata per avviare e sviluppare modelli di verifica e piccoli progetti pilota. Offre un'architettura semplificata con le risorse minime necessarie e costi inferiori. La distribuzione standard è progettata per compilare e personalizzare una soluzione pronta per l'ambiente di produzione e offre una distribuzione con gli elementi necessari per realizzare tale soluzione. Per scopi di affidabilità e scalabilità, i microservizi dell'applicazione vengono creati come contenitori Docker e distribuiti usando un agente di orchestrazione (per impostazione predefinita, Kubernetes). L'agente di orchestrazione è responsabile della distribuzione, del ridimensionamento e della gestione dell'applicazione. È consigliabile scegliere un'opzione in base alle attuali esigenze. È possibile usarne una, l'altra o una combinazione di entrambe, a seconda della fase del progetto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Come si configura una mappa del dashboard?

Per altre informazioni, vedere [Upgrade map key to see devices on a dynamic map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Aggiornare una chiave mappa per visualizzare i dispositivi in una mappa dinamica).

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione per il monitoraggio remoto viene effettuato con il piano Transazioni sito Web interno - Livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Esplorare le funzionalità della soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-explore.md)
* [Panoramica della soluzione preconfigurata di manutenzione predittiva](iot-suite-predictive-overview.md)
* [Panoramica della soluzione preconfigurata di connected factory](iot-suite-connected-factory-overview.md)
* [Sicurezza IoT sin dall'inizio](securing-iot-ground-up.md)