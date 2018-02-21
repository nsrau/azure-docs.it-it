---
title: Aggiungere manualmente un'API usando il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per aggiungere manualmente un'API.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 9426839f88daece1bb688a2079b7854ccaebdc57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="add-an-api-manually"></a>Aggiungere manualmente un'API 

La procedura illustrata in questo articolo illustra come usare il portale di Azure per aggiungere manualmente un'API all'istanza di Gestione API. Uno scenario comune quando si vuole creare un'API vuota e definirla manualmente è la simulazione dell'API. Per informazioni dettagliate sul comportamento fittizio di un'API, vedere [Simulare le risposte di un'API](mock-api-responses.md).

Per importare un'API esistente, vedere la sezione [Argomenti correlati](#related-topics).

In questo articolo viene creata un'API vuota e viene specificato [httpbin.org](http://httpbin.org) (un servizio di test pubblico) come API back-end.

## <a name="prerequisites"></a>prerequisiti

Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Creare un'API

1. Selezionare **API** in **GESTIONE API**.
2. Fare clic su **+ Aggiungi API** nel menu a sinistra.
3. Selezionare **API vuota** dall'elenco.

    ![API vuota](media/add-api-manually/blank-api.png)
4. Immettere le impostazioni per l'API.

    ![Impostazioni](media/add-api-manually/settings.png)

    |**Nome**|**Valore**|**Descrizione**|
    |---|---|---|
    |**Nome visualizzato**|"*Blank API*" |Questo nome viene visualizzato nel portale per sviluppatori.|
    |**URL del servizio Web** (facoltativo)| "*http://httpbin.org*"| Per simulare un'API, è possibile non immettere nulla. <br/>In questo caso viene immesso [http://httpbin.org](http://httpbin.org). Si tratta di un servizio pubblico di test. <br/>Per importare un'API di cui viene eseguito il mapping automatico a un back-end, vedere uno degli argomenti nella sezione [Argomenti correlati](#related-topics).|
    |**Schema URL**|"*HTTPS*"|In questo caso, anche se il back end ha accesso HTTP non sicuro, viene specificato un accesso di Gestione API HTTPS sicuro al back-end. <br/>Questo tipo di scenario (da HTTPS a HTTP) viene chiamato terminazione HTTPS. È possibile eseguirlo se l'API esiste in una rete virtuale (dove è certo che l'accesso è sicuro anche se non viene usato HTTPS). <br/>È possibile usare la "terminazione HTTPS" per il salvataggio in alcuni cicli di CPU.|
    |**Suffisso dell'URL dell'API**|"*hbin*"| Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.|
    |**Prodotti**|"*Unlimited*" |Pubblicare l'API associandola a un prodotto. Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.<br/><br/>I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. <br/>Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.<br/><br/> Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio: **Starter** e **Unlimited**.| 
5. Selezionare **Create**.

A questo punto, non si hanno operazioni in Gestione API di cui viene eseguito il mapping alle operazioni nell'API back-end. Se si chiama un'operazione esposta tramite il back-end, ma non tramite Gestione API, viene visualizzato un errore **404**. 

>[!NOTE] 
> Per impostazione predefinita, quando si aggiunge un'API, anche se è connessa a un servizio back-end, Gestione API non esporrà operazioni finché non le si inserirà nell'elenco elementi consentiti. Per inserire un'operazione del servizio back-end nell'elenco elementi consentiti, creare un'operazione di Gestione API che esegue il mapping all'operazione back-end.
>

## <a name="add-and-test-an-operation"></a>Aggiungere e testare un'operazione

Questa sezione illustra come aggiungere un'operazione "/get" per eseguirne il mapping all'operazione back-end "http://httpbin.org/get".

### <a name="add-the-operation"></a>Aggiungere l'operazione

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic su **+ Aggiungi operazione**.
3. In **URL** selezionare **GET** e immettere "*/get*" nella risorsa.
4. Immettere "*FetchData*" in **Nome visualizzato**.
5. Selezionare **Salva**.

### <a name="test-the-operation"></a>Testare l'operazione

Testare l'operazione nel portale di Azure. In alternativa è possibile testarla nel **portale per sviluppatori**.

1. Selezionare la scheda **Test**.
2. Selezionare **FetchData**.
3. Fare clic su **Invia**.

Viene visualizzata la risposta generata dall'operazione "http://httpbin.org/get". Per trasformare le operazioni, vedere [Trasformare e proteggere l'API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Aggiungere e testare un'operazione con parametri

Questa sezione illustra come aggiungere un'operazione che accetta un parametro. In questo caso viene eseguito il mapping dell'operazione a "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Aggiungere l'operazione

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic su **+ Aggiungi operazione**.
3. In **URL** selezionare **GET** e immettere "*/status/{codice}*" nella risorsa. Facoltativamente, è possibile fornire alcune informazioni associate a questo parametro. Immettere ad esempio "*Number*" in **TIPO**, "*200*" (impostazione predefinita) in **VALORI**.
4. Immettere "GetStatus" in **Nome visualizzato**.
5. Selezionare **Salva**.

### <a name="test-the-operation"></a>Testare l'operazione 

Testare l'operazione nel portale di Azure.  In alternativa è possibile testarla nel **portale per sviluppatori**.

1. Selezionare la scheda **Test**.
2. Selezionare **GetStatus**. Per impostazione predefinita, il valore del codice è impostato su "*200*". È possibile modificarlo per testare altri valori. Ad esempio digitare *418*.
3. Fare clic su **Invia**.

    Viene visualizzata la risposta generata dall'operazione "http://httpbin.org/status/200". Per trasformare le operazioni, vedere [Trasformare e proteggere l'API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
