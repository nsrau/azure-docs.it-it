---
title: Esempi di criteri di Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b8f97bb27b3eb45c731b20ad51e35fde9b6a394a
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126901"
---
# <a name="api-management-policy-samples"></a>Esempi di criteri di Gestione API

I [criteri](../api-management-howto-policies.md) sono una potente funzionalità del sistema che consente all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. La tabella seguente include i collegamenti agli esempi e una breve descrizione di ogni esempio.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criteri in ingresso**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Add a Forwarded header to allow the backend API to construct proper URLs](./set-header-to-enable-backend-to-construct-urls.md) (Aggiungere un'intestazione Forwarded per consentire all'API back-end di costruire URL corretti) | Illustra come aggiungere un'intestazione Forwarded nella richiesta in ingresso per consentire all'API back-end di costruire URL corretti.                                                                                                        |
| [Aggiungere un'intestazione con un ID di correlazione](./add-correlation-id.md)                                                             | Illustra come aggiungere un'intestazione con un ID di correlazione alla richiesta in ingresso.                                                                                                                                        |
| [Aggiungere funzionalità a un servizio back-end e memorizzare la risposta nella cache](./cache-response.md)                                             | Illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo.                                                                    |
| [Autorizzare l'accesso in base ad attestazioni JWT](./authorize-request-based-on-jwt-claims.md)                                              | Illustra come autorizzare l'accesso a specifici metodi HTTP in un'API in base ad attestazioni JWT.                                                                                                                                       |
| [Autorizzare richieste che usano un provider di autorizzazioni esterno](./authorize-request-using-external-authorizer.md)                                                   | Illustra come usare provider di autorizzazioni esterni per proteggere l'accesso all'API.                                                                                                                                                               |
| [Autorizzare l'accesso con token OAuth Google](./use-google-as-oauth-token-provider.md)                                            | Illustra come autorizzare l'accesso agli endpoint usando Google come provider di token OAuth.                                                                                                                                    |
| [Filtrare gli indirizzi IP quando si usa un gateway applicazione](./filter-ip-addresses-when-using-appgw.md) | Illustra come filtrare gli indirizzi IP nei criteri quando si accede all'istanza di Gestione API tramite un gateway applicazione
| [Generare una firma di accesso condiviso e inoltrare la richiesta ad Archiviazione di Azure](./generate-shared-access-signature.md)                  | Illustra come generare una [firma di accesso condiviso](../../storage/storage-dotnet-shared-access-signature-part-1.md) usando espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri. |
| [Ottenere un token di accesso OAuth2 da AAD e inoltrarlo al back-end](./use-oauth2-for-authorization.md)                             | Fornisce un esempio di uso di OAuth2 per l'autorizzazione tra gateway e back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end.                                                    |
| [Ottenere un token X-CSRF dal gateway SAP usando criteri di invio richiesta](./get-x-csrf-token-from-sap-gateway.md)                           | Illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP.                                                                                                                           |
| [Instradare la richiesta in base alle dimensioni del corpo](./route-requests-based-on-size.md)                                            | Illustra come instradare le richieste in base alle dimensioni del relativo corpo.                                                                                                                                                       |
| [Send request context information to the backend service](./send-request-context-info-to-backend-service.md) (Inviare informazioni di contesto per la richiesta al servizio back-end)                    | Illustra come inviare informazioni di contesto al servizio back-end per la registrazione o l'elaborazione.                                                                                                                                |
| [Set response cache duration](./set-cache-duration.md) (Impostare la durata della cache della risposta)                                                                          | Illustra come impostare la durata della cache della risposta usando il valore maxAge nell'intestazione Cache-Control inviata dal back-end.                                                                                                             |
| **Criteri in uscita**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Filtrare il contenuto della risposta](./filter-response-content.md)                                                                         | Illustra come filtrare elementi dati dal payload della risposta in base al prodotto associato alla richiesta.                                                                                                        |
| **Criteri in caso di errore**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Registrare gli errori in Stackify](./log-errors-to-stackify.md)                                                                           | Illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.                                                                                                                                            |
