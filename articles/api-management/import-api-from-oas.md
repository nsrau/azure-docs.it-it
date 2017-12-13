---
title: Importare una specifica OpenAPI usando il portale di Azure | Microsoft Docs
description: Informazioni su come importare una specifica OpenAPI con Gestione API.
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
ms.openlocfilehash: f0c77c6e959ca99698b3ea704756a6abf36147f3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-openapi-specification"></a>Importare una specifica OpenAPI

Questo articolo illustra come importare un'API back-end "Specifica OpenAPI" che si trova all'indirizzo http://conferenceapi.azurewebsites.net?format=json. Questa API back-end è fornita da Microsoft e ospitata in Azure. L'articolo illustra anche come testare l'API di Gestione API.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Importare un'API back-end "Specifica OpenAPI"
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **Specifica OpenAPI** dall'elenco **Add a new API** (Aggiungere una nuova API).
    ![Specifica OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Immettere le impostazioni appropriate. Durante la creazione, è possibile impostare tutti i valori dell'API. In alternativa è possibile impostarne alcuni successivamente andando alla scheda **Impostazioni**. <br/> Se si preme **TAB** alcuni o tutti i campi verranno compilati con le informazioni provenienti dal servizio back-end specificato.

    ![Creare un'API](./media/api-management-get-started/create-api.png)

    |Impostazione|Valore|Descrizione|
    |---|---|---|
    |**Specifica OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Fa riferimento al servizio che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.|
    |**Nome visualizzato**|*Demo Conference API*|Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON. <br/>Questo nome viene visualizzato nel portale per sviluppatori.|
    |**Nome**|*demo-conference-api*|Fornisce un nome univoco per l'API. <br/>Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON.|
    |**Descrizione**|Fornisce una descrizione facoltativa dell'API.|Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON.|
    |**Suffisso dell'URL dell'API**|*conference*|Il suffisso viene aggiunto all'URL di base del servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API di un editore specifico.|
    |**Schema URL**|*HTTPS*|Determina i protocolli da usare per l'accesso all'API. |
    |**Prodotti**|*Illimitato*| Pubblicare l'API associandola a un prodotto. Per aggiungere facoltativamente questa nuova API a un prodotto, digitare il nome del prodotto. Questo passaggio può essere ripetuto più volte per aggiungere l'API a più prodotti.<br/>I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.<br/> Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio: **Starter** e **Unlimited**. |

4. Selezionare **Crea**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testare la nuova API di Gestione API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.  

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic sulla scheda **Test**.

    ![Testare l'API](./media/api-management-get-started/test-api.png)
1. Fare clic su **GetSpeakers**.

    La pagina visualizza i campi per i parametri di query, ma in questo caso non ne esistono. La pagina visualizza anche i campi per le intestazioni. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente. 
4. Fare clic su **Invia**.

    Il back-end risponde con **200 OK** e alcuni dati.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

Le operazioni possono essere chiamate anche dal **portale per sviluppatori** per testare le API. 

1. Selezionare l'API creata nel passaggio "Importare e pubblicare un'API back-end".
2. Fare clic su **Portale per sviluppatori**.

    ![Eseguire il test nel portale per sviluppatori](./media/api-management-get-started/developer-portal.png)

    Viene aperto il sito "Portale per sviluppatori".
3. Selezionare **API**.
4. Selezionare **Demo Conference API**.
5. Fare clic su **GetSpeakers**.
    
    La pagina visualizza i campi per i parametri di query, ma in questo caso non ne esistono. La pagina visualizza anche i campi per le intestazioni. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente.
6. Fare clic su **Prova**.
7. Fare clic su **Invia**.
    
    Dopo aver richiamato un'operazione, nel portale per sviluppatori vengono visualizzati lo **stato della risposta**, le **intestazioni della risposta** e l'eventuale **contenuto della risposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
