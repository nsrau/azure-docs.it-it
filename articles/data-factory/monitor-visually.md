---
title: Monitorare visivamente le data factory di Azure
description: Informazioni su come monitorare visivamente le data factory di Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 7b79fd9c87e97e624cce567b57c1c65fefcc151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684634"
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorare visivamente le data factory di Azure
Azure Data Factory è un servizio di integrazione di dati basato sul cloud. È possibile usarlo per creare flussi di lavoro basati sui dati nel cloud per l'orchestrazione e l'automazione dello spostamento e della trasformazione dei dati. Con Azure Data Factory è possibile:

- Creare e pianificare flussi di lavoro basati sui dati (denominati pipeline) che possono inserire dati da archivi dati diversi.
- Elaborare/trasformare i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning.
- Pubblicare i dati di output in archivi dati, ad esempio Azure SQL Data Warehouse, per consentire alle applicazioni di business intelligence (BI) di utilizzarli.

In questo argomento di avvio rapido si apprenderà come monitorare visivamente le pipeline di Data Factory senza scrivere alcuna riga di codice.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="monitor-data-factory-pipelines"></a>Monitorare le pipeline di Data Factory

Monitorare le esecuzioni di pipeline e attività con una semplice interfaccia di visualizzazione elenco. Tutte le esecuzioni vengono visualizzate nel fuso orario locale del browser. Se si modifica il fuso orario, tutti i campi di data/ora vengono bloccati su quello selezionato.  

