---
title: Monitorare visivamente Azure Data Factory
description: Informazioni su come monitorare visivamente le data factory di Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a4258b51acfa603c156bc35cdb2cbc3b16f37ab0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278421"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorare visivamente Azure Data Factory

Una volta creata e pubblicata una pipeline in Azure Data Factory, è possibile associarla a un trigger o avviare manualmente un'esecuzione ad hoc. È possibile monitorare tutte le esecuzioni della pipeline in modo nativo nell'esperienza utente di Azure Data Factory. Per aprire l'esperienza di monitoraggio, selezionare il riquadro **monitoraggio & Gestisci** nel pannello data factory della [portale di Azure](https://portal.azure.com/). Se si è già nell'esperienza utente di Azure Data Factory, fare clic sull'icona di **monitoraggio** sulla barra laterale sinistra.

Tutte le esecuzioni di data factory vengono visualizzate nel fuso orario locale del browser. Se si modifica il fuso orario, tutti i campi di data/ora vengono bloccati su quello selezionato.

## <a name="monitor-pipeline-runs"></a>Monitorare le esecuzioni di pipeline

La visualizzazione di monitoraggio predefinita è l'elenco delle esecuzioni di pipeline nel periodo di tempo selezionato. Vengono visualizzate le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline |
| Actions | Icone che consentono di visualizzare i dettagli delle attività, annullare o eseguire di nuovo la pipeline |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline (MM/gg/aaaa, HH: MM: SS AM/PM) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Attivato da | Nome del trigger che ha avviato la pipeline |
| Stato | **Non riuscito**, **riuscito**, **in corso**, **annullato**o in **coda** |
| Annotazioni | Tag filtrabili associati a una pipeline  |
| parametri | Parametri per l'esecuzione della pipeline (coppie nome/valore) |
| Tipi di errore | Se la pipeline ha avuto esito negativo, l'errore di esecuzione |
| ID esecuzione | ID dell'esecuzione pipeline |

![Visualizzazione elenco per il monitoraggio delle esecuzioni di pipeline](media/monitor-visually/pipeline-runs.png)

È necessario selezionare manualmente il pulsante **Aggiorna** per aggiornare l'elenco delle esecuzioni di pipeline e attività. L'aggiornamento automatico non è attualmente supportato.

