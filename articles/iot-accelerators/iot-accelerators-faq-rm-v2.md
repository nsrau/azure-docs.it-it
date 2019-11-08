---
title: Domande frequenti sull'acceleratore di soluzioni di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo risponde alle domande frequenti relative agli acceleratori della soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826247"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Domande frequenti sull'acceleratore di soluzioni di monitoraggio remoto

Vedere anche le [domande frequenti](iot-accelerators-faq.md) generali.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto costa effettuare il provisioning della nuova soluzione di monitoraggio?

Il nuovo acceleratore di soluzioni offre due opzioni di distribuzione:

* Un'opzione *di base* progettata per sviluppatori che necessitano di costi di distribuzione inferiori o clienti che vogliono compilare una demo o un modello di verifica.
* Un'opzione *standard* progettata per le organizzazioni che vogliono distribuire un'infrastruttura pronta per l'ambiente di produzione.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Come si mantengono ridotti i costi durante lo sviluppo della soluzione?

Oltre a offrire due distribuzioni diverse, la nuova soluzione di monitoraggio remoto include un'impostazione per abilitare o disabilitare su richiesta tutti i dispositivi simulati. La disabilitazione dei dispositivi di simulazione riduce i dati inseriti nella soluzione e, di conseguenza, il costo complessivo.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual è la differenza tra le opzioni di distribuzione di base e standard? Come si sceglie quella più appropriata?

Ogni opzione di distribuzione soddisfa esigenze diverse. La distribuzione di base è progettata per avviare e sviluppare modelli di verifica e piccoli progetti pilota. Offre un'architettura semplificata con le risorse minime necessarie e costi inferiori. La distribuzione standard è progettata per compilare e personalizzare una soluzione pronta per l'ambiente di produzione e offre una distribuzione con gli elementi necessari per realizzare tale soluzione. Per scopi di affidabilità e scalabilità, i microservizi dell'applicazione vengono creati come contenitori Docker e distribuiti usando un agente di orchestrazione (per impostazione predefinita, Kubernetes). L'agente di orchestrazione è responsabile della distribuzione, del ridimensionamento e della gestione dell'applicazione. È consigliabile scegliere un'opzione in base alle attuali esigenze. È possibile usarne una, l'altra o una combinazione di entrambe, a seconda della fase del progetto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Come si configura una mappa del dashboard?

Per altre informazioni, vedere [Upgrade map key to see devices on a dynamic map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Aggiornare una chiave mappa per visualizzare i dispositivi in una mappa dinamica).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Dove è possibile reperire informazioni sulla versione precedente della soluzione di monitoraggio remoto?

La versione precedente dell'acceleratore di soluzione di monitoraggio remoto era nota come soluzione di monitoraggio remoto IoT Suite preconfigurata. È possibile trovare la documentazione archiviata all'indirizzo [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Esplorare le funzionalità dell'acceleratore di soluzioni di monitoraggio remoto](quickstart-remote-monitoring-deploy.md)
* [Panoramica dell'acceleratore di soluzioni di manutenzione predittiva](iot-accelerators-predictive-overview.md)
* [Distribuire l'acceleratore di soluzioni di connected factory](quickstart-connected-factory-deploy.md)
* [Sicurezza IoT sin dall'inizio](/azure/iot-fundamentals/iot-security-ground-up)
