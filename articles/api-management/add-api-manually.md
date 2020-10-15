---
title: Aggiungere manualmente un'API usando il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per aggiungere manualmente un'API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631291"
---
# <a name="add-an-api-manually"></a>Aggiungere manualmente un'API

La procedura illustrata in questo articolo illustra come usare il portale di Azure per aggiungere manualmente un'API all'istanza di Gestione API. Uno scenario comune quando si vuole creare un'API vuota e definirla manualmente è la simulazione dell'API. Per informazioni dettagliate sul comportamento fittizio di un'API, vedere [Simulare le risposte di un'API](mock-api-responses.md).

Per importare un'API esistente, vedere la sezione [Argomenti correlati](#related-topics).

In questo articolo viene creata un'API vuota e viene specificato [httpbin.org](https://httpbin.org) (un servizio di test pubblico) come API back-end.

## <a name="prerequisites"></a>Prerequisiti

Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Creare un'API

1. Passare al servizio Gestione API nel portale di Azure e selezionare **API** dal menu.
2. Fare clic su **+ Aggiungi API** nel menu a sinistra.
3. Selezionare **API vuota** dall'elenco.  
    ![Blank API](media/add-api-manually/blank-api.png)  
4. Immettere le impostazioni per l'API. Le impostazioni sono illustrate nell'esercitazione [Importare e pubblicare la prima API](import-and-publish.md#import-and-publish-a-backend-api).
5. Selezionare **Create** (Crea).

A questo punto, non si hanno operazioni in Gestione API di cui viene eseguito il mapping alle operazioni nell'API back-end. Se si chiama un'operazione esposta tramite il back-end, ma non tramite Gestione API, viene visualizzato un errore **404**.

>[!NOTE] 
> Per impostazione predefinita, quando si aggiunge un'API, anche se è connessa a un servizio back-end, Gestione API non esporrà operazioni finché non vengono consentite. Per consentire un'operazione del servizio back-end, creare un'operazione di Gestione API mappata all'operazione back-end.

## <a name="add-and-test-an-operation"></a>Aggiungere e testare un'operazione

Questa sezione illustra come aggiungere un'operazione "/get" per eseguirne il mapping all'operazione back-end "http://httpbin.org/get".

### <a name="add-an-operation"></a>Aggiungere un'operazione

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic su **+ Aggiungi operazione**.
3. In **URL** selezionare **GET** e immettere " */get*" nella risorsa.
4. Immettere "*FetchData*" in **Nome visualizzato**.
5. Selezionare **Salva**.

### <a name="test-an-operation"></a>Testare un'operazione

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
3. In **URL** selezionare **GET** e immettere " */status/{codice}* " nella risorsa. Facoltativamente, è possibile fornire alcune informazioni associate a questo parametro. Immettere ad esempio "*Number*" in **TIPO**, "*200*" (impostazione predefinita) in **VALORI**.
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