![Pulsante Aggiorna](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorare le esecuzioni delle attività

Per visualizzare le esecuzioni di attività per ogni esecuzione della pipeline, selezionare l'icona **Visualizza esecuzioni attività** nella colonna **azioni** . La visualizzazione elenco Mostra le esecuzioni di attività corrispondenti a ogni esecuzione della pipeline.

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline |
| Tipo di attività | Tipo di attività, ad esempio **Copy**, **ExecuteDataFlow**o **AzureMLExecutePipeline** |
| Actions | Icone che consentono di visualizzare informazioni di input JSON, informazioni di output JSON o esperienze di monitoraggio dettagliate specifiche delle attività | 
| Inizio esecuzione | Data e ora di inizio dell'esecuzione dell'attività (MM/gg/aaaa, HH: MM: SS AM/PM) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Stato | **Non riuscito**, **riuscito**, **in corso**o **annullato** |
| Integration Runtime | Integration Runtime su cui è stata eseguita l'attività |
| Proprietà utente | Proprietà definite dall'utente dell'attività |
| Tipi di errore | Se l'attività ha avuto esito negativo, l'errore di esecuzione |
| ID esecuzione | ID dell'esecuzione attività |

![Visualizzazione elenco per le esecuzioni delle attività di monitoraggio](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Alzare di livello le proprietà utente per il monitoraggio

Innalzare di livello qualsiasi proprietà dell'attività pipeline come proprietà utente in modo che diventi un'entità monitorata. Ad esempio, è possibile alzare di livello le proprietà di **origine** e **destinazione** dell'attività di copia nella pipeline come proprietà utente. Selezionare **genera automaticamente** per generare le proprietà utente di **origine** e di **destinazione** per un'attività di copia.

![Creare proprietà utente](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> È possibile alzare di livello fino a cinque proprietà dell'attività pipeline come proprietà utente.

Dopo aver creato le proprietà utente, è possibile monitorarle nelle visualizzazioni elenco di monitoraggio. Se l'origine dell'attività di copia è un nome di tabella, è possibile monitorare il nome della tabella di origine come colonna nella visualizzazione elenco per le esecuzioni attività.

![Elenco esecuzioni attività senza proprietà utente](media/monitor-visually/monitor-user-properties-image2.png)

![Aggiungere colonne per le proprietà utente all'elenco esecuzioni attività](media/monitor-visually/monitor-user-properties-image3.png)

![Elenco esecuzioni attività con colonne per le proprietà utente](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurare la visualizzazione elenco

### <a name="order-and-filter"></a>Ordine e filtro

Consente di specificare se le esecuzioni della pipeline saranno in ordine decrescente o crescente in base all'ora di inizio dell'esecuzione. Filtrare le esecuzioni delle pipeline utilizzando le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Filtrare in base al nome della pipeline. |
| Inizio esecuzione |  Determinare l'intervallo di tempo in cui vengono visualizzate le esecuzioni della pipeline. Le opzioni includono filtri rapidi per le **ultime 24 ore**, **settimana scorsa**e **ultimi 30 giorni** oppure per selezionare una data e un'ora personalizzate. |
| Stato dell'esecuzione | Filtro eseguito in base allo stato: **riuscito**, **non riuscito**, **accodato**, **annullato**o **in corso**. |
| Annotazioni | Filtrare in base ai tag applicati a ogni pipeline |
| Esecuzioni | Filtrare se si desidera visualizzare le pipeline riesegue |

![Opzioni per il filtro](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Aggiungere o rimuovere le colonne
Fare clic con il pulsante destro del mouse sull'intestazione della visualizzazione elenco e scegliere le colonne che si desidera visualizzare nella visualizzazione elenco.

![Opzioni per le colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Regolare la larghezza delle colonne
Aumentare e diminuire la larghezza delle colonne nella visualizzazione elenco passando il puntatore del mouse sull'intestazione di colonna.

## <a name="rerun-activities-inside-a-pipeline"></a>Eseguire nuovamente attività all'interno di una pipeline

È possibile rieseguire le attività all'interno di una pipeline. Selezionare **Visualizza esecuzioni attività**, quindi selezionare l'attività nella pipeline da cui si desidera eseguire di nuovo la pipeline.

![Visualizzare le esecuzioni di attività](media/monitor-visually/rerun-activities-image1.png)

![Selezionare un'esecuzione di attività](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visualizzare la cronologia di riesecuzione

È possibile visualizzare la cronologia di riesecuzione per tutte le esecuzioni di pipeline nella visualizzazione elenco.

![Visualizzare la cronologia](media/monitor-visually/rerun-history-image1.png)

È anche possibile visualizzare la cronologia di riesecuzione per una specifica esecuzione della pipeline.

![Visualizzare la cronologia di un'esecuzione della pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Visualizzazioni Gantt

Usare le visualizzazioni Gantt per visualizzare rapidamente le pipeline e le esecuzioni delle attività.

![Esempio di diagramma di Gantt](media/monitor-visually/gantt1.png)

È possibile esaminare la visualizzazione Gantt per pipeline o raggruppare le annotazioni/tag creati nelle pipeline.

![Annotazioni diagramma di Gantt](media/monitor-visually/gantt2.png)

La lunghezza della barra informa la durata della pipeline. È anche possibile selezionare la barra per visualizzare altri dettagli.

![Durata diagramma di Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Presentazioni guidate
Selezionare l'icona **informazioni** in basso a sinistra. Selezionare quindi **Tours guidati** per ottenere istruzioni dettagliate su come monitorare le esecuzioni di pipeline e attività.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alerts

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
