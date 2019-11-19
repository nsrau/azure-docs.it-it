---
title: Eseguire l'autenticazione su più tenant
description: Descrive il modo in cui Azure Resource Manager gestisce le richieste di autenticazione su più tenant.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 1dccfc522179cd88e69bc5a58307b343aaef8f9e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149987"
---
# <a name="authenticate-requests-across-tenants"></a>Eseguire l'autenticazione delle richieste su più tenant

Quando si crea un'applicazione multi-tenant, potrebbe essere necessario gestire richieste di autenticazione per risorse in tenant diversi. Uno scenario comune è quando una macchina virtuale in un tenant deve essere aggiunta a una rete virtuale in un altro tenant. Azure Resource Manager fornisce un valore di intestazione per l'archiviazione dei token ausiliari per autenticare le richieste in tenant diversi.

## <a name="header-values-for-authentication"></a>Valori di intestazione per l'autenticazione

La richiesta include i valori di intestazione di autenticazione seguenti:

| Nome intestazione | DESCRIZIONE | Valore di esempio |
| ----------- | ----------- | ------------ |
| Autorizzazione | Token primario | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | Token ausiliari | Bearer &lt;ausiliario-token1&gt;, EncryptedBearer &lt;ausiliario-token2&gt;, Bearer &lt;ausiliario-token3&gt; |

L'intestazione ausiliaria può contenere fino a tre token ausiliari. 

Nel codice dell'app multi-tenant, ottenere il token di autenticazione per altri tenant e archiviarli nelle intestazioni ausiliari. Tutti i token devono provenire dallo stesso utente o dalla stessa applicazione. L'utente o l'applicazione devono essere stati invitati come guest in altri tenant.

## <a name="processing-the-request"></a>Elaborazione della richiesta

Quando l'app invia una richiesta a Resource Manager, la richiesta viene eseguita con l'identità dal token primario. Il token primario deve essere valido e non scaduto. Questo token deve provenire da un tenant in grado di gestire la sottoscrizione.

Quando la richiesta fa riferimento a una risorsa da un altro tenant, Resource Manager controlla i token ausiliari per determinare se è possibile elaborare la richiesta. Tutti i token ausiliari nell'intestazione devono essere validi e non scaduti. In presenza di token scaduti, Resource Manager restituisce un codice di risposta 401. La risposta include l'ID client e l'ID tenant dal token non valido. Se l'intestazione ausiliaria contiene un token valido per il tenant, viene elaborata la richiesta su più tenant.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle richieste di autenticazione, vedere [flussi di autenticazione e scenari di applicazione](../active-directory/develop/authentication-flows-app-scenarios.md).
* Per altre informazioni sui token, vedere [Token di accesso di Azure Active Directory](../active-directory/develop/access-tokens.md).
