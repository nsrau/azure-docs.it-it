---
title: Esempi di criteri di Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: e951eb47290a2d1b6f892f1ccf923f6ce0c1899c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="api-management-policy-samples"></a>Esempi di criteri di Gestione API

I [criteri](api-management-howto-policies.md) sono una potente funzionalità del sistema che consente all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. La tabella seguente include i collegamenti agli esempi e una breve descrizione di ogni esempio.

|||
|---|---|
|**Criteri in ingresso**||
|[Add a Forwarded header to allow the backend API to construct proper URLs](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) (Aggiungere un'intestazione Forwarded per consentire all'API back-end di costruire URL corretti) |Illustra come aggiungere un'intestazione Forwarded nella richiesta in ingresso per consentire all'API back-end di costruire URL corretti.|
|[Aggiungere un'intestazione con un ID di correlazione](./policies/add-correlation-id.md?toc=api-management/toc.json) |Illustra come aggiungere un'intestazione con un ID di correlazione alla richiesta in ingresso.|
|[Aggiungere funzionalità a un servizio back-end e memorizzare la risposta nella cache](./policies/cache-response.md?toc=api-management/toc.json) |Illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo.|
|[Autorizzare l'accesso in base ad attestazioni JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Illustra come autorizzare l'accesso a specifici metodi HTTP in un'API in base ad attestazioni JWT.|
|[Autorizzare l'accesso con token OAuth Google](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Illustra come autorizzare l'accesso agli endpoint usando Google come provider di token OAuth.|
|[Generare una firma di accesso condiviso e inoltrare la richiesta ad Archiviazione di Azure](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Illustra come generare una [firma di accesso condiviso](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) usando espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri. |
|[Ottenere un token di accesso OAuth2 da AAD e inoltrarlo al back-end](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Fornisce un esempio di uso di OAuth2 per l'autorizzazione tra gateway e back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end.|
|[Ottenere un token X-CSRF dal gateway SAP usando criteri di invio richiesta](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP. |
|[Instradare la richiesta in base alle dimensioni del corpo](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Illustra come instradare le richieste in base alle dimensioni del relativo corpo.|
|[Send request context information to the backend service](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) (Inviare informazioni di contesto per la richiesta al servizio back-end) |Illustra come inviare informazioni di contesto al servizio back-end per la registrazione o l'elaborazione.|
|[Set response cache duration](./policies/set-cache-duration.md?toc=api-management/toc.json) (Impostare la durata della cache della risposta) |Illustra come impostare la durata della cache della risposta usando il valore maxAge nell'intestazione Cache-Control inviata dal back-end.|
|**Criteri in uscita**||
|[Filtrare il contenuto della risposta](./policies/filter-response-content.md?toc=api-management/toc.json) | Illustra come filtrare elementi dati dal payload della risposta in base al prodotto associato alla richiesta.|
|**Criteri in caso di errore**||
|[Registrare gli errori in Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.|
