---
title: Come usare SendGrid in Funzioni di Azure | Documentazione Microsoft
description: Questo articolo mostra come usare SendGrid in Funzioni di Azure
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Come usare SendGrid nelle Funzioni di Azure

## <a name="sendgrid-overview"></a>Panoramica di SendGrid

Funzioni di Azure supporta le associazioni output di SendGrid per consentire alle funzioni di inviare messaggi posta elettronica con poche righe di codice e un account di SendGrid.

Per usare l'API SendGrid in una Funzione di Azure, è necessario un [account di SendGrid](http://SendGrid.com). Inoltre, è necessario disporre di una chiave dell'API di SendGrid. Accedere al proprio account di SendGrid e fare clic su **Impostazioni** quindi su **Chiave API** per generare una chiave API. Fare in modo che questa chiave sia disponibile per l'uso nel passaggio successivo.

È ora possibile creare un'app per le funzioni di Azure.

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure 

Le app per le funzioni di Azure sono contenitori per uno o più funzioni di Azure. Le funzioni di Azure sono solo delle funzioni. Ogni funzione di Azure è associata a un trigger, ovvero a un evento che attiva l'esecuzione della funzione.
Ogni funzione può contenere diverse associazioni di input oppure output. Le associazioni sono servizi che è possibile usare in una funzione. SendGrid è un'associazione di output che è possibile usare per inviare email. 

1. Accedere al Portale di Azure e [creare un'app per le funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) oppure aprire un'app per le funzioni esistente. 
2. Creare una funzione di Azure. Per farlo in modo semplice, scegliere un trigger manuale e C#. 

 ![Creare una funzione di Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Configurare SendGrid per l'uso in un'app per le funzioni di Azure

È necessario archiviare la chiave API di SendGrid come impostazione dell'app per usarla in una funzione. Il campo ApiKey non è la chiave API di SendGrid effettiva, ma un'impostazione dell'app definita dall'utente che rappresenta la chiave API effettiva. Per sicurezza si esegue questo tipo di archiviazione della chiave, poiché è separata da qualsiasi codice o file che potrebbe essere archiviato nel controllo del codice sorgente.

- Creare una chiave **AppSettings** in **Impostazioni applicazione** dell'app per le funzioni.

 ![Creare una funzione di Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Configurare le associazioni di output di SendGrid

SendGrid è disponibile come associazione di output della funzione di Azure. Per creare un'associazione di output di SendGrid:

1. Andare nella scheda **Integrazione** della funzione nel Portale di Azure.
2. Per creare un'associazione di output di SendGrid fare clic su **Nuovo output**.
3. Compilare le proprietà **Chiave API** e **Nome del parametro del messaggio**. Se lo si desidera, a questo punto è possibile immettere le altre proprietà oppure codificarle. Queste impostazioni possono essere usate come impostazioni predefinite.

 ![Configurare le associazioni di output di SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

L'aggiunta di un'associazione a una funzione crea un file denominato **function.json** nella cartella della funzione. Questo file contiene tutte le informazioni visualizzate nella scheda **Integrazione** della funzione di Azure , ma in formato JSON. L'impostazione dei campi **ApiKey**, **messaggio**, e **from** (da) crea le seguenti voci nel file **function.json**: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Se lo si preferisce, è possibile modificare direttamente questo file.

Ora che l'app per le funzioni e la funzione sono state create e configurate, è possibile scrivere il codice per inviare un'email.

## <a name="write-code-that-creates-and-sends-email"></a>Scrivere il codice che crea e invia email

L'API SendGrid contiene tutti i comandi necessari per creare e inviare un'email.  

- Sostituire il codice nella funzione con il codice seguente:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Si noti che la prima riga contiene la direttiva ```#r``` che fa riferimento all'assembly di SendGrid. Successivamente, è possibile usare un'istruzione ```using``` per accedere più facilmente agli oggetti nello spazio dei nomi. Nel codice, creare istanze degli oggetti ```Mail```, ```Personalization``` e ```Content``` dell'API di SendGrid che compongono l'email. Quando l'utente restituisce il messaggio, SendGrid lo recapita. 

La firma della funzione contiene inoltre un altro parametro out di tipo ```Mail``` denominato ```message```. Le associazioni di input e output si esprimono come parametri di funzione nel codice. 

2. Testare il codice facendo clic su **Test** e inserire un messaggio nel campo **Corpo della richiesta**, quindi fare clic sul pulsante **Esegui**.

 ![Testare il codice](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Controllare i messaggi di posta elettronica per verificare che l'email sia stata inviata da SendGrid. Dovrebbe essere recapitata all'indirizzo inserito nel codice del passaggio 1 e dovrebbe contenere il messaggio del **Corpo della richiesta**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare il servizio SendGrid per creare e inviare email. Per altre informazioni sull'uso di Funzioni di Azure nelle app, vedere gli argomenti seguenti: 

- L'articolo [Procedure consigliate per le funzioni di Azure](functions-best-practices.md) elenca alcune procedure consigliate da usare durante la creazione di Funzioni di Azure.

- La [guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md) contiene informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.

- [Test di Funzioni di Azure](functions-test-a-function.md) descrive diversi strumenti e tecniche per il test delle funzioni.