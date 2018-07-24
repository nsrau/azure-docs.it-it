---
title: Importare un'app per le funzioni come API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per importare un'app per le funzioni come API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090929"
---
# <a name="import-a-function-app-as-an-api"></a>Importare un'app per le funzioni come API

Questo articolo illustra come importare un'app per le funzioni come API e come testare l'API di Gestione API.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Importare un'app per le funzioni come API
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ Verificare che nella sottoscrizione sia disponibile un'app per le funzioni di Azure. Per altre informazioni, vedere [Creare un'app per le funzioni](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)
+ [Creare una definizione OpenAPI](../azure-functions/functions-openapi-definition.md) dell'app per le funzioni di Azure

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **App per le funzioni** nell'elenco **Add a new API** (Aggiungere una nuova API).

    ![App per le funzioni](./media/import-function-app-as-api/function-app-api.png)
3. Fare clic su **Sfoglia** per visualizzare l'elenco delle app per le funzioni nella sottoscrizione.
4. Selezionare l'app. Gestione API trova lo swagger associato all'app selezionata, lo recupera e lo importa. 
5. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.
6. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto "*Unlimited*".  Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    * **Starter**
    * **Illimitato**   
7. Selezionare **Crea**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Inserire le chiavi delle funzioni di Azure in Gestione API di Azure

Se le funzioni di Azure importate sono protette tramite chiavi, Gestione API di Azure crea automaticamente **valori denominati** corrispondenti, ma non inserisce i segreti nelle voci. Per ogni voce è necessario seguire questa procedura.  

1. Passare alla scheda **Valori denominati** nell'istanza di Gestione API.
2. Fare clic su una voce e quindi su **Mostra valore** nella barra laterale.

    ![Valori denominati](./media/import-function-app-as-api/apim-named-values.png)

3. Se il contenuto è simile a *codice per {nome funzione di Azure}*, passare all'app per le funzioni di Azure importata e quindi alla funzione di Azure.
4. Passare alla sezione **Gestione** della funzione di Azure desiderata e copiare la chiave pertinente, in base al metodo di autenticazione della funzione di Azure.

    ![App per le funzioni](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Incollare la chiave nella casella di testo visualizzata da **Valori denominati** e fare clic su **Salva**.

    ![App per le funzioni](./media/import-function-app-as-api/apim-named-values-2.png)

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