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
ms.date: 06/30/2020
ms.openlocfilehash: fcbf48081484941fd93f209bee21eb14c2c38310
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570140"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorare visivamente Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dopo aver creato e pubblicato una pipeline in Azure Data Factory, è possibile associarla a un trigger o avviare manualmente un'esecuzione ad hoc. È possibile monitorare tutte le esecuzioni della pipeline in modo nativo nell'esperienza utente di Azure Data Factory. Per aprire l'esperienza di monitoraggio, selezionare il riquadro **Monitoraggio e gestione** nel pannello Data factory del [portale di Azure](https://portal.azure.com/). Se si è già all'interno dell'esperienza utente di Azure Data Factory, fare clic sull'icona di **monitoraggio** sulla barra laterale sinistra.

Tutte le esecuzioni della data factory vengono visualizzate nel fuso orario locale del browser. Se si modifica il fuso orario, tutti i campi di data/ora vengono impostati sul fuso selezionato.

## <a name="monitor-pipeline-runs"></a>Monitorare le esecuzioni di pipeline

La visualizzazione di monitoraggio predefinita è l'elenco delle esecuzioni di pipeline nel periodo di tempo selezionato. Vengono visualizzate le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Nome della pipeline |
| Azioni | Icone che consentono di visualizzare i dettagli delle attività, annullare o ripetere l'esecuzione della pipeline |
| Inizio esecuzione | Data e ora di inizio dell'esecuzione della pipeline (GG/MM/AAAA, HH:MM:SS) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Attivato da | Nome del trigger che ha avviato la pipeline |
| Stato | **Operazione non riuscita**, **Operazione completata**, **In corso**, **Annullata** o **In coda** |
| Annotazioni | Tag filtrabili associati a una pipeline  |
| Parametri | Parametri relativi all'esecuzione della pipeline (coppie nome-valore) |
| Errore | Errore di esecuzione se la pipeline non è riuscita |
| ID esecuzione | ID dell'esecuzione pipeline |

![Visualizzazione elenco per il monitoraggio delle esecuzioni della pipeline](media/monitor-visually/pipeline-runs.png)

Per aggiornare l'elenco delle esecuzioni di pipeline e attività, è necessario selezionare manualmente il pulsante **Aggiorna**. L'aggiornamento automatico non è attualmente supportato.

