---
title: Importare e pubblicare la prima API in Gestione API di Azure | Microsoft Docs
description: Informazioni su come importare e pubblicare la prima API con Gestione API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 4dfd1c50137c0a211aa5ba211a96810b8b9831f1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417906"
---
# <a name="import-and-publish-your-first-api"></a>Importare e pubblicare la prima API 

Questa esercitazione illustra come importare un'API back-end "Specifica OpenAPI" che risiede in http://conferenceapi.azurewebsites.net?format=json. Questa API back-end è fornita da Microsoft e ospitata in Azure. 

Dopo l'importazione dell'API back-end in Gestione API, l'API di Gestione API diventa una facciata per l'API back-end. Al momento dell'importazione dell'API back-end, l'API di origine e l'API di Gestione API sono identiche. Gestione API consente di personalizzare la facciata in base alle proprie esigenze senza modificare l'API back-end. Per altre informazioni, vedere [Trasformare e proteggere l'API](transform-api.md). 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Importare la prima API
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

![Nuova API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

Questa sezione illustra come importare e pubblicare un'API back-end Specifica OpenAPI.
 
1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **Specifica OpenAPI** dall'elenco.

    ![Creare un'API](./media/api-management-get-started/create-api.png)

    È possibile impostare i valori dell'API durante la creazione o successivamente andando alla scheda **Impostazioni**. L'asterisco rosso accanto a un campo indica che il campo è obbligatorio.

    Usare i valori della tabella seguente per creare la prima API.

    | Impostazione                   | Valore                                              | DESCRIZIONE                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Specifica OpenAPI** | http://conferenceapi.azurewebsites.net?format=json | Fa riferimento al servizio che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Nome visualizzato**          | *Demo Conference API*                              | Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON. <br/>Questo nome viene visualizzato nel portale per sviluppatori.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Nome**                  | *demo-conference-api*                              | Fornisce un nome univoco per l'API. <br/>Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Descrizione**           | Fornisce una descrizione facoltativa dell'API.        | Se si preme TAB dopo avere immesso l'URL del servizio, Gestione API compilerà questo campo in base al contenuto del file JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **Schema URL**            | *HTTPS*                                            | Determina i protocolli da usare per l'accesso all'API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **Suffisso dell'URL dell'API**        | *conference*                                       | Il suffisso viene aggiunto all'URL di base del servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API di un editore specifico.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Prodotti**              | *Illimitato*                                        | I prodotti sono associazioni di una o più API. È possibile includere diverse API in un prodotto e offrirle agli sviluppatori tramite il portale per sviluppatori. <br/>Per pubblicare l'API, associarla a un prodotto (in questo esempio, *Unlimited*). Per aggiungere la nuova API a un prodotto, digitare il nome del prodotto. È possibile eseguire questa operazione anche in un secondo momento nella pagina **Impostazioni**. Questo passaggio può essere ripetuto più volte per aggiungere l'API a più prodotti.<br/>Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. <br/> Se si è creata l'istanza di Gestione API, si è già un amministratore ed è quindi già stata effettuata la sottoscrizione di ogni prodotto.<br/> Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio: **Starter** e **Unlimited**. |
    | Assegnare un numero di versione a questa API?         |                                                    | Per altre informazioni sul controllo delle versioni, vedere [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Per pubblicare l'API, è necessario associarla a un prodotto. È possibile eseguire questa operazione nella pagina **Impostazioni**.

3. Selezionare **Create**.

> [!TIP]
> In caso di problemi nell'importazione della definizione API, [vedere l'elenco delle restrizioni e dei problemi noti](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testare la nuova API di Gestione API nel portale di Azure

![Mappa di test API](./media/api-management-get-started/01-import-first-api-01.png)

È possibile chiamare le operazioni direttamente dal portale di Azure, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.

1. Selezionare l'API creata nel passaggio precedente nella scheda **API**.
2. Fare clic sulla scheda **Test**.
3. Fare clic su **GetSpeakers**. La pagina visualizza i campi per i parametri di query, che in questo caso non esistono, e le intestazioni. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. La chiave viene compilata automaticamente.
4. Fare clic su **Invia**.

    Il back-end risponde con **200 OK** e alcuni dati.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

È anche possibile chiamare le operazioni dal **portale per sviluppatori** per testare le API.

1. Passare al **portale per sviluppatori**.

    ![Portale per sviluppatori](./media/api-management-get-started/developer-portal.png)

2. Selezionare **API**, fare clic su **Demo Conference API** (API Demo Conference) e quindi su **GetSpeakers**.

    La pagina visualizza i campi per i parametri di query, che in questo caso non esistono, e le intestazioni. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente.

3. Fare clic su **Prova**.
4. Fare clic su **Invia**.

    Dopo la chiamata di un'operazione, nel portale per sviluppatori vengono visualizzate le risposte.  

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Importare la prima API
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare e pubblicare un prodotto](api-management-howto-add-products.md)
