---
title: Esempi di utilizzo del connettore del flusso (anteprima) di Microsoft Azure Data Explorer
description: Informazioni su alcuni esempi comuni di utilizzo del connettore Microsoft Flow.Learn some common Microsoft Flow connector usage examples.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521724"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Esempi di utilizzo del connettore di Microsoft Flow (anteprima)Microsoft Flow connector (Preview) usage examples

Il connettore di flusso di Azure Data Explorer consente a Esplora dati di Azure di usare le funzionalità di flusso di [Microsoft Power Automate](https://flow.microsoft.com/) per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata. Questo documento contiene diversi esempi comuni di utilizzo del connettore microsoft Flow.This document contains several common Microsoft Flow connector usage examples.

Per ulteriori informazioni, vedere [Connettore Microsoft Flow (anteprima)](flow.md).

* [Connettore Microsoft Flow e SQL](#microsoft-flow-connector-and-sql)
* [Eseguire il push dei dati nel set di dati di Power BIPush data to Power BI dataset](#push-data-to-power-bi-dataset)
* [Query condizionali](#conditional-queries)
* [Inviare più diagrammi di flusso di Azure Data Explorert](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Connettore Microsoft Flow e SQL

Usare il connettore Microsoft Flow per eseguire query sui dati e aggregarlo in un database SQL.

> [!Note]
> L'inserimento SQL viene eseguito separatamente per ogni riga. Utilizzare il connettore Microsoft Flow solo per piccole quantità di dati di output. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

## <a name="push-data-to-power-bi-dataset"></a>Eseguire il push dei dati nel set di dati di Power BIPush data to Power BI dataset

Il connettore Microsoft Flow può essere usato insieme al connettore Power BI per eseguire il push dei dati dalle query Kusto ai set di dati di streaming di Power BI.

1. Creare una nuova azione Esegui query ed elenca risultati.
1. Selezionare **Nuovo passaggio**.
1. Selezionare **Aggiungi un'azione** e cercare Power BI.
1. Selezionare **Power BI**.
1. Selezionare **Aggiungi righe a un set di dati**. 

    ![Connettore Power BI di flusso](./media/flow-usage/flow-powerbiconnector.png)
1. Immettere **Workspace**, **Dataset**e **Table** in cui verrà inserito i dati.
1. Nella finestra di dialogo del contenuto dinamico, aggiungere un payload contenente lo schema del set di dati e i risultati della query Kusto pertinenti.

    ![Campi di Power BI di flusso](./media/flow-usage/flow-powerbifields.png)

Flow applica automaticamente l'azione di Power BI per ogni riga della tabella dei risultati della query kusto. 

![Azione di Power BI per il flusso per ogni rigaFlow Power BI action for each row](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Query condizionali

I risultati delle query Kusto possono essere utilizzati come input o condizioni per le azioni di flusso successive.

Nell'esempio seguente viene eseguita una query su Kusto per gli eventi imprevisti che si sono verificati durante l'ultimo giorno. Per ogni evento imprevisto risolto, viene inviato un messaggio slack e viene creata una notifica push.
Per ogni incidente ancora attivo, Kusto viene interrogato per ulteriori informazioni su incidenti simili. Invia tali informazioni come messaggio di posta elettronica e apre un'attività TFS correlata.

Seguire queste istruzioni per creare un flusso simile:Follow these instructions to create a similar Flow:

1. Creare una nuova azione Esegui query ed elenca risultati.
1. Selezionare **Nuovo passaggio**.
1. Selezionare **Controllo condizione**.
1. Nella finestra del contenuto dinamico, selezionare il parametro che si desidera utilizzare come condizione per le azioni successive.
1. Selezionare il tipo di *Relazione* e *Valore* per impostare una condizione specifica nel parametro specificato.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow applica questa condizione a ogni riga della tabella dei risultati della query.
1. Aggiungere azioni per quando la condizione è true e false.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

È possibile utilizzare i valori dei risultati della query Kusto come input per le azioni successive. Selezionare i valori dei risultati dalla finestra del contenuto dinamico.
Nell'esempio seguente, è stata aggiunta un'azione Slack - Post messaggio e Visual Studio - Creare una nuova azione elemento di lavoro contenente i dati della query Kusto.

![Slack - Azione Post messaggio](./media/flow-usage/flow-slack.png)

![Azione di Visual Studio](./media/flow-usage/flow-visualstudio.png)

In questo esempio, se un evento imprevisto è ancora attivo, eseguire nuovamente una query su Kusto per ottenere informazioni su come sono stati risolti in passato gli eventi imprevisti dalla stessa origine.

![Query della condizione di flusso](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

Visualizza queste informazioni come grafico a torta e inviale via email al team.

![Posta elettronica della condizione di flusso](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Inviare più diagrammi di flusso di Azure Data Explorert

1. Creare un nuovo flusso con il trigger di ricorrenza e definire l'intervallo del flusso e la frequenza. 
1. Aggiungere un nuovo passaggio, con una o più azioni Kusto - Esegui query e visualizza i risultati. 

    ![Eseguire più query in un flussoRun several queries in a flow](./media/flow-usage/flow-severalqueries.png)
1. Per ogni kusto - eseguire una query e visualizzare il risultato, definire i seguenti campi:
    * URL cluster
    * Nome database
    * Tipo di query e grafico (tabella HTML, grafico a torta, grafico a barre, grafico a barre o immettere un valore personalizzato).

    ![Visualizza i risultati con più allegati](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Aggiungere un'azione Invia un messaggio di posta elettronica (v2):Add a Send an email (v2) action: 
    1. Nella sezione del corpo, selezionare l'icona della vista codice.
    1. Nel campo **Body** inserire il BodyHtml richiesto in modo che il risultato visualizzato della query sia incluso nel corpo del messaggio di posta elettronica.
    1. Per aggiungere un allegato al messaggio di posta elettronica, aggiungere Nome allegato e Contenuto allegato.
    
    ![Inviare più allegati tramite posta elettronica](./media/flow-usage/flow-email-multiple-attachments.png)

    Per istruzioni complete sulla creazione di un'azione di posta elettronica, vedere [Email Kusto risultati della query](flow.md#email-kusto-query-results). 

Risultati:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul connettore App per la logica di [Microsoft Azure Explorer,](https://docs.microsoft.com/azure/kusto/tools/logicapps) un altro modo per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata.