1. Avviare Microsoft Edge o Google Chrome. Attualmente, l'interfaccia utente di Data Factory è supportata solo in questi due Web browser.
2. Accedere al [portale di Azure](https://portal.azure.com/).
3. Passare al pannello per il data factory creato nella portale di Azure. Selezionare il riquadro **monitoraggio & Gestisci** per avviare l'esperienza di monitoraggio Data Factory visuale.

## <a name="monitor-pipeline-runs"></a>Monitorare le esecuzioni di pipeline
La visualizzazione elenco Mostra ogni esecuzione di pipeline per le pipeline Data Factory. Sono incluse le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline |
| Azioni | Singola azione disponibile per la visualizzazione delle esecuzioni di attività |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline (MM/gg/aaaa, HH: MM: SS AM/PM) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Attivato da | Trigger manuale o trigger pianificato |
| Stato | **Operazione non riuscita**, **riuscita**o **in corso** |
| Parametri | Parametri per l'esecuzione della pipeline (coppie nome/valore) |
| Tipi di errore | Eventuale errore di esecuzione della pipeline |
| ID esecuzione | ID dell'esecuzione pipeline |

![Visualizzazione elenco per il monitoraggio delle esecuzioni di pipeline](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorare le esecuzioni delle attività
La visualizzazione elenco Mostra le esecuzioni di attività corrispondenti a ogni esecuzione della pipeline. Per visualizzare le esecuzioni di attività per ogni esecuzione della pipeline, selezionare l'icona **esecuzioni attività** nella colonna **azioni** . La visualizzazione elenco include le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline |
| Tipo di attività | Tipo di attività, ad esempio **Copy**, **HDInsightSpark**o **HDInsightHive** |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione dell'attività (MM/gg/aaaa, HH: MM: SS AM/PM) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Stato | **Operazione non riuscita**, **riuscita**o **in corso** |
| Input | Matrice JSON che descrive gli input dell'attività |
| Output | Matrice JSON che descrive gli output delle attività |
| Tipi di errore | Errore di esecuzione dell'attività (se presente) |

![Visualizzazione elenco per le esecuzioni delle attività di monitoraggio](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Per aggiornare l'elenco delle esecuzioni di pipeline e attività, è necessario selezionare il pulsante **Aggiorna** nella parte superiore. L'aggiornamento automatico non è attualmente supportato.

![Pulsante Aggiorna](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Selezionare una data factory per il monitoraggio
Passare il puntatore del mouse sull'icona **Data Factory** in alto a sinistra. Selezionare l'icona della freccia per visualizzare un elenco di sottoscrizioni e data factory di Azure che è possibile monitorare.

![Selezionare il data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurare la visualizzazione elenco

### <a name="apply-rich-ordering-and-filtering"></a>Applicare ordinamento e filtro avanzati

La pipeline degli ordini viene eseguita in DESC/ASC in base all'ora di inizio dell'esecuzione. Filtrare le esecuzioni delle pipeline utilizzando le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline. Le opzioni includono filtri rapidi per le **ultime 24 ore**, **settimana scorsa**e **ultimi 30 giorni**. Oppure selezionare una data e un'ora personalizzate. |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline. |
| Stato dell'esecuzione | Filtro eseguito in base allo stato: **riuscito**, **non riuscito**o **in corso**. |

![Opzioni per il filtro](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Aggiungere o rimuovere le colonne
Fare clic con il pulsante destro del mouse sull'intestazione della visualizzazione elenco e scegliere le colonne che si desidera visualizzare nella visualizzazione elenco.

![Opzioni per le colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Regolare la larghezza delle colonne
Aumentare e diminuire la larghezza delle colonne nella visualizzazione elenco passando il puntatore del mouse sull'intestazione di colonna.

## <a name="promote-user-properties-to-monitor"></a>Alzare di livello le proprietà utente per il monitoraggio

È possibile alzare di livello delle proprietà dell'attività di pipeline come una proprietà utente in modo che diventi un'entità che è possibile monitorare. Ad esempio, è possibile alzare di livello le proprietà di **origine** e **destinazione** dell'attività di copia nella pipeline come proprietà utente. È anche possibile selezionare **genera automaticamente** per generare le proprietà utente di **origine** e di **destinazione** per un'attività di copia.

![Creare proprietà utente](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> È possibile alzare di livello fino a cinque proprietà dell'attività pipeline come proprietà utente.

Dopo aver creato le proprietà utente, è possibile monitorarle nelle visualizzazioni elenco di monitoraggio. Se l'origine dell'attività di copia è un nome di tabella, è possibile monitorare il nome della tabella di origine come colonna nella visualizzazione elenco per le esecuzioni attività.

![Elenco esecuzioni attività senza proprietà utente](media/monitor-visually/monitor-user-properties-image2.png)

![Aggiungere colonne per le proprietà utente all'elenco esecuzioni attività](media/monitor-visually/monitor-user-properties-image3.png)

![Elenco esecuzioni attività con colonne per le proprietà utente](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Eseguire nuovamente attività all'interno di una pipeline

È ora possibile eseguire nuovamente attività all'interno di una pipeline. Selezionare **Visualizza esecuzioni attività**, quindi selezionare l'attività nella pipeline da cui si desidera eseguire di nuovo la pipeline.

![Visualizzare le esecuzioni di attività](media/monitor-visually/rerun-activities-image1.png)

![Selezionare un'esecuzione di attività](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visualizzare la cronologia di riesecuzione

È possibile visualizzare la cronologia di riesecuzione per tutte le esecuzioni di pipeline nella visualizzazione elenco.

![Visualizzare la cronologia](media/monitor-visually/rerun-history-image1.png)

È anche possibile visualizzare la cronologia di riesecuzione per una specifica esecuzione della pipeline.

![Visualizzare la cronologia di un'esecuzione della pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Visualizzazioni Gantt

Usare le visualizzazioni Gantt per visualizzare rapidamente le pipeline e le esecuzioni delle attività. È possibile esaminare la visualizzazione Gantt per pipeline o raggruppare le annotazioni/tag creati nelle pipeline.

![Esempio di diagramma di Gantt](media/monitor-visually/gantt1.png)

![Annotazioni diagramma di Gantt](media/monitor-visually/gantt2.png)

La lunghezza della barra informa la durata della pipeline. È anche possibile selezionare la barra per visualizzare altri dettagli.

![Durata diagramma di Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Presentazioni guidate
Selezionare l'icona **informazioni** in basso a sinistra. Selezionare quindi **Tours guidati** per ottenere istruzioni dettagliate su come monitorare le esecuzioni di pipeline e attività.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Commenti e suggerimenti
Selezionare l'icona **commenti e suggerimenti** per inviare commenti e suggerimenti su diverse funzionalità o su eventuali problemi che potrebbero essere stati rilevati.

![Commenti e suggerimenti](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Avvisi

È possibile generare avvisi sulle metriche supportate in Data Factory. Per iniziare, selezionare **monitoraggio** > **avvisi & metriche** nella pagina Monitoraggio data factory.

![Pagina Monitoraggio di data factory](media/monitor-visually/alerts01.png)

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Creare avvisi

1.  Selezionare **nuova regola di avviso** per creare un nuovo avviso.

    ![Pulsante nuova regola di avviso](media/monitor-visually/alerts02.png)

1.  Specificare il nome della regola e selezionare la gravità dell'avviso.

    ![Caselle per il nome e la gravità della regola](media/monitor-visually/alerts03.png)

1.  Selezionare i criteri di avviso.

    ![Casella per i criteri di destinazione](media/monitor-visually/alerts04.png)

    ![Elenco di criteri](media/monitor-visually/alerts05.png)

1.  Configurare la logica di avviso. È possibile creare un avviso per la metrica selezionata per tutte le pipeline e attività corrispondenti. È inoltre possibile selezionare un tipo di attività specifico, un nome di attività, un nome di pipeline o un tipo di errore.

    ![Opzioni per la configurazione della logica di avviso](media/monitor-visually/alerts06.png)

1.  Configurare notifiche di posta elettronica, SMS, push e vocali per l'avviso. Creare un gruppo di azione oppure sceglierne uno esistente per le notifiche di avviso.

    ![Opzioni per la configurazione delle notifiche](media/monitor-visually/alerts07.png)

    ![Opzioni per l'aggiunta di una notifica](media/monitor-visually/alerts08.png)

1.  Creare la regola di avviso.

    ![Opzioni per la creazione di una regola di avviso](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e sulla gestione delle pipeline, vedere l'articolo [monitorare e gestire pipeline a livello di codice](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
