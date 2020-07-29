---
title: Chiamare API Web e API REST personalizzate
description: Chiamare API Web e API REST personalizzate da App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659781"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Distribuire e chiamare API personalizzate da flussi di lavoro in App per la logica

Dopo aver [creato API personalizzate](./logic-apps-create-api-app.md) da usare nei flussi di lavoro di app per la logica, perché sia possibile chiamarle è necessario distribuirle. È possibile distribuire le API come [app Web](../app-service/overview.md), ma è consigliabile distribuirle come [app per le API](../app-service/app-service-web-tutorial-rest-api.md), in modo da semplificare le attività durante la compilazione, l'hosting e l'uso delle API nel cloud e in locale. Non è necessario modificare alcuna parte di codice nelle API, ma è sufficiente distribuire il codice in un'app per le API. È possibile ospitare le API in [Servizio app di Azure](../app-service/overview.md), una soluzione PaaS (Platform-as-a-Service, piattaforma distribuita come servizio) che offre hosting di API semplice e altamente scalabile.

Sebbene sia possibile chiamare qualsiasi API da un'app per la logica, per ottimizzare i risultati aggiungere [metadati Swagger](https://swagger.io/specification/) che descrivano le operazioni e i parametri dell'API. Il documento Swagger semplifica l'integrazione dell'API e funziona meglio con le app per la logica.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuire l'API come app Web o app per le API

Prima di chiamare l'API personalizzata da un'app per la logica, implementare l'API come app Web o app per le API al servizio app di Azure. Per rendere leggibile il documento Swagger per Progettazione app per la logica, impostare le proprietà di definizione dell'API e attivare la [condivisione di risorse tra le origini](../app-service/overview.md) per l'app Web o l'app per le API.

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'app Web o l'app per le API.

2. Nel menu dell'app visualizzato scegliere **Definizione API** in **API**. Impostare la **posizione della definizione dell'API** sull'URL del file swagger.json.

   In genere, l'URL viene visualizzato in questo formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Collegamento al documento Swagger per l'API personalizzata](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Scegliere **CORS** nell'area **API**. Impostare i criteri CORS per **Origini consentite** **"*"** (consenti tutto).

   Questa impostazione consente le richieste da Progettazione app per la logica.

   ![Consentire le richieste da Progettazione app per la logica all'API personalizzata](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Per altre informazioni, vedere [Ospitare un'API RESTful con CORS in Servizio app di Azure](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chiamare l'API personalizzata dai flussi di lavoro delle app per la logica

Dopo aver configurato le proprietà di definizione dell'API e CORS, i trigger e le azioni dell'API personalizzata saranno disponibili per l'inserimento nel flusso di lavoro delle app per la logica. 

*  Per visualizzare i siti Web con URL OpenAPI, è possibile esplorare i siti Web di sottoscrizione in Progettazione app per la logica.

*  Per visualizzare le azioni e gli input disponibili puntando a un documento di Swagger, usare l'[azione HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Per chiamare qualsiasi API, incluse le API che non hanno o non espongono un documento Swagger, è sempre possibile creare una richiesta con l'[azione HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei connettori personalizzati](../logic-apps/custom-connector-overview.md)
