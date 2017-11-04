---
title: Monitorare visivamente le data factory di Azure | Microsoft Docs
description: Informazioni su come monitorare visivamente le data factory di Azure
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: shlo
ms.openlocfilehash: f30462c62c0aad73e7a1797a8a6dcd0a2f705666
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorare visivamente le data factory di Azure
Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI).
In questa Guida introduttiva si apprenderà come monitorare visivamente le pipeline di data factory versione 2 senza scrivere alcuna riga di codice.
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="monitor-data-factory-v2-pipelines"></a>Monitorare le pipeline di data factory versione 2

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pannello della data factory creata nel portale di Azure e fare clic sul riquadro 'Monitoraggio e gestione'. Verrà avviata l'esperienza di monitoraggio visivo dei file di definizione dell'applicazione (ADF) versione 2.

## <a name="list-view-monitoring"></a>Monitoraggio vista elenco

È possibile monitorare le esecuzioni di pipeline e attività con una semplice interfaccia vista elenco. Tutte le esecuzioni vengono visualizzate nel fuso orario del browser locale. È possibile modificare il fuso orario. In questo caso, tutti i campi data e ora passeranno al fuso orario selezionato.  

#### <a name="monitoring-pipeline-runs"></a>Monitoraggio delle esecuzioni di pipeline
Vista elenco che illustra ogni esecuzione di pipeline per la data factory versione 2. Colonne incluse:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline. |
| Azioni | Singola azione disponibile per la visualizzazione delle esecuzioni di attività. |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline (GG/MM/AAAA, HH:MM:SS) |
| Duration | Durata dell'esecuzione (HH:MM:SS) |
| Attivato da | Trigger manuale o pianificato |
| Stato | Non riuscito, riuscito, in corso |
| Parametri | Parametri di esecuzione della pipeline (nome, coppie di valori) |
| Errore | Eventuale errore di esecuzione della pipeline |
| ID esecuzione | ID dell'esecuzione della pipeline |

![Monitorare le esecuzioni di pipeline](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Monitoraggio delle esecuzioni di attività
Visualizzazione elenco che illustra le esecuzioni di attività corrispondenti a ogni esecuzione di pipeline. Fare clic sull'icona **'Esecuzioni attività'** nella colonna **'Azioni'** per visualizzare le esecuzioni di attività per ogni esecuzione di pipeline. Colonne incluse:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline. |
| Tipo di attività | Tipo di attività, ad esempio Copy, HDInsightSpark, HDInsightHive e così via |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione dell'attività (GG/MM/AAAA, HH:MM:SS) |
| Duration | Durata dell'esecuzione (HH:MM:SS) |
| Stato | Non riuscito, riuscito, in corso |
| Input | Matrice JSON con la descrizione degli input dell'attività |
| Output | Matrice JSON con la descrizione degli output dell'attività |
| Errore | Eventuale errore di esecuzione dell'attività |

![Monitorare le esecuzioni delle attività](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Per aggiornare l'elenco delle esecuzioni di pipeline e attività, è necessario fare clic sull'icona **'Aggiorna'** nella parte superiore. L'aggiornamento automatico non è attualmente supportato.
>

![Aggiorna](media/monitor-visually/refresh.png)

## <a name="features"></a>Funzionalità

#### <a name="rich-ordering-and-filtering"></a>Ordinamento e filtro avanzati

È possibile ordinare le esecuzioni di pipeline in ordine crescente e decrescente in base all'inizio dell'esecuzione e filtrarle in base alle colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline. Le opzioni includono filtri rapidi basati su 'Ultime 24 ore', 'Ultima settimana', 'Ultimi 30 giorni'. In alternativa è possibile selezionare una data e un'ora personalizzate. |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline |
| Stato dell'esecuzione | Esecuzioni di filtri in base allo stato, ad esempio riuscito, non riuscito, in corso |

![Filtro](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Aggiungere o rimuovere colonne dalla vista elenco
Fare clic con il pulsante destro del mouse sull'intestazione della vista elenco e scegliere le colonne da visualizzare all'interno di questa

![Colonne](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Modificare la larghezza delle colonne nella vista elenco
Per aumentare e ridurre la larghezza delle colonne nella vista elenco, è sufficiente semplicemente passare il mouse sull'intestazione delle colonne

#### <a name="select-data-factory"></a>Selezionare la data factory
Passare il mouse sull'icona 'Data factory' nella parte superiore sinistra. Fare clic sull'icona 'Freccia' per visualizzare un elenco di sottoscrizioni e data factory di Azure che è possibile monitorare.

![Selezionare la data factory](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Presentazioni guidate
Fare clic sull'icona 'Informazioni' nella parte inferiore sinistra e quindi su 'Presentazione guidata' per ottenere istruzioni dettagliate su come monitorare le esecuzioni di pipeline e attività.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Commenti e suggerimenti
Fare clic sull'icona 'Commenti e suggerimenti' per offrire un parere sulle diverse funzionalità o su eventuali problemi riscontrati.

![Commenti e suggerimenti](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo [Monitorare e gestire pipeline a livello di codice](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) per informazioni sul monitoraggio e sulla gestione delle pipeline.
