---
title: Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura in Azure | Microsoft Docs
description: Simulare un errore nell'accesso all'archiviazione con ridondanza geografica e accesso in lettura
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: 57296d828156184aa36532cd649fbec0c81b5e27
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura

Questa è la seconda di una serie di esercitazioni.  In questa esercitazione è possibile usare [Fiddler](#simulate-a-failure-with-fiddler) o il [routing statico](#simulate-a-failure-with-an-invalid-static-route) per simulare un errore per le richieste all'endpoint primario di un account di archiviazione con [ridondanza geografica e accesso in lettura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) e fare in modo che l'applicazione esegua la lettura dall'endpoint secondario.

![Scenario app](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Per completare questa esercitazione, è necessario aver completato la precedente esercitazione sull'archiviazione: [Applicare la disponibilità elevata ai dati delle applicazioni con l'archiviazione di Azure][previous-tutorial].

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Eseguire e mettere in pausa l'applicazione
> * Simulare un errore con [Fiddler](#simulate-a-failure-with-fiddler) o una [route statica non valida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulare il ripristino di un endpoint primario


## <a name="prerequisites"></a>prerequisiti

Per simulare un errore con Fiddler: 

* Scaricare e [installare Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simulare un errore con Fiddler

Per simulare un errore con Fiddler, si inserisce una risposta non riuscita per le richieste all'endpoint primario dell'account di archiviazione RA-GRS.

Per simulare un errore e il ripristino dell'endpoint primario con Fiddler, seguire questa procedura.

### <a name="launch-fiddler"></a>Avviare Fiddler

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

### <a name="start-and-pause-the-application"></a>Avviare e mettere in pausa l'applicazione

Eseguire l'applicazione nell'ambiente IDE o nell'editor di testo. Quando l'applicazione inizia la leggere dall'endpoint primario, premere un **tasto qualsiasi** nella finestra della console per sospendere l'applicazione.

### <a name="simulate-failure"></a>Simulare un errore

Quando l'applicazione è in pausa, è possibile rimuovere il commento dalla regola personalizzata salvata in Fiddler in un passaggio precedente. L'esempio di codice cerca le richieste all'account di archiviazione RA-GRS e restituisce un codice di risposta `503 - Service Unavailable` se il percorso contiene il nome dell'immagine `HelloWorld`.

Passare a Fiddler e selezionare **Regole** -> **Personalizza regole...**.  Rimuovere il commento nelle righe seguenti, sostituire `STORAGEACCOUNTNAME` con il nome dell'account di archiviazione. Selezionare **File** -> **Salva** per salvare le modifiche. 

> [!NOTE]
> Se si esegue l'applicazione di esempio in Linux, è necessario riavviare Fiddler ogni volta che si modifica il file **CustomRule.js** affinché Fiddler installi la logica personalizzata. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Per riprendere l'esecuzione dell'applicazione, premere **qualsiasi tasto**.

Dopo aver avviato di nuovo l'applicazione, le richieste all'endpoint primario iniziano a segnalare errori. L'applicazione tenta di ristabilire la connessione all'endpoint primario per 5 volte. Dopo aver superato la soglia di errore di cinque tentativi, richiede l'immagine dall'endpoint secondario di sola lettura. Dopo che l'applicazione ha recuperato correttamente l'immagine dall'endpoint secondario per 20 volte, l'applicazione tenta di connettersi all'endpoint primario. Se l'endpoint primario non è ancora raggiungibile, l'applicazione riprende a leggere l'endpoint secondario. Questo è il modello [Interruttore](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descritto nell'esercitazione precedente.

![Incollare la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simulare il ripristino di un endpoint primario

Dopo aver impostato la regola personalizzata di Fiddler nel passaggio precedente, le richieste all'endpoint primario hanno esito negativo. Per simulare di nuovo il funzionamento dell'endpoint primario, rimuovere la logica per inserire l'errore `503`.

Per sospendere l'applicazione, premere un **tasto qualsiasi**.

Passare a Fiddler e selezionare **Regole** e **Personalizza regole...**.  Commentare o rimuovere la logica personalizzata nella funzione `OnBeforeResponse`, lasciando la funzione predefinita. Selezionare **File** e **Salva** per salvare le modifiche.

![Rimuovere la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Al termine premere un **tasto qualsiasi** per riprendere l'applicazione. L'applicazione continua la lettura dall'endpoint primario fino al raggiungimento di 999 letture.

![Riprendere l'applicazione](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulare un errore con una route statica non valida 
È possibile creare una route statica non valida per tutte le richieste all'endpoint primario dell'account di archiviazione con [ridondanza geografica e accesso in lettura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS). In questa esercitazione, l'host locale viene usato come gateway per il routing delle richieste all'account di archiviazione. L'uso dell'host locale come gateway determina il loopback all'interno dell'host di tutte le richieste all'endpoint primario dell'account di archiviazione, causando quindi un errore. Per simulare un errore e il ripristino dell'endpoint primario con una route statica non valida, seguire questa procedura. 

### <a name="start-and-pause-the-application"></a>Avviare e mettere in pausa l'applicazione

Eseguire l'applicazione nell'ambiente IDE o nell'editor di testo. Quando l'applicazione inizia la leggere dall'endpoint primario, premere un **tasto qualsiasi** nella finestra della console per sospendere l'applicazione. 

### <a name="simulate-failure"></a>Simulare un errore

Quando l'applicazione è in pausa, avviare il prompt dei comandi in Windows come amministratore oppure eseguire il terminale come utente ROOT in Linux. Per ottenere informazioni sul dominio dell'endpoint primario dell'account di archiviazione, immettere il comando seguente al prompt dei comandi o nel terminale.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Sostituire `STORAGEACCOUNTNAME` con il nome del proprio account di archiviazione. Copiare l'indirizzo IP dell'account di archiviazione in un editor di testo per usarlo in seguito. Per ottenere l'indirizzo IP dell'host locale, digitare `ipconfig` al prompt dei comandi di Windows oppure `ifconfig` nel terminale Linux. 

Per aggiungere una route statica per un host di destinazione, digitare il comando seguente al prompt dei comandi di Windows o nel terminale Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Sostituire `<destination_ip>` con l'indirizzo IP dell'account di archiviazione e `<gateway_ip>` con l'indirizzo IP dell'host locale. Per riprendere l'esecuzione dell'applicazione, premere **qualsiasi tasto**.

Dopo aver avviato di nuovo l'applicazione, le richieste all'endpoint primario iniziano a segnalare errori. L'applicazione tenta di ristabilire la connessione all'endpoint primario per 5 volte. Dopo aver superato la soglia di errore di cinque tentativi, richiede l'immagine dall'endpoint secondario di sola lettura. Dopo che l'applicazione ha recuperato correttamente l'immagine dall'endpoint secondario per 20 volte, l'applicazione tenta di connettersi all'endpoint primario. Se l'endpoint primario non è ancora raggiungibile, l'applicazione riprende a leggere l'endpoint secondario. Questo è il modello [Interruttore](/azure/architecture/patterns/circuit-breaker.md) descritto nell'esercitazione precedente.

### <a name="simulate-primary-endpoint-restoration"></a>Simulare il ripristino di un endpoint primario

Per simulare il ripristino del funzionamento dell'endpoint primario, eliminare la route statica dell'endpoint primario dalla tabella di routing. In questo modo, tutte le richieste all'endpoint primario potranno essere instradate attraverso il gateway predefinito. 

Per eliminare la route statica di un host di destinazione, ossia dell'account di archiviazione, digitare il comando seguente al prompt dei comandi di Windows o nel terminale Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Premere **qualsiasi tasto** per riprendere l'esecuzione dell'applicazione. L'applicazione continua la lettura dall'endpoint primario fino al raggiungimento di 999 letture.

![Riprendere l'applicazione](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie si è appreso come simulare un errore per testare l'archiviazione con ridondanza geografica e accesso in lettura, ad esempio come:

> [!div class="checklist"]
> * Eseguire e mettere in pausa l'applicazione
> * Simulare un errore con [Fiddler](#simulate-a-failure-with-fiddler) o una [route statica non valida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulare il ripristino di un endpoint primario

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md