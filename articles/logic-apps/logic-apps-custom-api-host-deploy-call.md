---
title: Distribuire e chiamare API Web e API REST da App per la logica di Azure | Microsoft Docs
description: Distribuire e chiamare API Web e API REST per flussi di lavoro di integrazione di sistema in App per la logica di Azure
keywords: API Web, API REST, connettori, flussi di lavoro, integrazioni di sistema, autenticazione
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: c7a240bf5b7ed5e7780b90f438d2e336ee79f0b3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Distribuire e chiamare API personalizzate da flussi di lavoro di app per la logica

Dopo aver [creato API personalizzate](./logic-apps-create-api-app.md) da usare in flussi di lavoro di app per la logica, è necessario distribuire le API prima di poterle chiamare. È possibile distribuire le API come [app Web](../app-service/app-service-web-overview.md), ma è consigliabile distribuirle come [app per le API](../app-service/app-service-web-tutorial-rest-api.md), in modo da semplificare le attività durante la compilazione, l'hosting e l'uso delle API nel cloud e in locale. Non è necessario modificare alcuna parte di codice nelle API, ma è sufficiente distribuire il codice in un'app per le API. È possibile ospitare le API in [Servizio app di Azure](../app-service/app-service-web-overview.md), una soluzione PaaS (Platform-as-a-Service, piattaforma distribuita come servizio) che offre hosting di API semplice e altamente scalabile.

Benché sia possibile chiamare qualsiasi API da un'app per la logica, per un'esperienza ottimale aggiungere [metadati OpenAPI (in precedenza Swagger)](http://swagger.io/specification/) che descrivano le operazioni e i parametri dell'API. Il file OpenAPI semplifica l'integrazione dell'API e funziona meglio con le app per la logica.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuire l'API come app Web o app per le API

Prima di chiamare l'API personalizzata da un'app per la logica, implementare l'API come app Web o app per le API al servizio app di Azure. Inoltre, per rendere il file OpenAPI leggibile per Progettazione app per la logica, impostare le proprietà di definizione dell'API e attivare la [condivisione di risorse tra le origini](../app-service/app-service-web-overview.md) per l'app Web o l'app per le API.

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'app Web o l'app per le API.

2. Nel menu dell'app visualizzato scegliere **Definizione API** in **API**. Impostare la **Posizione di definizione dell'API** sull'URL del file OpenAPI swagger.json.

   In genere, l'URL viene visualizzato in questo formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Collegarsi a un file OpenAPI per l'API personalizzata](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Scegliere **CORS** nell'area **API**. Impostare i criteri CORS per **Origini consentite** **"*"** (consenti tutto).

   Questa impostazione consente le richieste da Progettazione app per la logica.

   ![Consentire le richieste da Progettazione app per la logica all'API personalizzata](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Per altre informazioni, vedere [Ospitare un'API RESTful con CORS in Servizio app di Azure](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chiamare l'API personalizzata dai flussi di lavoro delle app per la logica

Dopo aver configurato le proprietà di definizione dell'API e CORS, i trigger e le azioni dell'API personalizzata saranno disponibili per l'inserimento nel flusso di lavoro delle app per la logica. 

*  Per visualizzare i siti Web con URL OpenAPI, è possibile esplorare i siti Web di sottoscrizione in Progettazione app per la logica.

*  Per visualizzare le azioni e gli input disponibili puntando a un documento OpenAPI, usare l'[azione HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Per chiamare qualsiasi API, incluse le API che non hanno o non espongono un documento OpenAPI, è sempre possibile creare una richiesta con l'[azione HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei connettori personalizzati](../logic-apps/custom-connector-overview.md)