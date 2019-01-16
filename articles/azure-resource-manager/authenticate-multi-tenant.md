---
title: Eseguire l'autenticazione su più tenant - Azure Resource Manager
description: Descrive il modo in cui Azure Resource Manager gestisce le richieste di autenticazione su più tenant.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109476"
---
# <a name="authenticate-requests-across-tenants"></a>Eseguire l'autenticazione delle richieste su più tenant

Quando si crea un'applicazione multi-tenant, potrebbe essere necessario gestire richieste di autenticazione per risorse in tenant diversi. Uno scenario comune è quando una macchina virtuale in un tenant deve essere aggiunta a una rete virtuale in un altro tenant. Azure Resource Manager fornisce un valore di intestazione per l'archiviazione dei token ausiliari per autenticare le richieste in tenant diversi.

## <a name="header-values-for-authentication"></a>Valori di intestazione per l'autenticazione

La richiesta include i valori di intestazione di autenticazione seguenti:

| Nome intestazione | DESCRIZIONE | Valore di esempio |
| ----------- | ----------- | ------------ |
| Authorization | Token primario | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | Token ausiliari | Bearer &lt;auxiliary-token1&gt;; EncryptedBearer &lt;auxiliary-token2&gt;; Bearer &lt;auxiliary-token3&gt; |

L'intestazione ausiliaria può contenere fino a tre token ausiliari. 

Nel codice dell'app multi-tenant, ottenere il token di autenticazione per altri tenant e archiviarli nelle intestazioni ausiliari. Tutti i token devono provenire dallo stesso utente o dalla stessa applicazione. L'utente o l'applicazione devono essere stati invitati come guest in altri tenant.

## <a name="processing-the-request"></a>Elaborazione della richiesta

Quando l'app invia una richiesta a Resource Manager, la richiesta viene eseguita con l'identità dal token primario. Il token primario deve essere valido e non scaduto. Questo token deve provenire da un tenant in grado di gestire la sottoscrizione.

Quando la richiesta fa riferimento a una risorsa da un altro tenant, Resource Manager controlla i token ausiliari per determinare se è possibile elaborare la richiesta. Tutti i token ausiliari nell'intestazione devono essere validi e non scaduti. In presenza di token scaduti, Resource Manager restituisce un codice di risposta 401. La risposta include l'ID client e l'ID tenant dal token non valido. Se l'intestazione ausiliaria contiene un token valido per il tenant, viene elaborata la richiesta su più tenant.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'invio di richieste di autenticazione con le API di Resource Manager, vedere [Usare l'API di autenticazione di Resource Manager per accedere alle sottoscrizioni](resource-manager-api-authentication.md).
* Per altre informazioni sui token, vedere [Token di accesso di Azure Active Directory](/azure/active-directory/develop/access-tokens).
