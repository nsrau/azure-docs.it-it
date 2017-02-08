---
title: "Usare le funzionalità delle app per la logica | Documentazione Microsoft"
description: "Informazioni su come usare le funzionalità avanzate delle app per la logica."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 557602297022b07a05f7ac202473b67332bec5d7


---
# <a name="use-logic-apps-features"></a>Usare le funzionalità delle app per la logica
Nell' [argomento precedente](../logic-apps/logic-apps-create-a-logic-app.md)è stata creata la prima app per la logica. In questa fase verrà mostrato come creare un processo più completo usando le app per la logica del servizio app. Questo argomento introduce i nuovi concetti relativi alle app per la logica elencati di seguito:

* Logica condizionale, che esegue un'azione solo quando viene soddisfatta una determinata condizione.
* Visualizzazione del codice per modificare un'app per la logica esistente.
* Opzioni per avviare un flusso di lavoro.

Prima di completare questo argomento, completare i passaggi elencati nell'articolo [Creare una nuova app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Nel [portale di Azure], passare all'app per la logica e fare clic su **Trigger e azioni** nel riepilogo per modificare la definizione di app per la logica.

## <a name="reference-material"></a>Materiale di riferimento
I seguenti documenti possono essere utili:

* [API REST di gestione e di runtime](https://msdn.microsoft.com/library/azure/mt643787.aspx) : contiene anche informazioni su come richiamare direttamente le app per la logica
* [Riferimento al linguaggio](https://msdn.microsoft.com/library/azure/mt643789.aspx) : fornisce un elenco completo di tutte le funzioni/espressioni supportate
* [Tipi di trigger e azioni](https://msdn.microsoft.com/library/azure/mt643939.aspx) : descrive i diversi tipi di azioni e gli input che accettano
* [Panoramica del servizio app](../app-service/app-service-value-prop-what-is.md) : fornisce informazioni sui componenti da scegliere per la creazione di una soluzione

## <a name="adding-conditional-logic"></a>Aggiunta di logica condizionale
Benché il flusso originale funzioni, vi sono alcune aree che è possibile migliorare.

### <a name="conditional"></a>Condizionale
Questa app per la logica può comportare la ricezione di grandi quantità di messaggi di posta elettronica. Le procedure seguenti consentono di aggiungere la logica per assicurarsi di ricevere un messaggio solo quando il tweet proviene da un utente con un determinato numero di follower.

1. Fare clic sul segno più e trovare l'azione *Get User* per Twitter.
2. Passare al campo **Tweeted by** del trigger per visualizzare le informazioni sull'utente di Twitter.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)
3. Fare di nuovo clic sul segno più e selezionare **Aggiungi condizione**
4. Nella prima casella fare clic su **...** sotto **Ottieni utente** per trovare il campo **Numero di follower**.
5. Nella casella a discesa selezionare **Greater than**
6. Nella seconda casella digitare il numero di follower per gli utenti.

    ![Condizionale](media/logic-apps-use-logic-app-features/conditional.png)
7. Infine, trascinare il messaggio di posta elettronica nella casella **If Yes** . In questo modo si riceveranno messaggi di posta elettronica solo quando viene raggiunto il numero di follower.

## <a name="repeating-over-a-list-with-foreach"></a>Ripetizione in un elenco con forEach
Il ciclo forEach specifica una matrice per la ripetizione di un'azione. Se non è una matrice, il flusso ha esito negativo. Ad esempio, se action1 genera una matrice di messaggi e si vuole inviare ogni messaggio, è possibile inserire questa istruzione forEach nelle proprietà dell'azione: forEach : "@action('action1').outputs.messages"

## <a name="using-the-code-view-to-edit-a-logic-app"></a>Uso della visualizzazione codice per modificare un'app per la logica
Oltre alla finestra di progettazione, è possibile modificare direttamente il codice che definisce un'app per la logica, come segue.

1. Fare clic sul pulsante **Visualizzazione Codice** nella barra dei comandi.

    Verrà aperto un editor completo che mostra la definizione appena modificata.

    ![Visualizzazione Codice](media/logic-apps-use-logic-app-features/codeview.png)

    Usando l'editor di testo, è possibile copiare e incollare un numero qualsiasi di azioni all'interno della stessa app per la logica o tra app per la logica. È anche possibile aggiungere o rimuovere facilmente intere sezioni dalla definizione, oltre a condividere definizioni con altri.
2. Dopo aver apportato le modifiche nella visualizzazione codice, fare clic su **Salva**.

### <a name="parameters"></a>Parametri
Vi sono alcune funzionalità delle app per la logica che possono essere usate solo in visualizzazione codice, come ad esempio i parametri. I parametri semplificano il riutilizzo dei valori all'interno di un'app per la logica. Ad esempio, se si ha un indirizzo e-mail che si vuole usare in diverse azioni, è consigliabile definirlo come parametro.

Il codice seguente consente di aggiornare l'app per la logica esistente in modo da usare parametri per il termine della query.

1. Nella visualizzazione codice individuare l'oggetto `parameters : {}` e inserire il seguente argomento:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
2. Scorrere fino all'azione `twitterconnector`, trovare il valore della query e sostituirlo con `#@{parameters('topic')}`.
    È anche possibile usare la funzione **concat** per unire due o più stringhe, ad esempio `@concat('#',parameters('topic'))` corrisponde a quanto scritto sopra.

I parametri costituiscono un buon metodo per estrarre valori che probabilmente verranno modificati molto. Sono particolarmente utili quando è necessario eseguire l'override dei parametri in diversi ambienti. Per altre informazioni su come eseguire l'override dei parametri in base all'ambiente, vedere la [documentazione sulle API REST](https://msdn.microsoft.com/library/mt643787.aspx).

A questo punto, quando si fa clic su **Salva**, ogni ora tutti i nuovi tweet con più di 5 retweet vengono recapitati in una cartella denominata **tweet** nella propria area Dropbox.

Per informazioni sulle definizioni dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Avvio di un flusso di lavoro dell'app per la logica
Vi sono diverse opzioni per avviare il flusso di lavoro definito nell'app per la logica. Un flusso di lavoro può sempre essere avviato su richiesta nel [portale di Azure].

### <a name="recurrence-triggers"></a>Trigger di ricorrenza
Un trigger di ricorrenza viene eseguito a un intervallo specificato dall'utente. Quando il trigger è caratterizzato dalla logica condizionale, determina se è necessario o no eseguire il flusso di lavoro. Un trigger indica che deve essere eseguito restituendo un codice di stato `200` . Quando non è necessario eseguirlo, restituisce il codice di stato `202` .

### <a name="callback-using-rest-apis"></a>Callback tramite le API REST
I servizi possono chiamare un endpoint dell'app per la logica per avviare un flusso di lavoro. Per altre informazioni, vedere [App per la logica come endpoint che è possibile chiamare](../logic-apps/logic-apps-http-endpoint.md) . Per avviare questa tipologia di app per la logica su richiesta, fare clic sul pulsante **Esegui adesso** sulla barra dei comandi. 

<!-- Shared links -->
[portale di Azure]: https://portal.azure.com



<!--HONumber=Jan17_HO3-->


