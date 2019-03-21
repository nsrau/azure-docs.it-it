---
title: Creare una funzione che si integra con le app per la logica di Azure
description: Creare una funzione che si integra con le app per la logica di Azure e i servizi cognitivi di Azure per classificare la valutazione dei tweet e inviare notifiche quando la valutazione è bassa.
services: functions, logic-apps, cognitive-services
keywords: flusso di lavoro, app cloud, servizi cloud, processi aziendali, integrazione di sistemi, enterprise application integration, EAI
author: craigshoemaker
manager: jeconnoc
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 5e0ef8287b7ce257cd551a1ace043ccbed72b50b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087092"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Creare una funzione che si integra con le app per la logica di Azure

Funzioni di Azure si integra con App per la logica di Azure in Progettazione app per la logica. L'integrazione consente di usare la potenza di calcolo di Funzioni nelle orchestrazioni con altri servizi di Azure e di terze parti. 

Questa esercitazione illustra come usare Funzioni con App per la logica e Servizi cognitivi in Azure per eseguire l'analisi del sentiment nei post di Twitter. Una funzione attivata tramite HTTP classifica i tweet come verde, giallo o rosso in base al punteggio del sentiment. Quando viene rilevato un livello di sentiment basso viene inviato un messaggio di posta elettronica. 

