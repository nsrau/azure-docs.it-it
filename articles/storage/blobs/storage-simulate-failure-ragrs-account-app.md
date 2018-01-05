---
title: Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura in Azure | Microsoft Docs
description: Simulare un errore nell'accesso all'archiviazione con ridondanza geografica e accesso in lettura
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 151e875bd72598b0b788d68eee7fb186fca86f46
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura

Questa è la seconda di una serie di esercitazioni. In questa esercitazione, si inserisce una risposta non riuscita con Fiddler per le richieste a un account di archiviazione [con ridondanza geografica e accesso in lettura](../common/storage-redundancy.md#read-access-geo-redundant-storage) per simulare un errore e attivare la lettura dell'applicazione da un endpoint secondario.

![Scenario app](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Eseguire e mettere in pausa l'applicazione
> * Simulare un errore
> * Simulare il ripristino di un endpoint primario

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Scaricare e installare [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Per completare questa esercitazione, è necessario aver completato la precedente esercitazione sull'archiviazione: [Applicare la disponibilità elevata ai dati delle applicazioni con l'archiviazione di Azure][previous-tutorial].

## <a name="launch-fiddler"></a>Avviare Fiddler

Aprire Fiddler, selezionare **Regole** e **Personalizza regole**.

![Personalizzare le regole di Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Si avvia ScriptEditor di Fiddler e mostra il file **SampleRules.js**. Questo file viene usato per personalizzare Fiddler. Incollare il codice di esempio seguente nella funzione `OnBeforeResponse`. Il nuovo codice viene commentato per garantire che la logica creata non sia implementata immediatamente. Al termine della procedura selezionare **File** e **Salva** per salvare le modifiche.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Incollare la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Avviare e mettere in pausa l'applicazione

In Visual Studio premere **F5** o selezionare **Avvia** per avviare il debug dell'applicazione. Quando l'applicazione inizia la leggere dall'endpoint primario, premere un **tasto qualsiasi** nella finestra della console per sospendere l'applicazione.

## <a name="simulate-failure"></a>Simulare un errore

Quando l'applicazione è in pausa è possibile rimuovere il commento alla regola personalizzata salvata in Fiddler in un passaggio precedente. Questo codice di esempio cerca le richieste nell'account di archiviazione RA-GRS e se il percorso contiene il nome dell'immagine, `HelloWorld`, restituisce un codice di risposta `503 - Service Unavailable`.

Passare a Fiddler e selezionare **Regole** -> **Personalizza regole...**.  Rimuovere il commento nelle righe seguenti, sostituire `STORAGEACCOUNTNAME` con il nome dell'account di archiviazione. Selezionare **File** -> **Salva** per salvare le modifiche.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Per riprendere l'applicazione, premere un **tasto qualsiasi**.

Dopo aver avviato di nuovo l'applicazione, le richieste all'endpoint primario iniziano a segnalare errori. L'applicazione tenta di ristabilire la connessione all'endpoint primario per 5 volte. Dopo aver superato la soglia di errore di cinque tentativi, richiede l'immagine dall'endpoint secondario di sola lettura. Dopo che l'applicazione ha recuperato correttamente l'immagine dall'endpoint secondario per 20 volte, l'applicazione tenta di connettersi all'endpoint primario. Se l'endpoint primario non è ancora raggiungibile, l'applicazione riprende a leggere l'endpoint secondario. Questo è il modello [Interruttore](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descritto nell'esercitazione precedente.

![Incollare la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simulare il ripristino di un endpoint primario

Dopo aver impostato la regola personalizzata di Fiddler nel passaggio precedente, le richieste all'endpoint primario hanno esito negativo. Per simulare di nuovo il funzionamento dell'endpoint primario, rimuovere la logica per inserire l'errore `503`.

Per sospendere l'applicazione, premere un **tasto qualsiasi**.

### <a name="remove-the-custom-rule"></a>Rimuovere la regola personalizzata

Passare a Fiddler e selezionare **Regole** e **Personalizza regole...**.  Commentare o rimuovere la logica personalizzata nella funzione `OnBeforeResponse`, lasciando la funzione predefinita. Selezionare **File** e **Salva** per salvare le modifiche.

![Rimuovere la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Al termine premere un **tasto qualsiasi** per riprendere l'applicazione. L'applicazione continua la lettura dall'endpoint primario fino al raggiungimento di 999 letture.

![Riprendere l'applicazione](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie, si è appreso come simulare un errore per testare l'archiviazione con ridondanza geografica e accesso in lettura, ad esempio come:

> [!div class="checklist"]
> * Eseguire e mettere in pausa l'applicazione
> * Simulare un errore
> * Simulare il ripristino di un endpoint primario

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md