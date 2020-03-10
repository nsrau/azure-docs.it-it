---
title: Importare e pubblicare la prima API in Gestione API di Azure
description: Informazioni su come importare un'API Specifica OpenAPI in Gestione API di Azure e testare l'API nel portale di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163496"
---
# <a name="import-and-publish-your-first-api"></a>Importare e pubblicare la prima API 

Questa esercitazione illustra come importare un'API back-end Specifica OpenAPI in formato JSON in Gestione API di Azure. Microsoft fornisce l'API back-end e la ospita in Azure all'indirizzo [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Dopo l'importazione dell'API back-end in Gestione API, l'API di Gestione API diventa una facciata per l'API back-end. Gestione API consente di personalizzare la facciata in base alle proprie esigenze senza modificare l'API back-end. Per altre informazioni, vedere [Trasformare e proteggere l'API](transform-api.md). 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Importare un'API in Gestione API
> * Testare l'API nel portale di Azure

![Nuova API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Prerequisiti

- Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
- [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importare e pubblicare un'API back-end

Questa sezione illustra come importare e pubblicare un'API back-end Specifica OpenAPI.
 
1. Nel riquadro di spostamento a sinistra dell'istanza di Gestione API selezionare **API** nella sezione **Gestione API**.
1. Selezionare il riquadro **OpenAPI**, quindi selezionare **Completa** nella schermata popup.
1. Nella schermata **Crea dalla specifica OpenAPI** usare i valori della tabella seguente per creare l'API.
   
   L'asterisco rosso accanto a un campo nel modulo indica che il campo è obbligatorio. È possibile impostare i valori dell'API durante la creazione o successivamente dalla scheda **Impostazioni**. 
   
   ![Creare un'API](./media/api-management-import-and-publish/create-api.png)
   
   |Impostazione|valore|Descrizione|
   |-------|-----|-----------|
   |**Specifica OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|Servizio che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.|
   |**Nome visualizzato**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Nome visualizzato nel portale per sviluppatori.|
   |**Nome**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Nome univoco per l'API.|
   |**Descrizione**|Dopo aver immesso l'URL del servizio precedente, Gestione API compila questo campo in base al codice JSON.|Descrizione facoltativa per l'API.|
   |**Schema URL**|**HTTPS**|Protocolli che è possibile usare per l'accesso all'API.|
   |**Suffisso dell'URL dell'API**|*conference*|Suffisso aggiunto all'URL di base per il servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ogni API di un editore specifico.|
   |**Prodotti**|**Illimitato**|Associazione di una o più API. Con ogni istanza di Gestione API vengono forniti due prodotti di esempio: **Starter** e **Senza limiti**. Per pubblicare l'API, associarla a un prodotto (in questo esempio, **Senza limiti**).<br/>È possibile includere numerose API in un prodotto e offrirle agli sviluppatori tramite il portale per sviluppatori. Per aggiungere questa API a un altro prodotto, digitare o selezionare il nome del prodotto. Ripetere questo passaggio per aggiungere l'API a più prodotti. È anche possibile aggiungere API ai prodotti in un secondo momento dalla pagina **Impostazioni**.<br/>Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. <br/>Se l'istanza di Gestione API è stata creata, si è già un amministratore e quindi tutti i prodotti dell'istanza sono già stati sottoscritti.|
   |**Tag**| |Tag per l'organizzazione delle API per la ricerca, il raggruppamento o il filtro.|
   |**Assegnare un numero di versione a questa API?**|Selezionare o deselezionare|Per altre informazioni sul controllo delle versioni, vedere [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Per pubblicare l'API, è necessario associarla a un prodotto. È possibile eseguire questa operazione nella pagina **Impostazioni**.
   
1. Selezionare **Create** (Crea).

In caso di problemi durante l'importazione della definizione API, vedere l'[elenco delle restrizioni e dei problemi noti](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testare la nuova API nel portale di Azure

È possibile chiamare le operazioni API direttamente dal portale di Azure, che rappresenta una soluzione pratica per visualizzare e testare le operazioni.

1. Nel riquadro di spostamento a sinistra dell'istanza di Gestione API selezionare **API** nella sezione **Gestione API** e quindi scegliere **Demo Conference API**.
1. Selezionare la scheda **Test** e quindi selezionare **GetSpeakers**. Nella pagina vengono visualizzati i **Parametri di query** e le **Intestazioni**, se presenti. La chiave **Ocp-Apim-Subscription-Key** viene compilata automaticamente per la chiave di sottoscrizione associata a questa API.
1. Selezionare **Send** (Invia).
   
   ![Mappa di test API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Il back-end risponde con **200 OK** e alcuni dati.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Importare la prima API
> * Testare l'API nel portale di Azure

Passare all'esercitazione successiva per imparare a creare e pubblicare un prodotto:

> [!div class="nextstepaction"]
> [Creare e pubblicare un prodotto](api-management-howto-add-products.md)
