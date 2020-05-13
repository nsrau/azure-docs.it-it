---
title: Aggiungere messaggi a una coda di archiviazione di Azure tramite Funzioni
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata da una richiesta HTTP e crea un messaggio in una coda di Archiviazione di Azure.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: 5ae282750580ed5b4e53e78c52ca285e40365fd3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122012"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Aggiungere messaggi a una coda di archiviazione di Azure tramite Funzioni

In Funzioni di Azure le associazioni di input e di output forniscono una modalità dichiarativa per rendere disponibili nel codice i dati di servizi esterni. In questa guida introduttiva viene usata un'associazione di output per creare un messaggio in una coda quando viene attivata una funzione da una richiesta HTTP. Usare il contenitore di archiviazione di Azure per visualizzare i messaggi della coda creati dalla funzione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Seguire le indicazioni in [Creare la prima funzione nel portale di Azure](functions-create-first-azure-function.md) e non eseguire il passaggio **Pulire le risorse**. La guida introduttiva crea l'app per le funzioni e la funzione da usare.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Aggiungere un binding di output

In questa sezione si usa l'interfaccia utente del portale per aggiungere un'associazione di output di archiviazione code alla funzione creata in precedenza. Questa associazione rende possibile la scrittura di codice minimo per la creazione di un messaggio in una coda. Non è necessario scrivere codice per attività come aprire una connessione di archiviazione, creare una coda o ottenere un riferimento a una coda. Queste attività vengono eseguite dal runtime di Funzioni di Azure e dall'associazione di output della coda.

1. Nel portale di Azure aprire la pagina dell'app per le funzioni creata in [Creare la prima funzione nel portale di Azure](functions-create-first-azure-function.md). Per aprire la pagina, cercare e selezionare **app per le funzioni**. Selezionare quindi l'app per le funzioni.

1. Selezionare l'app per le funzioni e quindi selezionare la funzione creata nella Guida introduttiva precedente.

1. Selezionare **integrazione**e quindi selezionare **+ Aggiungi output**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Creare un'associazione di output per la funzione." border="true":::

1. Selezionare il tipo di binding **archiviazione code di Azure** e aggiungere le impostazioni come specificato nella tabella che segue questa schermata: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Aggiungere un binding di output di Archiviazione code a una funzione nel portale di Azure." border="true":::
    
    | Impostazione      |  Valore consigliato   | Descrizione                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome del parametro del messaggio** | outputQueueItem | Nome del parametro di binding di output. | 
    | **Nome coda**   | outqueue  | Nome della coda a cui connettersi nell'account di archiviazione. |
    | **Connessione dell'account di archiviazione** | AzureWebJobsStorage | È possibile usare la connessione dell'account di archiviazione già usata dall'app per le funzioni oppure crearne una nuova.  |

1. Selezionare **OK** per aggiungere l'associazione.

Dopo aver definito un binding di output, è necessario ora aggiornare il codice in modo da usare il binding per aggiungere messaggi a una coda.  

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

In questa sezione si aggiunge il codice che scrive un messaggio nella coda di output. Il messaggio include il valore passato al trigger HTTP nella stringa di query. Se, ad esempio, la stringa di query include `name=Azure`, il messaggio della coda sarà *Name passed to the function: Azure*.

1. Nella funzione selezionare **codice + test** per visualizzare il codice della funzione nell'editor.

1. Aggiornare il codice della funzione a seconda del linguaggio della funzione:

    # <a name="c"></a>[C\#](#tab/csharp)

    Aggiungere un parametro **outputQueueItem** alla firma del metodo come illustrato nell'esempio seguente.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    Nel corpo della funzione, subito prima dell'istruzione `return`, aggiungere il codice che usa il parametro per creare un messaggio della coda.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Aggiungere il codice che usa il binding di output nell'oggetto `context.bindings` per creare un messaggio della coda. Aggiungere questo codice prima dell'istruzione `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Selezionare **Salva** per salvare le modifiche.

## <a name="test-the-function"></a>Testare la funzione

1. Dopo aver salvato le modifiche al codice, selezionare **test**.
1. Verificare che il test corrisponda all'immagine seguente e selezionare **Esegui**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Testare l'associazione di archiviazione della coda nel portale di Azure." border="true":::

    Si noti che in **Corpo della richiesta** è presente il valore di `name`*Azure*. Questo valore viene visualizzato nel messaggio della coda creato quando viene richiamata la funzione.
    
    Al posto di fare clic su **Esegui**, in questo caso è possibile chiamare la funzione immettendo un URL in un browser e specificando il valore di `name` nella stringa di query. Il metodo tramite browser è illustrato nella [guida introduttiva precedente](functions-create-first-azure-function.md#test-the-function).

1. Controllare i log per assicurarsi che la funzione abbia avuto esito positivo. 

Una nuova coda denominata **outqueue** viene creata nell'account di archiviazione dal runtime di funzioni quando viene usata per la prima volta l'associazione di output. Si userà l'account di archiviazione per verificare che la coda e un messaggio siano stati creati.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Trovare l'account di archiviazione connesso a AzureWebJobsStorage


1. Passare all'app per le funzioni e selezionare **Configuration (configurazione**).

1. In **Impostazioni applicazione**selezionare **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Individuare l'account di archiviazione connesso a AzureWebJobsStorage." border="true":::

1. Individuare e prendere nota del nome dell'account.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Individuare l'account di archiviazione connesso a AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Esaminare la coda di output

1. Nel gruppo di risorse per l'app per le funzioni selezionare l'account di archiviazione che si sta usando per questa Guida introduttiva.

1. In **servizio di Accodamento**selezionare **Code** e selezionare la coda denominata **outqueue**. 

   La coda contiene il messaggio creato dall'associazione di output della coda quando è stata eseguita la funzione attivata da HTTP. Se la funzione è stata richiamata con il valore predefinito di `name`*Azure*, il messaggio della coda è *Name passed to the function: Azure*.

1. Eseguire nuovamente la funzione e verrà visualizzato un nuovo messaggio nella coda.  

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata aggiunta un'associazione di output a una funzione esistente. Per altre informazioni sul binding all'archiviazione code, vedere [Associazioni della coda dell'archiviazione di Funzioni di Azure](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
