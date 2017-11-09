---
title: Chiamare una funzione di Azure da Microsoft Flow | Microsoft Docs
description: Creare un connettore personalizzato e quindi chiamare una funzione usando tale connettore.
services: functions
keywords: app cloud, servizi cloud, Microsoft Flow, processi aziendali, applicazione aziendale
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 120f5d69441c5e01ffafbdb8dccb179bf00bdb0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-microsoft-flow"></a>Chiamare una funzione da Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) facilita l'automazione dei flussi di lavoro e dei processi aziendali tra le app e i servizi preferiti dagli utenti. Gli sviluppatori professionisti possono usare Funzioni di Azure per estendere le funzionalità di Microsoft Flow, evitando ai creatori di app del flusso i dettagli tecnici.

In questo argomento si crea un flusso basato su uno scenario di manutenzione per le turbine eoliche. L'argomento mostra come chiamare la funzione definita in [Creare una definizione OpenAPI per una funzione](functions-openapi-definition.md). La funzione determina se una riparazione urgente di una turbina eolica è conveniente. Se risulta conveniente, il flusso invia un messaggio di posta elettronica per consigliare la riparazione.

Per informazioni sulla chiamata alla stessa funzione da PowerApps, vedere [Chiamare una funzione da PowerApps](functions-powerapps-scenario.md).

In questo argomento si apprenderà come:

> [!div class="checklist"]
> * Creare un elenco in SharePoint.
> * Esportare una definizione API.
> * Aggiungere una connessione all'API.
> * Creare un flusso per inviare un messaggio di posta elettronica se la riparazione è conveniente.
> * Eseguire il flusso.

## <a name="prerequisites"></a>Prerequisiti

+ Un [account di Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) attivo con le stesse credenziali di accesso dell'account di Azure. 
+ SharePoint, usato come origine dati per questo flusso. Iscriversi a [una versione di valutazione gratuita di Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) se l'utente non ha già SharePoint.
+ Completare l'esercitazione [Creare una definizione OpenAPI per una funzione](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Crea un elenco di SharePoint
Iniziare creando un elenco da usare come origine dati per il flusso. L'elenco è composto dalle colonne seguenti.

| Colonna dell'elenco     | Tipo di dati           | Note                                    |
|-----------------|---------------------|------------------------------------------|
| **Titolo**           | Riga di testo singola | Nome della turbina                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Number              | Output della turbina, in kWh            |
| **ServiceRequired** | Sì/No              |                                          |
| **EstimatedEffort** | Number              | Tempo stimato per la riparazione, espresso in ore |

1. Nel sito di SharePoint, fare clic o toccare **Nuovo**, quindi **Elenco**.

    ![Creare un nuovo elenco di SharePoint](./media/functions-flow-scenario/new-list.png)

2. Immettere il nome `Turbines`, quindi fare clic o toccare **Crea**.

    ![Specificare un nome per il nuovo elenco](./media/functions-flow-scenario/create-list.png)

    Viene creato l'elenco **Turbine** con il campo predefinito **Titolo**.

    ![Elenco Turbine](./media/functions-flow-scenario/initial-list.png)

