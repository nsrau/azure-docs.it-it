---
title: "Esercitazione: Usare le revisioni in Gestione API per apportare modifiche che non causano un'interruzione in modo sicuro"
titleSuffix: Azure API Management
description: Seguire le procedure di questa esercitazione per apprendere come apportare modifiche non di rilievo usando le revisioni in Gestione API.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377506"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Esercitazione: Usare le revisioni per apportare nell'API modifiche che non causano un'interruzione in modo sicuro
Quando l'API è pronta e inizia a essere usata dagli sviluppatori, è necessario apportare modifiche all'API e contemporaneamente evitare di interferire con i chiamanti dell'API. È anche utile far conoscere agli sviluppatori le modifiche apportate. 

In Gestione API di Azure è possibile usare le *revisioni* per apportare modifiche che non causano un'interruzione alle API al fine di modellare e testare le modifiche in modo sicuro. Quando si è pronti, è sufficiente impostare una revisione come corrente e sostituire l'API in uso. 

Per i concetti di base, vedere [Versioni e revisioni](https://azure.microsoft.com/blog/versions-revisions/) e [Controllo delle versioni delle API con Gestione API di Azure](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere una nuova revisione
> * Apportare modifiche non di rilievo alla revisione
> * Rendere la revisione corrente e aggiungere una voce del registro modifiche
> * Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Revisioni dell'API nel portale di Azure":::

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

## <a name="add-a-new-revision"></a>Aggiungere una nuova revisione

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Gestione API.
1. Selezionare **API**.
2. Selezionare **Demo Conference API** nell'elenco di API oppure l'API a cui si vuole aggiungere le revisioni.
3. Selezionare la scheda **Revisioni**.
4. Selezionare **+ Aggiungi revisione**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Aggiungere una revisione API":::

    > [!TIP]
    > È anche possibile scegliere **Aggiungi revisione** nel menu di scelta rapida ( **...** ) dell'API.

5. Fornire una descrizione per la nuova revisione, per facilitare la comprensione dell'uso.
6. Selezionare **Crea**.
7. La nuova revisione è stata creata.

    > [!NOTE]
    > L'API originale resta nella **Revisione 1**. Si tratta della revisione che gli utenti continueranno a chiamare fino a quando non si imposta come corrente una revisione diversa.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apportare modifiche non di rilievo alla revisione

1. Selezionare **Demo Conference API** (API Demo Conference) nell'elenco di API.
1. Selezionare la scheda **Progettazione** nella parte superiore della schermata.
1. Si noti che il **selettore di revisione** (appena sopra la scheda Progettazione) mostra la revisione corrente come **Revisione 2**.

    > [!TIP]
    > Usare il selettore di revisione per alternare le revisioni su cui si intende lavorare.
1. Selezionare **+ Aggiungi operazione**.
1. Impostare la nuova operazione su **POST** e il nome, il nome visualizzato e l'URL dell'operazione su **test**.
1. **Salvare** la nuova operazione.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Modificare la revisione":::
1. È stata ora apportata una modifica alla **Revisione 2**. Usare il **selettore di revisione** nella parte superiore della pagina per tornare alla **Revisione 1**.
1. Si noti che la nuova operazione non viene visualizzata nella **Revisione 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendere la revisione corrente e aggiungere una voce del registro modifiche

1. Selezionare la scheda **Revisioni** dal menu nella parte superiore della pagina.
1. Aprire il menu di scelta rapida (**...**) per la **Revisione 2**.
1. Selezionare **Imposta come corrente**.
1. Selezionare **Post to Public Change Log for this API**(Pubblica nel log delle modifiche pubblico per questa API) per pubblicare delle note sulla modifica. Fornire una descrizione della modifica comprensibile agli sviluppatori, ad esempio: **Test delle revisioni. Aggiunta una nuova operazione "test".**
1. La **Revisione 2** è ora quella corrente.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu Revisione nella finestra Revisioni":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche

Nel [portale per sviluppatori](api-management-howto-developer-portal-customize.md), è possibile esaminare le modifiche apportate all'API e il log delle modifiche.

1. Nel portale di Azure selezionare **API**.
1. Selezionare **Portale per sviluppatori** dal menu in alto.
1. Nel portale per sviluppatori selezionare **API**, quindi **Demo Conference API**.
1. Si noti che è ora disponibile la nuova operazione **test**.
1. Fare clic su **Log delle modifiche** accanto al nome dell'API.
1. Si noti che il registro modifiche è ora visualizzato in questo elenco.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere una nuova revisione
> * Apportare modifiche non di rilievo alla revisione
> * Rendere la revisione corrente e aggiungere una voce del registro modifiche
> * Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md)
