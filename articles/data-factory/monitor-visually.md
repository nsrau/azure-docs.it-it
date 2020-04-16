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
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419443"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorare visivamente Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dopo aver creato e pubblicato una pipeline in Azure Data Factory, è possibile associarla a un trigger o avviare manualmente un'esecuzione ad hoc. È possibile monitorare tutte le esecuzioni della pipeline in modo nativo nell'esperienza utente di Azure Data Factory.You can monitor all of your pipeline runs natively in the Azure Data Factory user experience. Per aprire l'esperienza di monitoraggio, selezionare il riquadro **Monitor& Gestisci** nel pannello della data factory del portale di [Azure.](https://portal.azure.com/) Se sei già nell'esperienza utente ADF, fai clic sull'icona **Monitor** nella barra laterale sinistra.

Tutte le esecuzioni della data factory vengono visualizzate nel fuso orario locale del browser. Se si modifica il fuso orario, tutti i campi data/ora si agganciano a quello selezionato.

## <a name="monitor-pipeline-runs"></a>Monitorare le esecuzioni di pipeline

La visualizzazione di monitoraggio predefinita è l'elenco delle esecuzioni della pipeline nel periodo di tempo selezionato. Vengono visualizzate le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline |
| Azioni | Icone che consentono di visualizzare i dettagli dell'attività, annullare o rieseguire la pipeline |
| Inizio esecuzione | Data e ora di inizio per l'esecuzione della pipeline (MM/GG/AAAA, HH:MM:SS AM/PM) |
| Duration | Durata dell'esecuzione (HH:MM:SS) |
| Attivato da | Nome del trigger che ha avviato la pipeline |
| Stato | **Non riuscito**, **Operazione riuscita**, **In corso**, **Annullato**o **In coda** |
| annotazioni | Tag filtrabili associati a una pipelineFilterable tags associated with a pipeline  |
| Parametri | Parametri per l'esecuzione della pipeline (coppie nome/valore) |
| Errore | Se la pipeline non è riuscita, l'errore di esecuzione |
| ID esecuzione | ID dell'esecuzione pipeline |

![Visualizzazione elenco per il monitoraggio delle esecuzioni della pipelineList view for monitoring pipeline runs](media/monitor-visually/pipeline-runs.png)

È necessario selezionare manualmente il pulsante **Aggiorna** per aggiornare l'elenco delle esecuzioni della pipeline e dell'attività. L'aggiornamento automatico non è attualmente supportato.

