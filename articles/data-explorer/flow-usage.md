---
title: Esempi di utilizzo del connettore del flusso (anteprima) di Microsoft Azure Data Explorer
description: Informazioni su alcuni esempi comuni di utilizzo del connettore Microsoft Flow.Learn some common Microsoft Flow connector usage examples.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397200"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Esempi di utilizzo del connettore di Microsoft Flow (anteprima)Microsoft Flow connector (Preview) usage examples

Il connettore di flusso di Azure Data Explorer consente a Esplora dati di Azure di usare le funzionalità di flusso di [Microsoft Power Automate](https://flow.microsoft.com/) per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata. Questo documento contiene diversi esempi comuni di utilizzo del connettore microsoft Flow.This document contains several common Microsoft Flow connector usage examples.

Per ulteriori informazioni, vedere [Connettore Microsoft Flow (anteprima)](flow.md).

* [Connettore Microsoft Flow e SQL](#microsoft-flow-connector-and-sql)
* [Eseguire il push dei dati nel set di dati di Power BIPush data to Power BI dataset](#push-data-to-power-bi-dataset)
* [Query condizionali](#conditional-queries)
* [Inviare più diagrammi di flusso di Azure Data Explorert](#email-multiple-azure-data-explorer-flow-charts)
* [Inviare un messaggio di posta elettronica diverso a contatti diversi](#send-a-different-email-to-different-contacts)
* [Creare una tabella HTML personalizzata](#create-a-custom-html-table)

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

1. Creare un nuovo flusso con trigger "Ricorrenza" e definire l'intervallo del flusso e la frequenza. 
1. Aggiungere un nuovo passaggio, con una o più azioni Kusto - Esegui query e visualizza i risultati. 

    ![Eseguire più query in un flussoRun several queries in a flow](./media/flow-usage/flow-severalqueries.png)
1. Per ogni kusto - eseguire una query e visualizzare il risultato, definire i seguenti campi:
    * URL cluster (nel campo *Nome cluster)*
    * Nome database
    * Tipo di query e grafico (Tabella HTML/Grafico a torta/Grafico temporale/Grafico a barre/Inserisci valore personalizzato).

    ![Visualizza i risultati con più allegati](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > Nei campi *Nome cluster* immettere l'URL del cluster.

1. Aggiungere un'azione Invia un messaggio di posta elettronica. 
    * Nel campo *Corpo* inserire il corpo richiesto in modo che il risultato visualizzato della query sia incluso nel corpo del messaggio di posta elettronica.
    * Per aggiungere un allegato al messaggio di posta elettronica, aggiungere Nome allegato e Contenuto allegato.
    
    ![Inviare più allegati tramite posta elettronica](./media/flow-usage/flow-email-multiple-attachments.png)

    Per istruzioni complete sulla creazione di un'azione di posta elettronica, vedere [Email Kusto risultati della query](flow.md#email-kusto-query-results). 

Risultati:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Inviare un messaggio di posta elettronica diverso a contatti diversi

È possibile utilizzare Microsoft Flow per inviare diversi messaggi di posta elettronica personalizzati a diversi contatti. Gli indirizzi e-mail e il contenuto dell'e-mail sono il risultato di una query Kusto.

Esempio:

![Posta elettronica dinamica con una query Kusto](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

![Messaggio di posta elettronica dinamico nell'azione flussoDynamic email in the flow action](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Creare una tabella HTML personalizzata

È possibile sfruttare Microsoft Flow per creare e utilizzare elementi HTML personalizzati, ad esempio una tabella HTML personalizzata.

Nell'esempio seguente viene illustrato come creare una tabella HTML personalizzata. Le righe della tabella HTML saranno colorate in base al livello di log (come in Azure Data Explorer).

Seguire queste istruzioni per creare un flusso simile:Follow these instructions to create a similar Flow:

1. Creare una nuova azione Kusto - Esegui query ed elenca risultati.

    ![Elencare i risultati per una tabella HTML](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

1. Eseguire un ciclo sui risultati della query e creare il corpo della tabella HTML: 
    1. Per creare una variabile per contenere la stringa HTML, selezionare **Nuovo passaggio**
    1. Selezionare **Aggiungi un'azione** e cercare Variabili.Select Add an action and search for Variables. 
    1. Seleziona **variabili - Inizializza variabile**. 
    1. Inizializzare una variabile stringa come segue:

    ![Inizializzare una variabile](./media/flow-usage/flow-initializevariable.png)

1. Scorrere i risultati:
    1. Selezionare **Nuovo passaggio**.
    1. Selezionare **Aggiungi un'azione**.
    1. Cercare Variabili. 
    1. Seleziona **variabili - Aggiungi alla variabile stringa**. 
    1. Selezionare il nome della variabile inizializzata in precedenza e creare le righe della tabella HTML utilizzando i risultati della query. 
    Quando si selezionano i risultati della query, l'opzione Applica a ognuno viene aggiunta automaticamente.

    Nell'esempio seguente, `if` l'espressione viene utilizzata per definire lo stile di ogni riga:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. Creare il contenuto HTML completo: 
    1. Aggiungere una nuova azione all'esterno Applica a ogni. 
    Nell'esempio seguente l'azione utilizzata è Invia un messaggio di posta elettronica.
    1. Definire la tabella HTML utilizzando la variabile dei passaggi precedenti. 
    1. Se si invia un messaggio di posta elettronica, selezionare **Mostra opzioni avanzate** e, in HTML, selezionare **Sì**.

    ![Posta elettronica tabella HTML personalizzata](./media/flow-usage/flow-customhtmltablemail.png)

Risultato:

![Risultato della posta elettronica della tabella HTML personalizzata](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul connettore App per la logica di [Microsoft Azure Explorer,](https://docs.microsoft.com/azure/kusto/tools/logicapps) un altro modo per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata.
