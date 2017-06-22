---
title: Aggiungere la logica condizionale e avviare i flussi di lavoro - App per la logica di Azure | Microsoft Docs
description: Controllare l&quot;esecuzione dei flussi di lavoro nelle app per la logica di Azure mediante l&quot;aggiunta di logica condizionale, trigger, azioni e parametri.
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
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: it-it
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>Usare le funzionalità delle app per la logica
Nell' [argomento precedente](../logic-apps/logic-apps-create-a-logic-app.md)è stata creata la prima app per la logica. A questo punto verrà creato un processo più completo con le app per la logica di Azure. Questo argomento introduce i concetti relativi alle app per la logica di Azure elencati di seguito:

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

## <a name="add-conditional-logic-to-your-logic-app"></a>Aggiungere la logica condizionale all'app per la logica

Benché il flusso originale dell'app per la logica funzioni, possiamo migliorare alcune aree.

### <a name="conditional"></a>Condizionale

La prima app per la logica potrebbe comportare la ricezione di troppi messaggi di posta elettronica. I passaggi seguenti consentono di aggiungere una logica condizionale per assicurarsi di ricevere un'e-mail solo quando il tweet proviene da un utente con un numero specifico di follower.

0. Nella finestra di progettazione dell'app per la logica scegliere **Nuovo passaggio** (+) > **Aggiungi un'azione**.
0.    Trovare e aggiungere l'azione **Get User** (Trova utente) per Twitter.
0. Per visualizzare le informazioni sull'utente di Twitter, trovare e aggiungere il campo **Tweeted by** (Pubblicato da) dal trigger.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)

0. Scegliere **Nuovo passaggio** (+) > **Aggiungi una condizione**.
0. Per filtrare il numero di follower relativi a un utente, in **Nome oggetto** scegliere **Aggiungi contenuto dinamico**. 
0.    Nella casella di ricerca, trovare e aggiungere il campo **Followers count** (Conteggio follower).
0. In **Relazione** selezionare **is greater than** (è maggiore di).
0. Nella casella **Valore** immettere il numero di follower per gli utenti.

    ![Condizionale](media/logic-apps-use-logic-app-features/conditional.png)

0. Infine, trascinare la casella **Send email** (Invia email) nella casella **If Yes** (Se sì) . 

Ora si ricevono messaggi di posta elettronica solo quando il numero di follower soddisfa la condizione.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Ripetere l'azione in un elenco con forEach

Il ciclo forEach specifica una matrice per la ripetizione di un'azione. Se non è una matrice, il flusso ha esito negativo. Ad esempio, se action1 genera una matrice di messaggi e si vuole inviare ogni messaggio, è possibile inserire questa istruzione forEach nelle proprietà dell'azione: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Modificare la definizione di codice per un'app per la logica

Oltre che nella finestra di progettazione dell'app per la logica, è possibile modificare direttamente il codice che definisce un'app per la logica.

1. Nella barra dei comandi fare clic su **Visualizzazione Codice**.

    Verrà aperto un editor completo che mostra la definizione appena modificata.

    ![Visualizzazione Codice](media/logic-apps-use-logic-app-features/codeview.png)

    Nell'editor di testo, è possibile copiare e incollare un numero qualsiasi di azioni all'interno della stessa app per la logica o tra app per la logica. 
    È anche possibile aggiungere o rimuovere facilmente intere sezioni dalla definizione, oltre a condividere definizioni con altri.

2. Per salvare le modifiche, scegliere **Salva**.

### <a name="parameters"></a>Parametri

Alcune funzionalità delle app per la logica sono disponibili solo nella visualizzazione codice, ad esempio, i parametri. I parametri semplificano il riutilizzo dei valori all'interno di un'app per la logica. Ad esempio, se si ha un indirizzo e-mail che si vuole usare in diverse azioni, è consigliabile definirlo come parametro.

I parametri costituiscono un buon metodo per estrarre valori che probabilmente verranno modificati molto. Sono particolarmente utili quando è necessario eseguire l'override dei parametri in diversi ambienti. Per informazioni su come eseguire l'override dei parametri in base all'ambiente, vedere la [documentazione sulle API REST](https://docs.microsoft.com/rest/api/logic).

Questo esempio illustra come aggiornare l'app per la logica esistente in modo che sia possibile usare i parametri per il termine della query.

1. Nella visualizzazione codice trovare l'oggetto `parameters : {}` e aggiungere un oggetto argomento:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. Andare all'azione `twitterconnector`, trovare il valore della query e sostituire tale valore con `#@{parameters('topic')}`. 

    Per unire due o più stringhe, è inoltre possibile usare la funzione `concat`. 
    Ad esempio, `@concat('#',parameters('topic'))` funziona esattamente come sopra.

3.    Al termine dell'operazione, scegliere **Salva**. 

    A questo punto, ogni ora tutti i nuovi tweet con più di cinque retweet vengono recapitati in una cartella denominata **tweet** nella propria area Dropbox.

Per informazioni sulle definizioni dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Avviare i flussi di lavoro delle app per la logica

Sono disponibili diverse opzioni per avviare il flusso di lavoro definito nell'app per la logica. Un flusso di lavoro può sempre essere avviato su richiesta nel [portale di Azure].

### <a name="recurrence-triggers"></a>Trigger di ricorrenza

Un trigger di ricorrenza viene eseguito a un intervallo specificato dall'utente. Quando il trigger è caratterizzato dalla logica condizionale, determina se è necessario eseguire il flusso di lavoro. Un trigger indica che il flusso di lavoro deve essere eseguito restituendo un codice di stato `200` . Quando non è necessaria l'esecuzione del flusso di lavoro, il trigger restituisce un codice di stato `202`.

### <a name="callback-using-rest-apis"></a>Callback tramite le API REST

Per avviare un flusso di lavoro, i servizi possono chiamare un endpoint dell'app per la logica. Per avviare questa tipologia di app per la logica su richiesta, fare clic su **Run now** (Esegui ora) nella barra dei comandi. Vedere [Avviare i flussi di lavoro chiamando endpoint dell'app per la logica come trigger](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[portale di Azure]: https://portal.azure.com

