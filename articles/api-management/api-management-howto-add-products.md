---
title: Come creare e pubblicare un prodotto in Gestione API di Azure
description: Informazioni su come creare e pubblicare prodotti in Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: b9e3127a6b055a1fe013fa91714676a7c56686c5
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-publish-a-product"></a>Creare e pubblicare un prodotto  

In Gestione API di Azure un prodotto contiene una o più API, oltre a una quota di utilizzo e alle condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto.  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

![Prodotto aggiunto](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>prerequisiti

+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>Creare e pubblicare un prodotto

1. Fare clic su **Prodotti** nel menu a sinistra per visualizzare la pagina **Prodotti**.
2. Fare clic su **+ Prodotto**.

    ![Prodotto aggiunto](media/api-management-howto-add-products/add-product.png)

    Quando si aggiunge un prodotto, è necessario fornire le informazioni seguenti: 

    |NOME|DESCRIZIONE|
    |---|---|
    |Nome visualizzato|Nome con cui deve essere visualizzato nel **portale per sviluppatori**.|
    |NOME|Nome descrittivo del prodotto.|
    |DESCRIZIONE|Il campo **Descrizione** consente di specificare informazioni dettagliate sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altre informazioni utili.|
    |Stato|Fare clic su **Pubblicato** se si vuole pubblicare il prodotto. Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**.|
    |Richiede approvazione|Selezionare **Richiedi approvazione della sottoscrizione** se si preferisce che i tentativi di sottoscrizione al prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se la casella è deselezionata, i tentativi di sottoscrizione vengono approvati automaticamente. |
    |Limite per il numero di sottoscrizioni|Per limitare il numero di più sottoscrizioni simultanee, immettere il limite per le sottoscrizioni. |
    |Note legali|È possibile includere le condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto.|
    |API|I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. <br/> È possibile aggiungere un'API esistente durante la creazione di prodotto. È possibile aggiungere un'API al prodotto in un secondo momento, dalla pagina **Impostazioni** dei prodotti o durante la creazione di un'API.|<br/>Gli sviluppatori devono prima sottoscrivere un prodotto per avere accesso all'API. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto.<br/> Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.|

3. Fare clic su **Crea** per creare il nuovo prodotto.

### <a name="add-more-configurations"></a>Aggiungere altre configurazioni

È possibile continuare a configurare il prodotto dopo averlo salvato scegliendo la scheda **Impostazioni**. 

Visualizzare/Aggiungere i sottoscrittori al prodotto dalla scheda **Sottoscrizioni**.

Impostare una visibilità di un prodotto per sviluppatori o guest dalla scheda **Controllo dell'accesso**.

## <a name="add-apis"></a>Aggiungere API a un prodotto

I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. È possibile aggiungere un'API esistente durante la creazione di prodotto. È possibile aggiungere un'API al prodotto in un secondo momento, dalla pagina **Impostazioni** dei prodotti o durante la creazione di un'API.

Gli sviluppatori devono prima sottoscrivere un prodotto per avere accesso all'API. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e si è quindi effettuata la sottoscrizione a ogni prodotto per impostazione predefinita.

### <a name="add-an-api-to-an-existing-product"></a>Aggiungere un'API a un prodotto esistente

1. Selezionare un prodotto.
2. Selezionare la scheda API.
3. Fare clic su **+API**.
4. Scegliere un'API e fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare un'API vuota e simulare le risposte di un'API](mock-api-responses.md)