3. Fare clic o toccare ![l'icona Nuovo elemento](./media/functions-flow-scenario/icon-new.png) quindi **Data**.

    ![Aggiungere una riga singola del campo di testo](./media/functions-flow-scenario/add-column.png)

4. Immettere il nome `LastServiceDate`, quindi fare clic o toccare **Crea**.

    ![Creare la colonna LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Ripetere gli ultimi due passaggi per le altre tre colonne nell'elenco:

    1. **Numero** > "MaxOutput"

    2. **Sì/No** > "ServiceRequired"

    3. **Numero** > "EstimatedEffort"

L'operazione per ora è conclusa. Viene visualizzato un elenco vuoto simile alla figura seguente. Aggiungerei i dati all'elenco dopo aver creato il flusso.

![Elenco vuoto](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Aggiungere una connessione all'API
L'API personalizzata, nota anche come connettore personalizzato, è disponibile in Microsoft Flow, ma è prima necessario stabilire una connessione all'API per poterla usare in un flusso.

1. In [flow.microsoft.com](https://flow.microsoft.com) fare clic sull'icona a forma di ingranaggio, in alto a destra, quindi fare clic su **connessioni**.

    ![Connessioni di flusso](media/functions-flow-scenario/flow-connections.png)

1. Fare clic su **Crea connessione**, scorrere verso il basso fino al connettore **Turbine Repair** (Riparazione turbina) e quindi fare clic su di esso.

    ![Creare la connessione](media/functions-flow-scenario/create-connection.png)

1. Immettere la chiave API e fare clic su **Crea connessione**.

    ![Immettere la chiave API e creare](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Se si condivide il flusso con altri utenti, ogni persona che lavora al flusso o lo usa deve immettere anche la chiave API per la connessione all'API. Questo comportamento potrebbe cambiare in futuro e questo argomento verrà aggiornato di conseguenza.


## <a name="create-a-flow"></a>Creare un flusso

A questo punto si è pronti per creare un flusso che usi il connettore personalizzato e l'elenco di SharePoint creato.

### <a name="add-a-trigger-and-specify-a-condition"></a>Aggiungere un trigger e specificare una condizione

Prima di tutto creare un flusso da zero, senza un modello, e aggiungere un *trigger* che viene attivato quando viene creato un elemento nell'elenco di SharePoint. Aggiungere quindi una *condizione* per determinare le operazioni successive.

1. In [flow.microsoft.com](https://flow.microsoft.com) fare clic su **Flussi personali**, quindi **Crea da zero**.

    ![Creare da zero](media/functions-flow-scenario/create-from-blank.png)

2. Fare clic sul trigger SharePoint **Alla creazione di un elemento**.

    ![Scegliere un trigger](media/functions-flow-scenario/choose-trigger.png)

    Se non è già stato effettuato l'accesso a SharePoint, verrà richiesto di farlo.

3. In **Indirizzo sito** immettere il nome del sito di SharePoint e in **Nome elenco** immettere l'elenco che contiene i dati della turbina.

    ![Scegliere un trigger](media/functions-flow-scenario/site-list.png)

4. Fare clic su **Nuovo passaggio**, quindi su **Aggiungi una condizione**.

    ![Add a condition](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow aggiunge due rami al flusso: **If yes** (Se sì) e **If no** (Se no). Aggiungere passaggi a uno o a entrambi i rami dopo aver definito la condizione per cui si desidera trovare una corrispondenza.

    ![Rami della condizione](media/functions-flow-scenario/condition-branches.png)

5. Nella scheda **Condizione** fare clic nella prima casella, quindi selezionare **ServiceRequired** nella finestra di dialogo **Contenuto dinamico**.

    ![Selezionare il campo per la condizione](media/functions-flow-scenario/condition1-field.png)

6. Immettere il valore `True` per la condizione.

    ![Immettere true per la condizione](media/functions-flow-scenario/condition1-yes.png)

    Il valore viene visualizzato come `Yes` o `No` nell'elenco di SharePoint, ma viene archiviato come valore *booleano*, `True` o `False`. 

7. Fare clic su **Crea flusso** nella parte superiore della pagina. Assicurarsi di fare clic periodicamente su **Aggiorna flusso**.

Per tutti gli elementi creati nell'elenco, il flusso controlla che il campo **ServiceRequired** sia impostato su `Yes`, quindi passa al ramo **If yes** (Se sì) o **If no** (Se no) in base alle esigenze. Per risparmiare tempo, in questo argomento l'utente deve specificare solo azioni per il ramo **If yes** (Se sì).

### <a name="add-the-custom-connector"></a>Aggiungere un connettore personalizzato

Aggiungere ora il connettore personalizzato che chiama la funzione in Azure. Aggiungere il connettore personalizzato al flusso come un connettore standard. 

1. Nel ramo **If yes** (Se sì) fare clic su **Aggiungi un'azione**.

    ![Aggiungere un'azione](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Nella finestra di dialogo **Scegli un'azione** cercare `Turbine Repair`, quindi selezionare l'azione **Turbine Repair - Calculates costs** (Riparazione turbina - Calcola costi).

    ![Scegliere un'azione](media/functions-flow-scenario/choose-turbine-repair.png)

    La figura seguente mostra la scheda che viene aggiunta al flusso. I campi e le descrizioni provengono dalla definizione di OpenAPI per il connettore.

    ![Calcola i valori predefiniti dei costi](media/functions-flow-scenario/calculates-costs-default.png)

3. Nella scheda **Calculates costs** (Calcola costi) usare la finestra di dialogo **Contenuto dinamico** per selezionare gli input per la funzione. Microsoft Flow mostra i campi numerici ma non il campo della data, perché la definizione OpenAPI specifica che **Ore** e **Capacità** contengono dati numerici.

    Per **Ore** selezionare **EstimatedEffort** e per **Capacità** selezionare **MaxOutput**.

    ![Scegliere un'azione](media/functions-flow-scenario/calculates-costs-fields.png)

     Aggiungere ora un'altra condizione basata sull'output della funzione.

4. Nella parte inferiore del ramo **If yes** (Se sì) fare clic su **Altro**, quindi **Aggiungi una condizione**.

    ![Add a condition](media/functions-flow-scenario/condition2-add.png)

5. Nella scheda **Condition 2** (Condizione 2) fare clic nella prima casella, quindi selezionare **Messaggio** dalla finestra di dialogo **Contenuto dinamico**.

    ![Selezionare il campo per la condizione](media/functions-flow-scenario/condition2-field.png)

6. Immettere il valore `Yes`. Il flusso passa al ramo successivo **If yes** (Se sì) o **If no** (Se no) a seconda che il messaggio restituito dalla funzione consigli di effettuare la riparazione o meno. 

    ![Immettere yes per la condizione](media/functions-flow-scenario/condition2-yes.png)

Il flusso ora sarà simile all'immagine seguente.

![Immettere yes per la condizione](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Inviare un messaggio di posta elettronica in base ai risultati della funzione

A questo punto nel flusso la funzione ha restituito un valore per **Messaggio**, ovvero `Yes` o `No`, insieme ad altre informazioni sui costi e i potenziali ricavi. Nel ramo **If yes** (Se sì) della seconda condizione l'utente invierà un messaggio di posta elettronica, ma è possibile eseguire altre operazioni, quali riscrivere l'elenco di SharePoint o avviare un [processo di approvazione](https://flow.microsoft.com/documentation/modern-approvals/).

1. Nel ramo **If yes** (Se sì) della seconda condizione, fare clic su **Aggiungi un'azione**.

    ![Aggiungere un'azione](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Nella finestra di dialogo **Scegli un'azione** cercare `email`, quindi selezionare un'azione per inviare un messaggio di posta elettronica in base al sistema di posta elettronica usato, in questo caso Outlook.

    ![Outlook invia un messaggio di posta elettronica](media/functions-flow-scenario/outlook-send-email.png)

3. Nella scheda **Invia un messaggio di posta elettronica** comporre un messaggio di posta elettronica. Immettere un nome valido all'interno dell'organizzazione per il campo **To** (A). Nell'immagine seguente è possibile visualizzare gli altri campi che sono una combinazione di testo e token della finestra di dialogo **Contenuto dinamico**. 

    ![Campi del messaggio di posta elettronica](media/functions-flow-scenario/email-fields.png)

    Il token **Titolo** proviene dall'elenco di SharePoint, mentre la funzione restituisce **CostToFix** e **RevenueOpportunity**.

    Il flusso completato dovrebbe apparire come nell'immagine seguente; per risparmiare spazio non è stato incluso il primo ramo **If no** (Se no).

    ![Completare il flusso](media/functions-flow-scenario/complete-flow.png)

4. Fare clic su **Aggiorna flusso** nella parte superiore della pagina, quindi fare clic su **Fatto**.

## <a name="run-the-flow"></a>Eseguire il flusso

Dopo aver completato il flusso, aggiungere una riga all'elenco di SharePoint e vedere come risponde il flusso.

1. Tornare all'elenco di SharePoint e fare clic su **Modifica rapida**.

    ![Modifica rapida](media/functions-flow-scenario/quick-edit.png)

2. Immettere i valori seguenti nella griglia di modifica.

    | Colonna dell'elenco     | Valore           |
    |-----------------|---------------------|
    | **Titolo**           | Turbina 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Sì |
    | **EstimatedEffort** | 10 |

3. Fare clic su **Done**.

    ![Modifica rapida eseguita](media/functions-flow-scenario/quick-edit-done.png)

    Quando si aggiunge l'elemento, si attiva il flusso come si vedrà successivamente.

4. In [flow.microsoft.com](https://flow.microsoft.com) fare clic su **Flussi personali**, quindi sul flusso creato.

    ![Flussi personali](media/functions-flow-scenario/my-flows.png)

5. In **Cronologia di esecuzione** fare clic sull'esecuzione del flusso.

    ![Cronologia di esecuzione](media/functions-flow-scenario/run-history.png)

    Se l'esecuzione ha avuto esito positivo, è possibile esaminare le operazioni di flusso nella pagina successiva. Se l'esecuzione per qualche motivo non è riuscita, la pagina successiva conterrà informazioni sulla risoluzione dei problemi.

6. Espandere le schede per vedere le operazioni eseguite durante il flusso. Ad esempio, espandere la scheda **Calculates costs** (Calcola costi) per visualizzare gli input e gli output della funzione. 

    ![Input e output di Calcola costi](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Controllare l'account di posta elettronica per l'utente specificato nel campo **To** (A) della scheda **Invia un messaggio di posta elettronica**. Il messaggio di posta elettronica inviato dal flusso sarà simile all'immagine seguente.

    ![Messaggio di posta elettronica del flusso](media/functions-flow-scenario/flow-email.png)

    È possibile vedere come i token siano stati sostituiti con i valori corretti dell'elenco di SharePoint e della funzione.

## <a name="next-steps"></a>Passaggi successivi
In questo argomento si è appreso come:

> [!div class="checklist"]
> * Creare un elenco in SharePoint.
> * Esportare una definizione API.
> * Aggiungere una connessione all'API.
> * Creare un flusso per inviare un messaggio di posta elettronica se la riparazione è conveniente.
> * Eseguire il flusso.

Per altre informazioni su Microsoft Flow, vedere [Attività iniziali con Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Per informazioni su altri scenari interessanti che usano Funzioni di Azure, vedere [Chiamare una funzione da PowerApps](functions-powerapps-scenario.md) e [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md).