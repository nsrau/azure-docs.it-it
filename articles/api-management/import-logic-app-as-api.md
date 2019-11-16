---
title: Importare un'app per la logica come API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per importare un'app per la logica come API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: 4077187fe04e3be914a6f7fba84c03df1b79d06a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108402"
---
# <a name="import-a-logic-app-as-an-api"></a>Importare un'app per la logica come API

Questo articolo illustra come importare un'app per la logica come API e testare l'API importata.

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> -   Importare un'app per la logica come API
> -   Testare l'API nel portale di Azure
> -   Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

-   Completare l'argomento di avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
-   Verificare che nella sottoscrizione sia disponibile un'app per la logica che espone un endpoint HTTP. Per altre informazioni, vedere [Attivare flussi di lavoro con endpoint HTTP](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **App per la logica** nell'elenco **Add a new API** (Aggiungere una nuova API).

    ![App per la logica](./media/import-logic-app-as-api/logic-app-api.png)

3. Fare clic su **Sfoglia** per visualizzare l'elenco di App per la logica con trigger HTTP nella sottoscrizione. Si noti che le App per la logica senza trigger HTTP non verranno visualizzate nell'elenco.
4. Selezionare l'app. Gestione API trova lo swagger associato all'app selezionata, lo recupera e lo importa.
5. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.
6. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto "_Unlimited_". Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    - **Starter**
    - **Illimitato**

7. Selezionare **Create** (Crea).

## <a name="test-the-api-in-the-azure-portal"></a>Testare l'API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic sulla scheda **Test**.
3. Selezionare un'operazione.

    La pagina visualizza campi per le intestazioni e campi per i parametri di query. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente.

4. Fare clic su **Invia**.

    Il back-end risponde con **200 OK** e alcuni dati.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Ogni app per la logica ha un'operazione **manual-invoke**. Se nell'API si vogliono includere più app per la logica, per evitare conflitti è necessario rinominare la funzione.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
