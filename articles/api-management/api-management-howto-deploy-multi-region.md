---
title: "Distribuire servizi di Gestione API di Azure in più aree di Azure | Documentazione Microsoft"
description: "Informazioni su come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: e126e34bc9fce21243b0ef79f5ab661aec3a2de6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure
Gestione API supporta la distribuzione in più aree che consente agli autori di API di distribuire un solo servizio Gestione API in qualsiasi numero di aree di Azure. Ciò consente di ridurre la latenza delle richieste percepita dagli utenti dell'API distribuiti geograficamente, oltre a migliorare la disponibilità del servizio se un'area viene portata offline. 

Quando un servizio di gestione API viene creato, inizialmente contiene una sola [unità][unit] e si trova in una sola area di Azure, designata come area primaria. È possibile aggiungere facilmente altre aree tramite il portale di Azure. Un server gateway di gestione API viene distribuito in ogni area e il traffico delle chiamate verrà indirizzato al gateway più vicino. Quando un'area passa offline, il traffico viene automaticamente reindirizzato al gateway successivo più vicino. 

> [!IMPORTANT]
> La distribuzione in più aree è disponibile solo nel livello **[Premium][Premium]**.
> 
> 

## <a name="add-region"></a>Distribuire un'istanza del servizio Gestione API in una nuova area
> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance].
> 
> 

Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API. 

![Scheda Scalabilità][api-management-scale-service]

Per distribuire una nuova area, fare clic su **+ Aggiungi area** dalla barra degli strumenti.

![Aggiungere un'area][api-management-add-region]

Selezionare la posizione dall'elenco a discesa e impostare il numero di unità con il dispositivo di scorrimento.

![Specificare le unità][api-management-select-location-units]

Fare clic su **Aggiungi** per inserire la selezione nella tabella Posizioni. 

Ripetere l'operazione per configurare tutte le posizioni e fare clic su **Salva** dalla barra degli strumenti per avviare il processo di distribuzione.

## <a name="remove-region"> </a>Eliminare un'istanza del servizio di Gestione API da una posizione
Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API. 

![Scheda Scalabilità][api-management-scale-service]

Per la località che si vuole rimuovere aprire il menu di scelta rapida usando il pulsante **...** sul lato destro della tabella. Selezionare l'opzione **Elimina**.

![Rimuovere un'area][api-management-remove-region]

Confermare l'eliminazione e fare clic su **Salva** per applicare le modifiche.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

