---
title: Importare un'app per le API come API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per importare un'app per le API come API.
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
ms.openlocfilehash: d0e1aa6763d96b5a84bbc5fba7dcae690c051eb3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-api-app-as-an-api"></a>Importare un'app per le API come API

Questo articolo illustra come importare un'app per le API come API e come testare l'API di Gestione API.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Importare un'app per le API come API
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ Verificare che sia disponibile un'app per le API nella sottoscrizione. Per altre informazioni, vedere [Documentazione del servizio app][https://docs.microsoft.com/azure/app-service/]

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **App per le API** dall'elenco **Add a new API** (Aggiungere una nuova API).

    !(App per le API)[./media/import-api-app-as-api/api-app.png]
3. Fare clic su **Sfoglia** per visualizzare l'elenco di app per le API nella sottoscrizione.
4. Selezionare l'app. Gestione API trova lo swagger associato all'app selezionata, lo recupera e lo importa. 

    Se Gestione API non trova lo swagger, espone l'API come API "pass-through". 
5. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.
6. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto "*Unlimited*".  Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    * **Starter**
    * **Illimitato**   
7. Selezionare **Crea**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testare la nuova API di Gestione API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.  

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic sulla scheda **Test**.
3. Selezionare un'operazione.

    La pagina visualizza campi per le intestazioni e campi per i parametri di query. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente. 
1. Fare clic su **Invia**.

    Il back-end risponde con **200 OK** e alcuni dati.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

Le operazioni possono essere chiamate anche dal **portale per sviluppatori** per testare le API. 

1. Selezionare l'API creata nel passaggio "Importare e pubblicare un'API back-end".
2. Fare clic su **Portale per sviluppatori**.

    Viene aperto il sito "Portale per sviluppatori".
3. Selezionare l'**API** creata.
4. Fare clic sull'operazione che si vuole testare.
5. Fare clic su **Prova**.
6. Fare clic su **Invia**.
    
    Dopo aver richiamato un'operazione, nel portale per sviluppatori vengono visualizzati lo **stato della risposta**, le **intestazioni della risposta** e l'eventuale **contenuto della risposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)