---
title: Monitorare visivamente le data factory di Azure | Microsoft Docs
description: Informazioni su come monitorare visivamente le data factory di Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54409963"
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorare visivamente le data factory di Azure
Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI).

In questo argomento di avvio rapido si apprenderà come monitorare visivamente le pipeline di Data Factory senza scrivere alcuna riga di codice.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="monitor-data-factory-pipelines"></a>Monitorare le pipeline di Data Factory

È possibile monitorare le esecuzioni di pipeline e attività con una semplice interfaccia vista elenco. Tutte le esecuzioni vengono visualizzate nel fuso orario del browser locale. È possibile modificare il fuso orario. In questo caso, tutti i campi data e ora passano al fuso orario selezionato.  

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Accedere al [portale di Azure](https://portal.azure.com/).
3. Passare al pannello della data factory creata nel portale di Azure e fare clic sul riquadro 'Monitoraggio e gestione' per avviare l'esperienza di monitoraggio visivo di Data Factory.

## <a name="monitor-pipeline-runs"></a>Monitorare le esecuzioni di pipeline
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
| Tipi di errore | Eventuale errore di esecuzione della pipeline |
| ID esecuzione | ID dell'esecuzione pipeline |

![Monitorare le esecuzioni di pipeline](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorare le esecuzioni delle attività
Visualizzazione elenco che illustra le esecuzioni di attività corrispondenti a ogni esecuzione di pipeline. Fare clic sull'icona **'Esecuzioni attività'** nella colonna **'Azioni'** per visualizzare le esecuzioni di attività per ogni esecuzione di pipeline. Colonne incluse:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline. |
| Tipo di attività | Tipo di attività, ad esempio Copy, HDInsightSpark, HDInsightHive e così via. |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione dell'attività (GG/MM/AAAA, HH:MM:SS) |
| Duration | Durata dell'esecuzione (HH:MM:SS) |
| Stato | Non riuscito, riuscito, in corso |
| Input | Matrice JSON con la descrizione degli input dell'attività |
| Output | Matrice JSON con la descrizione degli output dell'attività |
| Tipi di errore | Eventuale errore di esecuzione dell'attività |

![Monitorare le esecuzioni delle attività](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Per aggiornare l'elenco delle esecuzioni di pipeline e attività, è necessario fare clic sull'icona **'Aggiorna'** nella parte superiore. L'aggiornamento automatico non è attualmente supportato.

![Aggiorna](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Selezionare una data factory per il monitoraggio
Passare il mouse sull'icona **Data factory** nella parte superiore sinistra. Fare clic sull'icona 'Freccia' per visualizzare un elenco di sottoscrizioni e data factory di Azure che è possibile monitorare.

![Selezionare la data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurare la visualizzazione elenco

### <a name="apply-rich-ordering-and-filtering"></a>Applicare ordinamento e filtro avanzati

È possibile ordinare le esecuzioni di pipeline in ordine crescente e decrescente in base all'inizio dell'esecuzione e filtrarle in base alle colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline. Le opzioni includono filtri rapidi basati su 'Ultime 24 ore', 'Ultima settimana', 'Ultimi 30 giorni'. In alternativa è possibile selezionare una data e un'ora personalizzate. |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline |
| Stato dell'esecuzione | Esecuzioni di filtri in base allo stato, ad esempio riuscito, non riuscito, in corso |

![Filtro](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Aggiungere o rimuovere le colonne
Fare clic con il pulsante destro del mouse sull'intestazione della vista elenco e scegliere le colonne da visualizzare all'interno di questa

![Colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Regolare la larghezza delle colonne
Per aumentare e ridurre la larghezza delle colonne nella vista elenco, è sufficiente passare il mouse sull'intestazione delle colonne

## <a name="promote-user-properties-to-monitor"></a>Alzare di livello le proprietà utente per il monitoraggio

È possibile alzare di livello delle proprietà dell'attività di pipeline come una proprietà utente in modo che diventi un'entità che è possibile monitorare. Ad esempio, è possibile alzare di livello delle proprietà **Origine** e **Destinazione** dell'attività di copia nella pipeline come proprietà dell'utente. È anche possibile selezionare **genera automaticamente** per generare le proprietà utente **Origine** e **Destinazione** per un'attività di copia.

![Creare proprietà utente](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> È possibile alzare di livello fino a 5 proprietà di attività di pipeline come proprietà utente.

Dopo aver creato le proprietà utente, è possibile monitorarle successivamente nelle viste elenco di monitoraggio. Se l'origine per l'attività di copia è un nome tabella, è possibile monitorare il nome tabella di origine come tabella nella vista elenco esecuzioni attività.

![Elenco esecuzioni attività senza proprietà utente](media/monitor-visually/monitor-user-properties-image2.png)

![Aggiungere colonne per le proprietà utente all'elenco di esecuzioni attività](media/monitor-visually/monitor-user-properties-image3.png)

![Elenco esecuzioni attività con colonne per le proprietà utente](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Eseguire nuovamente attività all'interno di una pipeline

È ora possibile eseguire nuovamente attività all'interno di una pipeline. Fare clic su **View activity runs** (Visualizza le esecuzioni di attività) e selezionare l'attività nella pipeline da cui si vuole eseguire nuovamente la pipeline.

![Visualizzare le esecuzioni di attività](media/monitor-visually/rerun-activities-image1.png)

![Selezionare un'esecuzione di attività](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visualizzare la cronologia di riesecuzione

È possibile visualizzare la cronologia di riesecuzione per tutte le esecuzioni di pipeline nella visualizzazione elenco.

![Visualizzare la cronologia](media/monitor-visually/rerun-history-image1.png)

È anche possibile visualizzare la cronologia di riesecuzione per una specifica esecuzione della pipeline.

![Visualizzare la cronologia di un'esecuzione della pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Presentazioni guidate
Fare clic sull'icona 'Informazioni' nella parte inferiore sinistra e quindi su 'Presentazione guidata' per ottenere istruzioni dettagliate su come monitorare le esecuzioni di pipeline e attività.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Commenti e suggerimenti
Fare clic sull'icona 'Commenti e suggerimenti' per offrire un parere sulle diverse funzionalità o su eventuali problemi riscontrati.

![Commenti e suggerimenti](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Avvisi

È possibile generare avvisi sulle metriche supportate in Data Factory. Selezionare **Monitoraggio -> Avvisi e metriche** nella pagina Monitoraggio di Data Factory per iniziare.

![](media/monitor-visually/alerts01.png)

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Creare avvisi

1.  Fare clic su **Nuova regola di avviso**  per creare un nuovo avviso.

    ![](media/monitor-visually/alerts02.png)

1.  Specificare il nome della regola e selezionare l'avviso **Gravità**.

    ![](media/monitor-visually/alerts03.png)

1.  Selezionare i criteri di avviso.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Configurare la logica degli avvisi. È possibile creare un avviso per la metrica selezionata per tutte le pipeline e attività corrispondenti. È anche possibile selezionare un tipo di attività, un nome di attività, un nome di pipeline o un tipo di errore specifico.

    ![](media/monitor-visually/alerts06.png)

1.  Configurare notifiche tramite **posta elettronica/SMS/push/voce** per l'avviso. Creare o scegliere un **gruppo di azioni** esistente per le notifiche di avviso.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Creare la regola di avviso.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo [Monitorare e gestire pipeline a livello di codice](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) per informazioni sul monitoraggio e sulla gestione delle pipeline.