![Pulsante Aggiorna](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorare le esecuzioni delle attività

Per visualizzare le esecuzioni di attività per ogni esecuzione della pipeline, selezionare l'icona **Visualizza esecuzioni attività** nella colonna **Azioni.To** view activity runs for each pipeline run, select the View activity runs icon under the Actions column. La visualizzazione elenco mostra le esecuzioni di attività che corrispondono a ogni esecuzione della pipeline.

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline |
| Tipo di attività | Tipo dell'attività, ad esempio **Copy**, **ExecuteDataFlow**o **AzureMLExecutePipeline** |
| Azioni | Icone che consentono di visualizzare informazioni di input JSON, informazioni di output JSON o esperienze di monitoraggio dettagliate specifiche dell'attività | 
| Inizio esecuzione | Data e ora di inizio dell'esecuzione dell'attività (MM/GG/AAAA, HH:MM:SS AM/PM) |
| Duration | Durata dell'esecuzione (HH:MM:SS) |
| Stato | **Non riuscito**, **Operazione non riuscita**, In **corso**o **Annullato** |
| Runtime di integrazione | Runtime di integrazione su cui è stata eseguita l'attività |
| Proprietà utente | Proprietà definite dall'utente dell'attività |
| Errore | Se l'attività non è riuscita, l'errore di esecuzione |
| ID esecuzione | ID dell'esecuzione attività |

![Visualizzazione elenco per il monitoraggio delle esecuzioni delle attività](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Alzare di livello le proprietà utente per il monitoraggio

Promuovere qualsiasi proprietà dell'attività della pipeline come proprietà utente in modo che diventi un'entità monitorata. Ad esempio, è possibile alzare di livello le proprietà **Origine** e **Destinazione** dell'attività di copia nella pipeline come proprietà utente. Selezionare **Genera automatica** per generare le proprietà utente **Origine** e **Destinazione** per un'attività di copia.

![Creare proprietà utente](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> È possibile promuovere fino a cinque proprietà dell'attività della pipeline solo come proprietà utente.

Dopo aver creato le proprietà utente, è possibile monitorarle nelle visualizzazioni elenco di monitoraggio. Se l'origine dell'attività di copia è un nome di tabella, è possibile monitorare il nome della tabella di origine come colonna nella visualizzazione elenco per le esecuzioni di attività.

![Elenco esecuzioni attività senza proprietà utente](media/monitor-visually/monitor-user-properties-image2.png)

![Aggiungere colonne per le proprietà utente all'elenco delle esecuzioni attività](media/monitor-visually/monitor-user-properties-image3.png)

![Elenco esecuzioni attività con colonne per le proprietà utente](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurare la visualizzazione elenco

### <a name="order-and-filter"></a>Ordine e filtro

Consente di specificare se le esecuzioni della pipeline saranno defininte o ascendenti in base all'ora di inizio esecuzione. La pipeline di filtro viene eseguita utilizzando le colonne seguenti:Filter pipeline runs by using the following columns:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Filtrare in base al nome della pipeline. |
| Inizio esecuzione |  Determinare l'intervallo di tempo delle esecuzioni della pipeline visualizzate. Le opzioni includono filtri rapidi per **Ultime 24 ore**, **Ultima settimana**e **Ultimi 30 giorni** oppure per selezionare una data e un'ora personalizzate. |
| Stato dell'esecuzione | Il filtro viene eseguito in base allo stato: **Riuscito**, **Non riuscito**, **In coda**, **Annullato**o **In corso**. |
| annotazioni | Filtra per tag applicati a ogni pipeline |
| Esecuzioni | Filtrare se si desidera visualizzare le pipeline rieseFilter whether you want to see reran pipelines |

![Opzioni per il filtraggio](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Aggiungere o rimuovere le colonne
Fare clic con il pulsante destro del mouse sull'intestazione della visualizzazione elenco e scegliere le colonne che si desidera visualizzare nella visualizzazione elenco.

![Opzioni per le colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Regolare la larghezza delle colonne
Aumentare e ridurre la larghezza delle colonne nella visualizzazione elenco passando il mouse sull'intestazione della colonna.

## <a name="rerun-activities-inside-a-pipeline"></a>Eseguire nuovamente attività all'interno di una pipeline

È possibile rieseguire le attività all'interno di una pipeline. Selezionare **Visualizza esecuzioni attività**e quindi selezionare l'attività nella pipeline da cui si desidera rieseguire la pipeline.

![Visualizzare le esecuzioni di attività](media/monitor-visually/rerun-activities-image1.png)

![Selezionare un'esecuzione di attività](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Riesecuzione da attività non riuscitaRerun from failed activity

Se un'attività non riesce, scade o viene annullata, è possibile eseguire nuovamente la pipeline da tale attività non riuscita selezionando **Riesegui dall'attività non riuscita**.

![Riesecuzione dell'attività non riuscita](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Visualizzare la cronologia di riesecuzione

È possibile visualizzare la cronologia di riesecuzione per tutte le esecuzioni di pipeline nella visualizzazione elenco.

![Visualizzare la cronologia](media/monitor-visually/rerun-history-image1.png)

È anche possibile visualizzare la cronologia di riesecuzione per una specifica esecuzione della pipeline.

![Visualizzare la cronologia di un'esecuzione della pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Visualizzazioni Gantt

Utilizzare le visualizzazioni Gantt per visualizzare rapidamente le pipeline e le esecuzioni di attività.

![Esempio di diagramma di Gantt](media/monitor-visually/gantt1.png)

È possibile esaminare la visualizzazione Gantt per pipeline o gruppo in base alle annotazioni/tag creati nelle pipeline.

![Annotazioni del diagramma di Gantt](media/monitor-visually/gantt2.png)

La lunghezza della barra informa la durata della pipeline. È inoltre possibile selezionare la barra per visualizzare ulteriori dettagli.

![Durata diagramma di Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Presentazioni guidate
Selezionare l'icona **Informazioni** in basso a sinistra. Selezionare **quindi Visite guidate** per ottenere istruzioni dettagliate su come monitorare le esecuzioni della pipeline e dell'attività.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Avvisi

È possibile generare avvisi sulle metriche supportate in Data Factory. Selezionare **Monitora** > **avvisi & metriche** nella pagina Monitoraggio di Data Factory per iniziare.

![Pagina Monitoraggio factory dati](media/monitor-visually/alerts01.png)

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Creare avvisi

1.  Selezionare **Nuova regola di avviso** per creare un nuovo avviso.

    ![Pulsante Nuova regola di avviso](media/monitor-visually/alerts02.png)

1.  Specificare il nome della regola e selezionare la gravità dell'avviso.

    ![Caselle per il nome e la gravità della regola](media/monitor-visually/alerts03.png)

1.  Selezionare i criteri di avviso.

    ![Scatola per i criteri di destinazione](media/monitor-visually/alerts04.png)

    ![Elenco dei criteri](media/monitor-visually/alerts05.png)

1.  Configurare la logica di avviso. È possibile creare un avviso per la metrica selezionata per tutte le pipeline e attività corrispondenti. È inoltre possibile selezionare un particolare tipo di attività, nome attività, nome della pipeline o tipo di errore.

    ![Opzioni per la configurazione della logica di avviso](media/monitor-visually/alerts06.png)

1.  Configurare le notifiche e-mail, SMS, push e vocali per l'avviso. Creare un gruppo di azioni o sceglierne uno esistente per le notifiche di avviso.

    ![Opzioni per la configurazione delle notifiche](media/monitor-visually/alerts07.png)

    ![Opzioni per l'aggiunta di una notifica](media/monitor-visually/alerts08.png)

1.  Creare la regola di avviso.

    ![Opzioni per la creazione di una regola di avviso](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e la gestione delle pipeline, vedere l'articolo [Monitorare e gestire le pipeline](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) a livello di codice.