![immagine dei primi due passaggi dell'app in Progettazione app per la logica](media/functions-twitter-email/00-logic-app-overview.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una risorsa API per Servizi cognitivi.
> * Creare una funzione che classifica i sentiment nei tweet.
> * Creare un'app per la logica che si connette a Twitter.
> * Aggiungere il rilevamento dei sentiment all'app per la logica. 
> * Connettere l'app per la logica alla funzione.
> * Inviare un messaggio di posta elettronica in base alla risposta dalla funzione.

## <a name="prerequisites"></a>Prerequisiti

+ Un account [Twitter](https://twitter.com/) attivo. 
+ Un account [Outlook.com](https://outlook.com/) per l'invio delle notifiche.
+ Questo articolo usa per iniziare le risorse create in [Creare la prima funzione nel portale di Azure](functions-create-first-azure-function.md).  
Se queste procedure non sono state ancora completate, completarle ora per creare l'app per le funzioni.

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Le API Servizi cognitivi sono disponibili in Azure come singole risorse. Usare l'API Analisi del testo per rilevare il sentiment dei tweet sottoposti a monitoraggio.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

3. Fare clic su **Intelligenza artificiale e Machine Learning** > **Analisi del testo**. Usare quindi le impostazioni indicate nella tabella per creare la risorsa.

    ![Pagina di creazione della risorsa per Servizi cognitivi](media/functions-twitter-email/01-create-text-analytics.png)

    | Impostazione      |  Valore consigliato   | Descrizione                                        |
    | --- | --- | --- |
    | **Nome** | MyCognitiveServicesAccnt | Scegliere un nome dell'account univoco. |
    | **Posizione** | Stati Uniti occidentali | Usare la posizione più vicina. |
    | **Piano tariffario** | F0 | Iniziare dal livello più basso. Se si esauriscono le chiamate, passare a un livello superiore.|
    | **Gruppo di risorse** | myResourceGroup | Usare lo stesso gruppo di risorse per tutti i servizi in questa esercitazione.|

4. Fare clic su **Crea** per creare la risorsa. 

5. Fare clic su **Panoramica** e copiare il valore di **Endpoint** in un editor di testo. Questo valore viene usato durante la creazione di una connessione all'API Servizi cognitivi.

    ![Impostazioni di Servizi cognitivi](media/functions-twitter-email/02-cognitive-services.png)

6. Nella colonna di spostamento a sinistra fare clic su **Chiavi** e quindi copiare il valore di **Chiave 1** e conservarlo in un editor di testo. La chiave viene usata per connettere l'app per la logica all'API Servizi cognitivi. 
 
    ![Chiavi di Servizi cognitivi](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Creare l'app per le funzioni

Funzioni permette di ripartire il carico di lavoro delle attività di elaborazione in un flusso di lavoro di app per la logica. Questa esercitazione fa uso di una funzione attivata tramite HTTP per elaborare i punteggi attribuiti da Servizi cognitivi ai sentiment dei tweet e restituire un valore categoria.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Creare una funzione attivata tramite HTTP  

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Nel portale**.

    ![Pagina della guida introduttiva di Funzioni nel portale di Azure](media/functions-twitter-email/05-function-app-create-portal.png)

2. Selezionare quindi **Webhook e API** e fare clic su **Crea**. 

    ![Scegliere un trigger HTTP](./media/functions-twitter-email/06-function-webhook.png)

3. Sostituire il contenuto del file `run.csx` con il codice seguente e quindi fare clic su **Salva**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Questo codice di funzione restituisce una categoria colore in base al punteggio del sentiment ricevuto nella richiesta. 

4. Per testare la funzione, fare clic su **Test** a destra per espandere la scheda Test. Digitare un valore di  per il Corpo della richiesta`0.2` e quindi fare clic su **Esegui**. Nel corpo della risposta verrà restituito il valore **RED**. 

    ![Testare la funzione nel portale di Azure](./media/functions-twitter-email/07-function-test.png)

A questo punto è disponibile una funzione che classifica i punteggi dei sentiment. Nella fase successiva viene creata un'app per la logica che integra la funzione con l'API Twitter e Servizi cognitivi. 

## <a name="create-a-logic-app"></a>Creare un'app per la logica   

1. Nel portale di Azure fare clic sul pulsante **Nuovo** nell'angolo in alto a sinistra del portale.

2. Fare clic su **Web** > **App per la logica**.
 
3. Digitare quindi un valore per **Nome**, ad esempio `TweetSentiment`, e usare le impostazioni specificate nella tabella.

    ![Creare l'app per la logica nel portale di Azure](./media/functions-twitter-email/08-logic-app-create.png)

    | Impostazione      |  Valore consigliato   | Descrizione                                        |
    | ----------------- | ------------ | ------------- |
    | **Nome** | TweetSentiment | Scegliere un nome appropriato per l'app. |
    | **Gruppo di risorse** | myResourceGroup | Scegliere lo stesso gruppo di risorse esistente visto in precedenza. |
    | **Posizione** | Stati Uniti orientali | Scegliere una località vicina. |    

4. Dopo aver immesso i valori appropriati per le impostazioni, fare clic su **Crea** per creare l'app per la logica. 

5. Dopo aver creato l'app, fare clic sulla nuova app per la logica aggiunta al dashboard. In Progettazione app per la logica scorrere verso il basso e fare clic sul modello **App per la logica vuota**. 

    ![Modello App per la logica vuota](media/functions-twitter-email/09-logic-app-create-blank.png)

A questo punto è possibile usare Progettazione app per la logica per aggiungere servizi e trigger all'applicazione.

## <a name="connect-to-twitter"></a>Connettersi a Twitter

Creare prima di tutto una connessione all'account Twitter. L'app per la logica esegue il poll dei tweet, che attiva l'app da eseguire.

1. Nella finestra di progettazione, fare clic sul servizio **Twitter** e scegliere il trigger **Quando viene pubblicato un nuovo tweet**. Accedere all'account Twitter e autorizzare App per la logica all'uso dell'account.

2. Usare le impostazioni del trigger Twitter come specificato nella tabella. 

    ![Impostazioni del connettore Twitter](media/functions-twitter-email/10-tweet-settings.png)

    | Impostazione      |  Valore consigliato   | DESCRIZIONE                                        |
    | ----------------- | ------------ | ------------- |
    | **Testo di ricerca** | #Azure | Usare un hashtag che sia abbastanza comune per generare nuovi tweet nell'intervallo selezionato. Quando si usa un hashtag troppo comune con il livello gratuito, c'è il rischio di esaurire rapidamente la quota di transazioni nell'API Servizi cognitivi. |
    | **Interval** | 15 | Tempo trascorso tra le richieste di Twitter, in unità di frequenza. |
    | **Frequenza** | Minuto | Unità di frequenza usata per eseguire il poll di Twitter.  |

3.  Fare clic su **Salva** per connettersi all'account Twitter. 

A questo punto l'app è connessa a Twitter. Connettersi ora all'analisi del testo per rilevare il sentiment dei tweet raccolti.

## <a name="add-sentiment-detection"></a>Aggiungere il rilevamento dei sentiment

1. Selezionare **Nuovo passaggio** e quindi **Aggiungi un'azione**.

2. In **Scegliere un'azione** digitare **Analisi del testo** e quindi scegliere l'azione **Rileva sentiment**.
    
    ![Nuovo passaggio e quindi Aggiungi un'azione.](media/functions-twitter-email/11-detect-sentiment.png)

3. Digitare un nome per la connessione, ad esempio `MyCognitiveServicesConnection`, incollare la chiave dell'API Servizi cognitivi e l'endpoint di Servizi cognitivi conservato nell'editor di testo e fare clic su **Crea**.

    ![Nuovo passaggio e quindi Aggiungi un'azione.](media/functions-twitter-email/12-connection-settings.png)

4. Immettere quindi **Testo tweet** nella casella di testo e quindi fare clic su **Nuovo passaggio**.

    ![Definire il testo da analizzare](media/functions-twitter-email/13-analyze-tweet-text.png)

Dopo aver configurato il rilevamento dei sentiment, è possibile aggiungere una connessione alla funzione che utilizza l'output del punteggio dei sentiment.

## <a name="connect-sentiment-output-to-your-function"></a>Connettere l'output dei sentiment alla funzione

1. In Progettazione app per la logica fare clic su **Nuovo passaggio** > **Aggiungi un'azione**, filtrare in base a **Funzioni di Azure** e fare clic su **Scegliere una funzione di Azure**.

    ![Rileva sentiment](media/functions-twitter-email/14-azure-functions.png)
  
4. Selezionare l'app per le funzioni creata in precedenza.

    ![Selezionare la funzione](media/functions-twitter-email/15-select-function.png)

5. Selezionare la funzione creata per questa esercitazione.

    ![Selezionare la funzione](media/functions-twitter-email/16-select-function.png)

4. In **Corpo della richiesta** fare clic su **Punteggio** e quindi su **Salva**.

    ![Score](media/functions-twitter-email/17-function-input-score.png)

A questo punto, la funzione viene attivata quando l'app per la logica invia un punteggio di sentiment. La funzione restituisce quindi una categoria colore all'app per la logica. Aggiungere quindi l'invio di una notifica di posta elettronica quando la funzione restituisce un valore di sentiment **RED**. 

## <a name="add-email-notifications"></a>Aggiungere le notifiche di posta elettronica

L'ultima parte del flusso di lavoro consiste nell'attivare l'invio di un messaggio di posta elettronica quando il punteggio del sentiment è _RED_. Questo argomento fa uso di un connettore Outlook.com. Per usare un connettore Gmail o Office 365 Outlook è possibile seguire una procedura simile.   

1. In Progettazione app per la logica fare clic su **Nuovo passaggio** > **Aggiungi una condizione**. 

    ![Aggiungere una condizione all'app per la logica.](media/functions-twitter-email/18-add-condition.png)

2. Fare clic su **Scegliere un valore** e quindi su **Corpo**. Selezionare **è uguale a**, fare clic su **Scegliere un valore**, digitare `RED` e quindi fare clic su **Salva**. 

    ![Scegliere un'azione per la condizione.](media/functions-twitter-email/19-condition-settings.png)    

3. In **È true** fare clic su **Aggiungi un'azione**, cercare `outlook.com`, fare clic su **Invia un messaggio di posta elettronica** e accedere all'account Outlook.com.

    ![Configurare la posta elettronica per l'azione Invia un messaggio di posta elettronica.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Se non è disponibile un account Outlook.com, è possibile scegliere un altro connettore, ad esempio Gmail o Office 365 Outlook.

4. Nell'azione **Invia un messaggio di posta elettronica** usare le impostazioni di posta elettronica indicate nella tabella. 

    ![Configurare la posta elettronica per l'azione Invia un messaggio di posta elettronica.](media/functions-twitter-email/21-configure-email.png)
    
| Impostazione      |  Valore consigliato   | DESCRIZIONE  |
| ----------------- | ------------ | ------------- |
| **To** | Digitare l'indirizzo di posta elettronica | Indirizzo di posta elettronica che riceve la notifica. |
| **Oggetto** | Rilevato sentiment di tweet negativo  | Riga dell'oggetto della notifica di posta elettronica.  |
| **Corpo** | Testo tweet, Località | Fare clic sui parametri **Testo tweet** e **Località**. |

1. Fare clic su **Save**.

Ora che il flusso di lavoro è completo, è possibile abilitare l'app per la logica e provarne il funzionamento.

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

1. In Progettazione app per la logica fare clic su **Esegui** per avviare l'app.

2. Nella colonna di sinistra fare clic su **Panoramica** per visualizzare lo stato dell'app per la logica. 
 
    ![Stato di esecuzione dell'app per la logica](media/functions-twitter-email/22-execution-history.png)

3. (Facoltativo) Fare clic su una delle esecuzioni per visualizzarne i dettagli.

4. Passare alla funzione, visualizzare i log e verificare che i valori dei sentiment siano stati ricevuti ed elaborati.
 
    ![Visualizzare i log della funzione](media/functions-twitter-email/sent.png)

5. Quando viene rilevato un sentiment potenzialmente negativo, si riceve un messaggio di posta elettronica. Se non è stato ricevuto un messaggio di posta elettronica, è possibile modificare il codice della funzione per restituisca RED ogni volta:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Dopo aver verificato le notifiche di posta elettronica, ripristinare il codice originale:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Al termine di questa esercitazione è consigliabile disabilitare l'app per la logica. In questo modo è possibile evitare di incorrere in addebiti per le esecuzioni e di esaurire le transazioni nell'API Servizi cognitivi.

Come illustrato in questa esercitazione, integrare Funzioni in un flusso di lavoro di App per la logica è molto semplice.

## <a name="disable-the-logic-app"></a>Disabilitare l'app per la logica

Per disabilitare l'app per la logica, fare clic su **Panoramica** e quindi scegliere **Disabilita** nella parte superiore della schermata. La disabilitazione dell'app ne arresta l'esecuzione e permette di evitare addebiti senza doverla necessariamente eliminare.

![Log della funzione](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una risorsa API per Servizi cognitivi.
> * Creare una funzione che classifica i sentiment nei tweet.
> * Creare un'app per la logica che si connette a Twitter.
> * Aggiungere il rilevamento dei sentiment all'app per la logica. 
> * Connettere l'app per la logica alla funzione.
> * Inviare un messaggio di posta elettronica in base alla risposta dalla funzione.

Passare all'esercitazione successiva per imparare a creare un'API senza server per la funzione.

> [!div class="nextstepaction"] 
> [Creare un'API senza server mediante Funzioni di Azure](functions-create-serverless-api.md)

Per altre informazioni sulle app per la logica, vedere [App per la logica di Azure](../logic-apps/logic-apps-overview.md).

