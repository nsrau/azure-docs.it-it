---
title: Importare un'app Funzioni di Azure come API nel portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API di Azure per importare un'app Funzioni di Azure come API.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239053"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Importare un'app Funzioni di Azure come API

Questo articolo illustra come importare un'app Funzioni di Azure come API. Viene inoltre descritto come testare l'API di Gestione API di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Importare un'app per le funzioni come API
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

+ Completare la guida introduttiva [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Verificare che nella sottoscrizione sia disponibile un'app Funzioni di Azure. Per altre informazioni, vedere [Creare un'app per le funzioni](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Creare una definizione OpenAPI](../azure-functions/functions-openapi-definition.md) dell'app Funzioni di Azure.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Nell'elenco **Add a new API** (Aggiungere una nuova API) selezionare **App per le funzioni**.

    ![App per le funzioni](./media/import-function-app-as-api/function-app-api.png)
3. Selezionare **Sfoglia** per visualizzare l'elenco delle app per le funzioni nella sottoscrizione.
4. Selezionare l'app. Gestione API trova lo swagger associato all'app selezionata, lo recupera e lo importa. 
5. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Il suffisso deve essere univoco nell'istanza di Gestione API.
6. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto **Unlimited**. Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile aggiungere l'API a un prodotto durante la creazione dell'API oppure in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. Quando uno sviluppatore esegue la sottoscrizione, ottiene una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è un amministratore. Per impostazione predefinita, gli amministratori dispongono di una sottoscrizione per tutti i prodotti.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    * **Starter**
    * **Illimitato**   
7. Selezionare **Crea**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Inserire le chiavi delle funzioni di Azure in Gestione API di Azure

Se le app Funzioni di Azure importate sono protette tramite chiavi, Gestione API crea automaticamente *valori denominati* per le chiavi. Gestione API non inserisce i segreti nelle voci. Completare i passaggi seguenti per ciascuna voce:  

1. Nell'istanza di Gestione API selezionare la scheda **Valori denominati**.
2. Selezionare una voce e quindi selezionare **Mostra valore** nella barra laterale.

    ![Valori denominati](./media/import-function-app-as-api/apim-named-values.png)

3. Se il testo visualizzato nella casella **Valore** è simile a **codice per \<nome Funzioni di Azure\>**, passare a **App per le funzioni** e quindi a **Funzioni**.
4. Selezionare **Gestisci** e quindi copiare la chiave appropriata in base al metodo di autenticazione dell'app.

    ![App per le funzioni - Copiare le chiavi](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Incollare la chiave nella casella **Valore** e quindi selezionare **Salva**.

    ![App per le funzioni - Incollare i valori delle chiavi](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Testare la nuova API di Gestione API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure. Il portale di Azure offre un sistema pratico per visualizzare e testare le operazioni di un'API.  

1. Selezionare l'API creata nella sezione precedente.
2. Selezionare la scheda **Test**.
3. Selezionare un'operazione.

    La pagina visualizza campi per le intestazioni e campi per i parametri di query. Una delle intestazioni è **Ocp-Apim-Subscription-Key**, per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente. 
4. Selezionare **Send** (Invia).

    Il back-end risponde con **200 OK** e alcuni dati.

## <a name="call-operation"></a>Chiamare un'operazione dal portale per sviluppatori

È anche possibile chiamare le operazioni dal portale per sviluppatori per testare le API. 

1. Selezionare l'API creata in [Importare e pubblicare un'API back-end](#create-api).
2. Selezionare **Portale per sviluppatori**.

    Verrà aperto il sito Portale per sviluppatori.
3. Selezionare l'**API** creata.
4. Selezionare l'operazione che si vuole testare.
5. Selezionare **Prova**.
6. Selezionare **Send** (Invia).
    
    Dopo aver richiamato un'operazione, nel portale per sviluppatori vengono visualizzati lo **stato della risposta**, le **intestazioni della risposta** e l'eventuale **contenuto della risposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)