![Pulsante Aggiorna](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorare le esecuzioni delle attività

Per visualizzare le esecuzioni attività per ogni esecuzione della pipeline, selezionare l'icona **View activity runs** (Visualizza esecuzioni attività) nella colonna **Azioni**. La visualizzazione elenco illustra le esecuzioni attività corrispondenti a ogni esecuzione della pipeline.

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome attività | Nome dell'attività all'interno della pipeline |
| Tipo di attività | Tipo di attività, ad esempio **Copy**, **ExecuteDataFlow** o **AzureMLExecutePipeline** |
| Azioni | Icone che consentono di visualizzare le informazioni di input JSON, le informazioni di output JSON o esperienze di monitoraggio dettagliate specifiche delle attività | 
| Inizio esecuzione | Data e ora di inizio dell'esecuzione attività (GG/MM/AAAA, HH:MM:SS) |
| Durata | Durata dell'esecuzione (HH:MM:SS) |
| Stato | **Operazione non riuscita**, **Operazione completata**, **In corso** o **Annullata** |
| Integration Runtime | Indica l'istanza di Integration Runtime in cui è stata eseguita l'attività |
| Proprietà utente | Proprietà dell'attività definite dall'utente |
| Errore | Errore di esecuzione se l'attività non è riuscita |
| ID esecuzione | ID dell'esecuzione attività |

![Visualizzazione elenco per il monitoraggio delle esecuzioni attività](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Alzare di livello le proprietà utente per il monitoraggio

Alzare di livello qualsiasi proprietà dell'attività della pipeline e impostarla come proprietà utente in modo che diventi un'entità che è possibile monitorare. Ad esempio, è possibile alzare di livello le proprietà **Origine** e **Destinazione** dell'attività di copia nella pipeline impostandole come proprietà utente. Selezionare **Genera automaticamente** per generare le proprietà utente **Origine** e **Destinazione** per un'attività di copia.

![Creare proprietà utente](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> È possibile alzare di livello fino a cinque proprietà di attività della pipeline e impostarle come proprietà utente.

Dopo aver creato le proprietà utente, è possibile monitorarle successivamente nelle visualizzazioni elenco di monitoraggio. Se l'origine per l'attività di copia è un nome di tabella, è possibile monitorare il nome di tabella di origine come una colonna nella visualizzazione elenco delle esecuzioni attività.

![Elenco delle esecuzioni attività senza proprietà utente](media/monitor-visually/monitor-user-properties-image2.png)

![Aggiungere colonne per le proprietà utente all'elenco di esecuzioni attività](media/monitor-visually/monitor-user-properties-image3.png)

![Elenco delle esecuzioni attività con colonne per le proprietà utente](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurare la visualizzazione elenco

### <a name="order-and-filter"></a>Ordinare e filtrare

È possibile attivare/disattivare l'ordinamento crescente o decrescente delle esecuzioni della pipeline in base all'ora di inizio dell'esecuzione. Per filtrare le esecuzioni delle pipeline, usare le colonne seguenti:

| **Nome colonna** | **Descrizione** |
| --- | --- |
| Nome pipeline | Filtra in base al nome della pipeline. |
| Inizio esecuzione |  Determina l'intervallo di tempo delle esecuzioni della pipeline visualizzate. Le opzioni includono filtri rapidi basati su **Ultime 24 ore**, **Ultima settimana** e **Ultimi 30 giorni**. In alternativa è possibile selezionare una data e un'ora personalizzate. |
| Stato dell'esecuzione | Filtra le esecuzioni in base allo stato: **Operazione completata**, **Operazione non riuscita**, **Accodata**, **Annullata** o **In corso**. |
| Annotazioni | Filtra in base ai tag applicati a ogni pipeline |
| Esecuzioni | Filtra se si vogliono visualizzare le pipeline eseguite di nuovo |

![Opzioni per i filtri](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Aggiungere o rimuovere le colonne
Fare clic con il pulsante destro del mouse sull'intestazione della visualizzazione elenco e scegliere le colonne da visualizzare.

![Opzioni per le colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Regolare la larghezza delle colonne
Per aumentare e ridurre la larghezza delle colonne nella visualizzazione elenco, è sufficiente passare con il puntatore del mouse sull'intestazione di colonna.

## <a name="rerun-activities-inside-a-pipeline"></a>Eseguire nuovamente attività all'interno di una pipeline

È possibile eseguire nuovamente attività all'interno di una pipeline. Scegliere **View activity runs** (Visualizza le esecuzioni attività) e quindi selezionare l'attività nella pipeline da cui si vuole eseguire nuovamente la pipeline.

![Visualizzare le esecuzioni attività](media/monitor-visually/rerun-activities-image1.png)

![Selezionare un'esecuzione attività](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Eseguire nuovamente dall'attività non riuscita

Se un'attività non riesce, scade o viene annullata, è possibile eseguire nuovamente la pipeline da tale attività selezionando **Rerun from failed activity** (Esegui nuovamente da attività non riuscita).

![Eseguire nuovamente dall'attività non riuscita](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Visualizzare la cronologia di riesecuzione

È possibile visualizzare la cronologia di riesecuzione per tutte le esecuzioni di pipeline nella visualizzazione elenco.

![Visualizzare la cronologia](media/monitor-visually/rerun-history-image1.png)

È anche possibile visualizzare la cronologia di riesecuzione per una specifica esecuzione della pipeline.

![Visualizzare la cronologia di un'esecuzione della pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorare l'utilizzo

Per visualizzare le risorse utilizzate da un'esecuzione della pipeline, fare clic sull'icona del consumo accanto all'esecuzione. 

![Screenshot che mostra dove è possibile visualizzare le risorse utilizzate da una pipeline.](media/monitor-visually/monitor-consumption-1.png)

Facendo clic sull'icona viene visualizzato un report sul consumo delle risorse usate dall'esecuzione della pipeline. 

![Monitorare l'utilizzo](media/monitor-visually/monitor-consumption-2.png)

È possibile inserire questi valori nel [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/details/data-factory/) per stimare il costo dell'esecuzione della pipeline. Per altre informazioni sui prezzi di Azure Data Factory, vedere [Informazioni sui prezzi](pricing-concepts.md).

> [!NOTE]
> I valori restituiti dal calcolatore prezzi sono solo stimati e non rispecchiano l'importo esatto che verrà fatturato da Azure Data Factory 

## <a name="gantt-views"></a>Visualizzazioni Gantt

Usare le visualizzazioni Gantt per visualizzare rapidamente le pipeline e le esecuzioni attività.

![Esempio di un diagramma di Gantt](media/monitor-visually/gantt1.png)

È possibile esaminare la visualizzazione Gantt per singola pipeline o raggruppare in base alle annotazioni o ai tag creati nelle pipeline.

![Annotazioni nel diagramma di Gantt](media/monitor-visually/gantt2.png)

La lunghezza della barra indica la durata della pipeline. È anche possibile selezionare la barra per visualizzare altri dettagli.

![Durata nel diagramma di Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Presentazioni guidate
Selezionare l'icona **Informazioni** in basso a sinistra. Selezionare quindi **Guided Tours** (Presentazioni guidate) per visualizzare istruzioni dettagliate su come monitorare le esecuzioni attività e della pipeline.

![Presentazioni guidate](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Avvisi

È possibile generare avvisi sulle metriche supportate in Data Factory. Per iniziare, selezionare **Monitor** > **Alerts & metrics** (Monitoraggio > Avvisi e metriche) nella pagina di monitoraggio di Data Factory.

![Pagina di monitoraggio di Data Factory](media/monitor-visually/start-page.png)

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Creare avvisi

1.  Selezionare **Nuova regola di avviso** per creare un nuovo avviso.

    ![Pulsante Nuova regola di avviso](media/monitor-visually/new-alerts.png)

1.  Specificare il nome della regola e selezionare la gravità dell'avviso.

    ![Caselle per il nome e la gravità della regola](media/monitor-visually/name-and-severity.png)

1.  Selezionare i criteri di avviso.

    ![Casella per i criteri di destinazione](media/monitor-visually/add-criteria-1.png)

    ![Screenshot che mostra dove si seleziona una metrica per configurare la condizione di avviso.](media/monitor-visually/add-criteria-2.png)

    ![Elenco dei criteri](media/monitor-visually/add-criteria-3.png)

    È possibile creare avvisi per diverse metriche, incluse quelle per il numero/dimensioni delle entità ADF, le esecuzioni di attività/pipeline/trigger, l'utilizzo della CPU Integration Runtime (IR), il numero di memoria/nodo/coda dei pacchetti, nonché le esecuzioni di pacchetti SSIS e le operazioni di avvio/arresto del runtime di integrazione SSIS.

1.  Configurare la logica degli avvisi. È possibile creare un avviso per la metrica selezionata per tutte le pipeline e attività corrispondenti. È anche possibile selezionare un tipo di attività, un nome di attività, un nome di pipeline o un tipo di errore specifico.

    ![Opzioni per la configurazione della logica degli avvisi](media/monitor-visually/alert-logic.png)

1.  Configurare notifiche tramite posta elettronica, SMS, push, voce per l'avviso. Creare un gruppo di azioni o sceglierne uno esistente per le notifiche di avviso.

    ![Opzioni per la configurazione delle notifiche](media/monitor-visually/configure-notification-1.png)

    ![Opzioni per l'aggiunta di una notifica](media/monitor-visually/configure-notification-2.png)

1.  Creare la regola di avviso.

    ![Opzioni per la creazione di una regola di avviso](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e sulla gestione delle pipeline, vedere l'articolo su come [monitorare e gestire pipeline a livello di codice](https://docs.microsoft.com/azure/data-factory/monitor-programmatically).
