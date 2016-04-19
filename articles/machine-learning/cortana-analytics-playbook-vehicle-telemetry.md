<properties 
	pageTitle="Studio della soluzione di analisi dei dati di telemetria del veicolo | Microsoft Azure" 
	description="Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="bradsev" />


# Studio della soluzione di analisi dei dati di telemetria del veicolo

Questo **menu** contiene i collegamenti alle sezioni dello studio.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Panoramica
I super computer sono usciti dai laboratori e ora sono parcheggiati nel nostro garage! Questi automobili all'avanguardia contengono una miriade di sensori, fornendo agli utenti la possibilità di tenere traccia di milioni di eventi al secondo e monitorarli. Si prevede che entro il 2020 la maggior parte di queste autovetture verrà connessa a Internet. Si supponga di attingere a questa varietà di dati per fornire la sicurezza, l’affidabilità e l’esperienza di guida migliore del settore. Grazie a Cortana Intelligence, Microsoft ha reso reale questa fantasia.

Microsoft Cortana Intelligence è una famiglia di prodotti completamente gestita di Big Data e analisi avanzata che consente di trasformare i dati in azioni intelligenti. In questo articolo verrà presentato il modello di soluzione per l'analisi dei dati di telemetria del veicolo di Cortana Intelligence. Questa soluzione mostra come le concessionarie di auto, i produttori di automobili e le compagnie di assicurazione possono usare le funzionalità di Cortana Intelligence per ottenere informazioni in tempo reale e predittive sullo stato di integrità dei veicoli e sulle abitudini di guida.

La soluzione viene implementata come un [modello di architettura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) che mostra tutto il potenziale della piattaforma Cortana Intelligence per l'elaborazione in tempo reale e batch. Include un simulatore dei dati telematici del veicolo, sfrutta hub eventi per l'inserimento milioni di eventi di telemetria simulati del veicolo in Azure, quindi utilizza l'analisi di flusso per ottenere informazioni in tempo reale sull'integrità del veicolo e rende questi dati persistenti nell'archiviazione a lungo termine per un’analisi di batch più approfondita. Sfrutta il Machine Learning per il rilevamento di anomalie in tempo reale e l'elaborazione batch per ottenere informazioni predittive. HDInsight viene sfruttato per trasformare i dati nella scalabilità e Data Factory gestisce l'orchestrazione, la pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch. Infine, Power BI offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittiva.

## Architettura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Figura 1: Architettura della soluzione di analisi dei dati di telemetria del veicolo*

Questa soluzione include i seguenti **componenti di Cortana Intelligence** e illustra la loro integrazione end-to-end


- **Hub eventi** per l'inserimento di milioni di eventi di telemetria del veicolo in Azure.
- **Analisi di flusso** per ottenere informazioni dettagliate in tempo reale sul funzionamento del veicolo e salvare i dati in modo permanente nello spazio di archiviazione a lungo termine per un'analisi batch avanzata.
- **Machine Learning** per il rilevamento di anomalie in tempo reale e l'elaborazione batch per ottenere informazioni predittive.
- **HDInsight** per trasformare i dati su larga scala
- **Data Factory** gestisce l'orchestrazione, la pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch.
- **Power BI** offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittiva.

La soluzione accede a due **origini dati** diverse:

- **Segnali e diagnostica simulati del veicolo**: un simulatore di dati telematici del veicolo genera informazioni di diagnostica e segnali che corrispondono allo stato del veicolo e al modello di guida in un determinato momento. 
- **Catalogo del veicolo**: un set di dati di riferimento contenente il VIN (Vehicle Identification Number, numero di identificazione del veicolo) per il mapping del modello

<!---HONumber=AcomDC_0406_2016-->