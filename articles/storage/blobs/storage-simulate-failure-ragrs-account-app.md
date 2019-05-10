---
title: "Esercitazione: Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura in Azure | Microsoft Docs"
description: Simulare un errore nell'accesso all'archiviazione con ridondanza geografica e accesso in lettura
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 1f5c404e410ded2714be761e35060f3c07379bd3
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508086"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Esercitazione: Simulare un errore nell'accesso all'archiviazione con ridondanza e accesso in lettura

Questa è la seconda di una serie di esercitazioni. Si apprenderanno i vantaggi di un'[archiviazione con ridondanza geografica e accesso in lettura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) simulando un errore.

Per simulare un errore, è possibile usare il [routing statico](#simulate-a-failure-with-an-invalid-static-route) o [Fiddler](#simulate-a-failure-with-fiddler). Entrambi i metodi consentono di simulare un errore per le richieste all'endpoint primario di un account di archiviazione con [ridondanza geografica e accesso in lettura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) e fare in modo che l'applicazione esegua invece la lettura dall'endpoint secondario.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Eseguire e mettere in pausa l'applicazione
> * Simulare un errore con una [route statica non valida](#simulate-a-failure-with-an-invalid-static-route) o con [Fiddler](#simulate-a-failure-with-fiddler)
> * Simulare il ripristino di un endpoint primario

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, completare quella precedente: [Applicare la disponibilità elevata ai dati delle applicazioni con l'archiviazione di Azure][previous-tutorial].

Per simulare un errore con il routing statico, usare un prompt dei comandi con privilegi elevati.

Per simulare un errore con Fiddler, scaricare e [installare Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulare un errore con una route statica non valida

È possibile creare una route statica non valida per tutte le richieste all'endpoint primario dell'account di archiviazione con [ridondanza geografica e accesso in lettura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). In questa esercitazione, l'host locale viene usato come gateway per il routing delle richieste all'account di archiviazione. L'uso dell'host locale come gateway determina il loopback all'interno dell'host di tutte le richieste all'endpoint primario dell'account di archiviazione, causando quindi un errore. Per simulare un errore e il ripristino dell'endpoint primario con una route statica non valida, seguire questa procedura.

### <a name="start-and-pause-the-application"></a>Avviare e mettere in pausa l'applicazione

Usare le istruzioni nell'[esercitazione precedente] [ previous-tutorial] per avviare l'esempio e scaricare il file di test, confermando che proviene da un'archiviazione primaria. A seconda della piattaforma di destinazione, è quindi possibile sospendere manualmente l'esempio o attendere un prompt dei comandi.

### <a name="simulate-failure"></a>Simulare un errore

Mentre l'applicazione è in pausa, aprire un prompt dei comandi in Windows come amministratore oppure eseguire il terminale come utente ROOT in Linux.

Per ottenere informazioni sul dominio dell'endpoint primario dell'account di archiviazione, immettere il comando seguente al prompt dei comandi o nel terminale, sostituendo `STORAGEACCOUNTNAME` con il nome de proprio account di archiviazione.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Copiare l'indirizzo IP dell'account di archiviazione in un editor di testo per usarlo in seguito.

Per ottenere l'indirizzo IP dell'host locale, digitare `ipconfig` al prompt dei comandi di Windows oppure `ifconfig` nel terminale Linux.

Per aggiungere una route statica per un host di destinazione, digitare il comando seguente al prompt dei comandi di Windows o nel terminale Linux, sostituendo `<destination_ip>` con l'indirizzo IP dell'account di archiviazione e `<gateway_ip>` con l'indirizzo IP dell'host locale.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a> Windows

```
route add <destination_ip> <gateway_ip>
```

Nella finestra contenente l'esempio in esecuzione, riprendere l'applicazione o premere il tasto appropriato per scaricare il file di esempio e confermare che proviene da un'archiviazione secondaria. È quindi possibile sospendere nuovamente l'esempio o attendere un prompt dei comandi.

### <a name="simulate-primary-endpoint-restoration"></a>Simulare il ripristino di un endpoint primario

Per simulare il ripristino del funzionamento dell'endpoint primario, eliminare la route statica non valida dalla tabella di routing. In questo modo, tutte le richieste all'endpoint primario potranno essere instradate attraverso il gateway predefinito. Digitare il comando seguente al prompt dei comandi di Windows o nel terminale Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a> Windows

```
route delete <destination_ip>
```

È quindi possibile riprendere l'applicazione o premere il tasto appropriato per scaricare nuovamente il file di esempio, questa volta confermando che proviene ancora una volta da un'archiviazione primaria.

## <a name="simulate-a-failure-with-fiddler"></a>Simulare un errore con Fiddler

Per simulare un errore con Fiddler, si inserisce una risposta non riuscita per le richieste all'endpoint primario dell'account di archiviazione RA-GRS.

Le sezioni seguenti illustrano come simulare un errore e il ripristino dell'endpoint primario con Fiddler.

### <a name="launch-fiddler"></a>Avviare Fiddler

Aprire Fiddler, selezionare **Regole** e **Personalizza regole**.

![Personalizzare le regole di Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Si avvia ScriptEditor di Fiddler, che visualizza il file **SampleRules.js**. Questo file viene usato per personalizzare Fiddler.

Incollare il codice di esempio seguente nella funzione `OnBeforeResponse`, sostituendo `STORAGEACCOUNTNAME` con il nome del proprio account di archiviazione. A seconda dell'esempio, potrebbe inoltre essere necessario sostituire `HelloWorld` con il nome del file di test (o un prefisso, ad esempio `sampleFile`) scaricato. Il nuovo codice è impostato come commentato per assicurarsi che non venga eseguito immediatamente.

Al termine, selezionare **File** e **Salva** per salvare le modifiche. Lasciare aperta la finestra ScriptEditor per l'uso nei passaggi seguenti.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Incollare la regola personalizzata](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Avviare e mettere in pausa l'applicazione

Usare le istruzioni nell'[esercitazione precedente] [ previous-tutorial] per avviare l'esempio e scaricare il file di test, confermando che proviene da un'archiviazione primaria. A seconda della piattaforma di destinazione, è quindi possibile sospendere manualmente l'esempio o attendere un prompt dei comandi.

### <a name="simulate-failure"></a>Simulare un errore

Mentre l'applicazione è in pausa, tornare a Fiddler e rimuovere il commento dalla regola personalizzata salvata nella funzione `OnBeforeResponse`. Assicurarsi di selezionare **File** e **Salva** per salvare le modifiche in modo da applicare la regola. Questo codice cerca le richieste all'account di archiviazione RA-GRS e, se il percorso contiene il nome del file di esempio, restituisce il codice di risposta `503 - Service Unavailable`.

Nella finestra contenente l'esempio in esecuzione, riprendere l'applicazione o premere il tasto appropriato per scaricare il file di esempio e confermare che proviene da un'archiviazione secondaria. È quindi possibile sospendere nuovamente l'esempio o attendere un prompt dei comandi.

### <a name="simulate-primary-endpoint-restoration"></a>Simulare il ripristino di un endpoint primario

In Fiddler rimuovere o impostare nuovamente come commento la regola personalizzata. Selezionare **File** e **Salva** per assicurarsi che la regola non sia più in vigore.

Nella finestra contenente l'esempio in esecuzione, riprendere l'applicazione o premere il tasto appropriato per scaricare il file di esempio e confermare che proviene ancora una volta da un'archiviazione primaria. È quindi possibile chiudere l'esempio.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie si è appreso come simulare un errore per testare l'archiviazione con ridondanza geografica e accesso in lettura.

Per altre informazioni sul funzionamento dell'archiviazione con ridondanza geografica e accesso in lettura e sui rischi associati, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Progettazione di applicazioni a disponibilità elevata con RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
