---
title: Previsione dell'integrità dei veicoli e abitudini di guida - Azure | Documentazione Microsoft
description: Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida.
services: machine-learning
author: marktab
ms.author: tdsp
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 688a821667b38423b43af87b7a26525e52d9a7aa
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444160"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Studio della soluzione di analisi dei dati di telemetria del veicolo

I supercomputer non sono più usati solo nei laboratori, ma sono nei nostri garage. Ora si trovano nelle automobili all'avanguardia che contengono una miriade di sensori. Questi sensori consentono di tenere traccia e di monitorare milioni di eventi al secondo. Entro il 2020, la maggior parte di questi veicoli sarà connessa a Internet. Si attingerà così a questa grande quantità di dati per offrire maggiore sicurezza, affidabilità e quindi una migliore esperienza di guida. Grazie a Cortana Intelligence, Microsoft trasforma questo sogno in realtà.

Cortana Intelligence è una famiglia di prodotti di analisi avanzata e Big Data completamente gestita che consente di trasformare i dati in azioni intelligenti. Il modello di soluzione per l'analisi dei dati di telemetria del veicolo di Cortana Intelligence mostra come le concessionarie, i produttori di automobili e le compagnie assicurative possono ottenere informazioni predittive in tempo reale sullo stato di integrità dei veicoli e sulle abitudini di guida.

La soluzione viene implementata come un [modello di architettura lambda](https://en.wikipedia.org/wiki/Lambda_architecture), che mostra tutto il potenziale della piattaforma Cortana Intelligence per l'elaborazione batch e in tempo reale.

## <a name="architecture"></a>Architettura
L'architettura della soluzione di analisi dei dati di telemetria del veicolo è illustrata nel diagramma seguente:

![Diagramma dell'architettura della soluzione](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Questa soluzione include i componenti di Cortana Intelligence seguenti e ne illustra l'integrazione:

* **Hub eventi di Azure** inserisce milioni di eventi di telemetria del veicolo in Azure.
* **Analisi di flusso di Azure** fornisce informazioni dettagliate in tempo reale sull'integrità del veicolo e salva i dati in modo permanente in un archivio a lungo termine per un'analisi batch avanzata.
* **Azure Machine Learning** rileva le anomalie in tempo reale e usa l'elaborazione batch per offrire informazioni dettagliate predittive.
* **Azure HDInsight** trasforma i dati su larga scala.
* **Azure Data Factory** gestisce l'orchestrazione, la pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch.
* **Power BI** offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittiva.

La soluzione accede a due origini dati diverse: 

* **Segnali e diagnostica simulati del veicolo**: un simulatore di dati telematici del veicolo genera informazioni di diagnostica e segnali che corrispondono allo stato del veicolo e al modello di guida in un determinato momento. 
* **Catalogo dei veicoli**: questo set di dati di riferimento esegue il mapping dei numeri di telaio (VIN, Vehicle Identification Number) ai modelli.

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente questa soluzione, vedere [Playbook della soluzione di analisi dei dati di telemetria del veicolo: approfondimento della soluzione](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

Per informazioni su come configurare i report e i dashboard di Power BI per questa soluzione, vedere [Istruzioni di configurazione del dashboard di Power BI per il modello di soluzione per l'analisi dei dati di telemetria del veicolo](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
