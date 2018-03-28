---
title: Previsione dell'integrità dei veicoli e abitudini di guida - Azure | Documentazione Microsoft
description: Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 02b3e0e0808cb9a1a8a2186b1abe6da7dd13e56e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Studio della soluzione di analisi dei dati di telemetria del veicolo
Questo menu contiene i collegamenti alle sezioni dello studio: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Panoramica
I supercomputer non sono più usati solo nei laboratori, ma sono nei nostri garage. Ora si trovano nelle automobili all'avanguardia che contengono una miriade di sensori. Questi sensori consentono di tenere traccia e di monitorare milioni di eventi al secondo. Entro il 2020, la maggior parte di questi veicoli sarà connessa a Internet. Si attingerà così a questa grande quantità di dati per offrire maggiore sicurezza, affidabilità e quindi una migliore esperienza di guida. Grazie a Cortana Intelligence, Microsoft trasforma questo sogno in realtà.

Cortana Intelligence è una famiglia di prodotti di analisi avanzata e Big Data completamente gestita che consente di trasformare i dati in azioni intelligenti. Il modello di soluzione per l'analisi dei dati di telemetria del veicolo di Cortana Intelligence mostra come le concessionarie, i produttori di automobili e le compagnie assicurative possono ottenere informazioni predittive in tempo reale sullo stato di integrità dei veicoli e sulle abitudini di guida.

La soluzione viene implementata come un [modello di architettura lambda](https://en.wikipedia.org/wiki/Lambda_architecture), che mostra tutto il potenziale della piattaforma Cortana Intelligence per l'elaborazione batch e in tempo reale.

## <a name="architecture"></a>Architecture
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

