---
title: Connettore del flusso di Esplora dati di Microsoft Azure (anteprima)Microsoft Azure Data Explorer Flow connector (Preview)
description: Informazioni sull'utilizzo del connettore Microsoft Flow per creare flussi di attività pianificate o attivate automaticamente.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397119"
---
# <a name="microsoft-flow-connector-preview"></a>Connettore Microsoft Flow (anteprima)Microsoft Flow connector (Preview)

Il connettore di flusso di Azure Data Explorer consente a Esplora dati di Azure di usare le funzionalità di flusso di [Microsoft Power Automate](https://flow.microsoft.com/) per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata.

Gli scenari di utilizzo comuni includono:Common usage scenarios include:

* Invio di report giornalieri contenenti tabelle e grafici
* Impostazione delle notifiche in base ai risultati della query
* Pianificazione dei comandi di controllo nei cluster
* Esportazione e importazione di dati tra Azure Data Explorer e altri database 

Per ulteriori informazioni, vedere Esempi di [utilizzo del connettore Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Accesso 

1. Accedere a [Microsoft Power Automate](https://flow.microsoft.com/).

1. Quando ti connetti per la prima volta, ti verrà richiesto di accedere.

1. Selezionare **Accedi** e immettere le credenziali.

![Finestra di dialogo di accesso](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentication

È possibile eseguire l'autenticazione con credenziali utente o un'applicazione AAD.

> [!Note]
> Assicurarsi che l'applicazione sia [un'applicazione AAD](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) e sia autorizzata a eseguire query nel cluster.

1. Selezionare i tre puntini in alto a ![destra del connettore Microsoft Flow: Aggiungere una connessione](./media/flow/flow-addconnection.png)

1. Selezionare **Aggiungi nuova connessione** e quindi Connetti con entità **servizio**.
![Finestra di dialogo di accesso](./media/flow/flow-signin.png)

1. Immettere le informazioni necessarie:
    * Nome connessione: un nome descrittivo e significativo per la nuova connessione
    * ID client: ID applicazione
    * Segreto client: chiave dell'applicazioneClient Secret: Your application key
    * Tenant: l'ID della directory AAD in cui è stata creata l'applicazione. Ad esempio, l'ID tenant Microsoft è: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Autenticazione applicazione](./media/flow/flow-appauth.png)

Al termine dell'autenticazione, si noterà che il flusso utilizza la connessione appena aggiunta.

![Autenticazione dell'applicazione completata](./media/flow/flow-appauthcomplete.png)

D'ora in equi, questo flusso verrà eseguito utilizzando queste credenziali dell'applicazione.

## <a name="find-the-azure-kusto-connector"></a>Trovare il connettore di Azure KustoFind the Azure Kusto connector

Per utilizzare il connettore di Microsoft Flow, è innanzitutto necessario aggiungere un trigger. Un trigger può essere definito in base a un periodo di tempo ricorrente o come risposta a un'azione di flusso precedente.

1. [Creare un nuovo flusso](https://flow.microsoft.com/manage/flows/new) oppure, dalla home page, selezionare l'azione **Flussi personali** e quindi selezionare **Nuovo**.

    ![Creare un nuovo flusso](./media/flow/flow-newflow.png)

1. Aggiungi a campo Pianificato da.

    ![Nuovo flusso pianificato](./media/flow/flow-scheduled-from-blank.png)

1. Immettere le informazioni necessarie nella pagina Crea un flusso pianificato.
    ![Creare un flusso pianificatoBuild a scheduled flow](./media/flow/flow-build-scheduled-flow.png)
1. Selezionare **Create** (Crea).
1. Selezionare **+ nuovo passaggio**.
1. Nella casella di ricerca, inserisci "Kusto".

    ![Selezionare le azioni del flusso](./media/flow/flow-actions.png)

1. Selezionare **Azure Data Explorer**.

## <a name="flow-actions"></a>Azioni flusso

Quando si apre il connettore di Azure Data Explorer, è possibile aggiungere tre possibili azioni al flusso.

In questa sezione vengono descritte le funzionalità e i parametri per ogni azione di Microsoft Flow.

![Eseguire il flusso delle azioni di Esplora dati di AzureFlow Azure Data Explorer actions](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Eseguire il comando di controllo e visualizzare i risultati

Utilizzare il comando Esegui controllo e l'azione Visualizza risultati per eseguire un comando di [controllo](https://docs.microsoft.com/azure/kusto/management/index).

1. Specificare l'URL del cluster. Ad esempio, usare https://clusterName.eastus.kusto.windows.net
1. Immettere il nome del database.
1. Specificare il comando di controllo:
    * Selezionare il contenuto dinamico dalle app e dai connettori usati nel flusso
    * Aggiungere un'espressione per accedere, convertire e confrontare i valoriAdd an expression to access, convert, and compare values
1. Per inviare i risultati di questa azione tramite posta elettronica come tabella o grafico, specificare il tipo di grafico, che può essere:
    * Una tabella HTML
    * Un grafico a torta
    * Un grafico temporale
    * Un grafico a barre

![Eseguire il comando di controllo del flusso](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

### <a name="run-query-and-list-results"></a>Eseguire query ed elencare i risultati

> [!Note]
> Se la query inizia con un punto (ovvero è un comando di [controllo](https://docs.microsoft.com/azure/kusto/management/index)), utilizzare il [comando Esegui controllo e visualizzare i risultati](#run-control-command-and-visualize-results).

Questa azione invia una query al cluster Kusto. Le azioni aggiunte successivamente scorrono su ogni riga dei risultati della query.

L'esempio seguente attiva una query ogni minuto e invia un messaggio di posta elettronica in base ai risultati della query. La query controlla il numero di righe nel database e quindi invia un messaggio di posta elettronica solo se il numero di righe è maggiore di 0. 

![Eseguire i risultati dell'elenco di queryRun query list results](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Se la colonna ha più linee, il connettore verrà eseguito per ogni linea nella colonna.

### <a name="run-query-and-visualize-results"></a>Eseguire query e visualizzare i risultati
        
> [!Note]
> Se la query inizia con un punto (ovvero è un comando di [controllo](https://docs.microsoft.com/azure/kusto/management/index)), utilizzare il [comando Esegui controllo e visualizzare i risultati](#run-control-command-and-visualize-results).
        
Utilizzare l'azione Esegui query e visualizza risultati per visualizzare il risultato della query Kusto come tabella o grafico. Ad esempio, utilizzare questo flusso per ricevere report ICM giornalieri tramite posta elettronica. 
    
In questo esempio, i risultati della query vengono restituiti come tabella HTML.
            
![Eseguire query e visualizzare i risultati](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Nel campo *Nome cluster* immettere l'URL del cluster.

## <a name="email-kusto-query-results"></a>Invia i risultati delle query con Kusto tramite posta elettronica

È possibile includere un passaggio in qualsiasi flusso per inviare report tramite posta elettronica a qualsiasi indirizzo di posta elettronica. 

1. Selezionare **Nuovo passaggio** per aggiungere un nuovo passaggio al flusso.
1. Nel campo di ricerca immettere Office 365 e selezionare **Office 365 Outlook**.
1. Selezionare **Invia un messaggio di posta elettronica (V2)**.
1. Immettere l'indirizzo di posta elettronica a cui si desidera inviare il report di posta elettronica.
1. Immettere l'oggetto dell'e-mail.
1. Selezionare **la vista Codice**.
1. Posizionare il cursore nel campo *Corpo* e selezionare **Aggiungi contenuto dinamico**.
1. Selezionare **BodyHtml**.
    ![Inviare posta elettronica](./media/flow/flow-send-email.png)
1. Selezionare **Mostra le opzioni avanzate**.
1. Nel campo *Nome allegati -1* selezionare **Nome allegato**.
1. Nel campo *Contenuto allegati* selezionare **Contenuto allegato**.
1. Se necessario, aggiungere altri allegati. 
1. Se necessario, impostare il livello di importanza.
1. Selezionare **Salva**.

![Send email](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Verificare se il flusso è riuscito

Per verificare se il flusso è riuscito, vedere la cronologia di esecuzione del flusso:To check if your flow succeeded, see the flow's run history:
1. Passare alla home page di [Microsoft Flow](https://flow.microsoft.com/).
1. Dal menu principale, selezionare [I miei flussi](https://flow.microsoft.com/manage/flows).
    ![Pagina Flussi personali](./media/flow/flow-myflows.png)
1. Nella riga del flusso che si desidera analizzare selezionare l'icona Altri comandi e quindi **Cronologia di esecuzione**.

    ![Menu Cronologia esecuzione](./media/flow//flow-runhistory.png)

    Tutte le esecuzioni del flusso sono elencate con l'ora di inizio, la durata e lo stato.
    ![Pagina dei risultati della cronologia di esecuzione](./media/flow/flow-runhistoryresults.png)

    Per informazioni dettagliate sul flusso, nella pagina [Flussi personali](https://flow.microsoft.com/manage/flows) selezionare il flusso da analizzare.

    ![Pagina dei risultati completi della cronologia delle visualizzazioni](./media/flow/flow-fulldetails.png) 

Per verificare il motivo per cui un'esecuzione non è riuscita, selezionare l'ora di inizio dell'esecuzione. Viene visualizzato il flusso e il passaggio del flusso non riuscito è indicato da un punto esclamativo rosso. Espandere il passaggio non riuscito per visualizzarne i dettagli. Il riquadro di destra contiene informazioni sull'errore in modo che sia possibile risolverlo.

![Errore di flusso](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Eccezioni di timeout

Il flusso può avere esito negativo e restituire un'eccezione "RequestTimeout" se viene eseguito per più di sette minuti.

Ulteriori informazioni sulle [limitazioni](#limitations)di Microsoft Flow .
    
La stessa query può essere eseguita correttamente in Azure Data Explorer in cui il tempo non è limitato e può essere modificato.
            
L'eccezione "RequestTimeout" è illustrata nell'immagine seguente:
    
![Errore di eccezione di timeout richiesta di flusso](./media/flow/flow-requesttimeout.png)
    
Per risolvere un problema di timeout, provare a rendere la query più efficiente in modo che venga eseguita più velocemente o separarla in blocchi. Ogni blocco può essere eseguito su una parte diversa della query.

Per ulteriori informazioni, leggere le informazioni sulle procedure consigliate per [le query.](https://docs.microsoft.com/azure/kusto/query/best-practices)

## <a name="limitations"></a>Limitazioni

* I risultati restituiti al cliente sono limitati a 500.000 record. La memoria complessiva per tali record non può superare il tempo di esecuzione di 64 MB e sette minuti.
* Il connettore non supporta gli operatori [fork](https://docs.microsoft.com/azure/kusto/query/forkoperator) e [facet.](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* Flow funziona meglio su Microsoft Edge e Chrome.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul connettore App per la logica di [Microsoft Azure Explorer,](https://docs.microsoft.com/azure/kusto/tools/logicapps) un altro modo per eseguire automaticamente query e comandi Kusto come parte di un'attività pianificata o attivata.
