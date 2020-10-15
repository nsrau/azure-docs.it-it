---
title: 'Esercitazione: Importare e pubblicare la prima API in Gestione API di Azure'
description: Questa esercitazione descrive come importare un'API Specifica OpenAPI in Gestione API di Azure e quindi testare l'API nel portale di Azure.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626963"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Esercitazione: Importare e pubblicare la prima API

Questa esercitazione illustra come importare un'API back-end Specifica OpenAPI in formato JSON in Gestione API di Azure. Microsoft fornisce l'API back-end usata in questo esempio e la ospita in Azure all'indirizzo [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Dopo l'importazione dell'API back-end in Gestione API, l'API di Gestione API diventa una facciata per l'API back-end. Gestione API consente di personalizzare la facciata in base alle proprie esigenze senza modificare l'API back-end. Per altre informazioni, vedere [Trasformare e proteggere l'API](transform-api.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Importare un'API in Gestione API
> * Testare l'API nel portale di Azure

Dopo l'importazione, è possibile gestire l'API nel portale di Azure.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nuova API in Gestione API":::

## <a name="prerequisites"></a>Prerequisiti

- Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
- [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importare e pubblicare un'API back-end

Questa sezione illustra come importare e pubblicare un'API back-end Specifica OpenAPI.

1. Nel riquadro di spostamento a sinistra dell'istanza di Gestione API selezionare **API**.
1. Selezionare il riquadro **OpenAPI**.
1. Nella finestra **Crea dalla specifica OpenAPI** selezionare **Completa**.
1. Immettere i valori dalla tabella seguente. Fare quindi clic su **Crea** per creare l'API.

   È possibile impostare i valori dell'API durante la creazione o successivamente dalla scheda **Impostazioni**.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Nuova API in Gestione API":::


   |Impostazione|valore|Descrizione|
   |-------|-----|-----------|
   |**Specifica OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|Servizio che implementa l'API Gestione API inoltra le richieste a questo indirizzo.|
   |**Nome visualizzato**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Nome visualizzato nel [portale per sviluppatori](api-management-howto-developer-portal.md).|
   |**Nome**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Nome univoco per l'API.|
   |**Descrizione**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Descrizione facoltativa per l'API.|
   |**Schema URL**|**HTTPS**|Protocolli che possono accedere all'API.|
   |**Suffisso dell'URL dell'API**|*conference*|Suffisso aggiunto all'URL di base per il servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ogni API di un editore specifico.|
   |**Tag**| |Tag per l'organizzazione delle API per la ricerca, il raggruppamento o il filtro.|
   |**Prodotti**|**Illimitato**|Associazione di una o più API. Con ogni istanza di Gestione API vengono forniti due prodotti di esempio: **Starter** e **Senza limiti**. Per pubblicare l'API, associarla a un prodotto (in questo esempio, **Senza limiti**).<br/><br/> È possibile includere numerose API in un prodotto e offrirle agli sviluppatori tramite il portale per sviluppatori. Per aggiungere questa API a un altro prodotto, digitare o selezionare il nome del prodotto. Ripetere questo passaggio per aggiungere l'API a più prodotti. È anche possibile aggiungere API ai prodotti in un secondo momento dalla pagina **Impostazioni**.<br/><br/>  Per altre informazioni sui prodotti, vedere [Creare e pubblicare un prodotto](api-management-howto-add-products.md).|
   |**Gateway**|**Gestiti**|Gateway API che espongono l'API. Questo campo è disponibile solo nei servizi di livello **Developer** e **Premium**.<br/><br/>**Gestito** indica il gateway integrato nel servizio Gestione API e ospitato da Microsoft in Azure. I [gateway self-hosted](self-hosted-gateway-overview.md) sono disponibili solo nei livelli di servizio Premium e Developer e possono essere distribuiti in locale o in altri cloud.<br/><br/> Se non è selezionato alcun gateway, l'API non sarà disponibile e le richieste API non verranno eseguite.|
   |**Assegnare un numero di versione a questa API?**|Selezionare o deselezionare|Per altre informazioni, vedere [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Per pubblicare l'API in consumer API, è necessario associarla a un prodotto.

2. Selezionare **Crea**.

In caso di problemi durante l'importazione della definizione API, vedere l'[elenco delle restrizioni e dei problemi noti](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testare la nuova API nel portale di Azure

È possibile chiamare le operazioni API direttamente dal portale di Azure, che rappresenta una soluzione pratica per visualizzare e testare le operazioni.

1. Nel riquadro di spostamento a sinistra dell'istanza di Gestione API selezionare **API** > **Demo Conference API**.
1. Selezionare la scheda **Test** e quindi selezionare **GetSpeakers**. Nella pagina vengono visualizzati i **Parametri di query** e le **Intestazioni**, se presenti. La chiave **Ocp-Apim-Subscription-Key** viene compilata automaticamente per la chiave di sottoscrizione associata a questa API.
1. Selezionare **Send** (Invia).

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Nuova API in Gestione API":::

   Il back-end risponde con **200 OK** e alcuni dati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Importare la prima API
> * Testare l'API nel portale di Azure

Passare all'esercitazione successiva per imparare a creare e pubblicare un prodotto:

> [!div class="nextstepaction"]
> [Creare e pubblicare un prodotto](api-management-howto-add-products.md)
