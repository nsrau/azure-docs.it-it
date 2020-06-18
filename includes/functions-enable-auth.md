---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648924"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Abilitare l'autenticazione/autorizzazione di Servizio app di Azure

La piattaforma Servizio app di Azure consente di usare Azure Active Directory (AAD) e diversi provider di identità di terze parti per autenticare i client. È possibile usare questa strategia per implementare regole di autorizzazione personalizzate per le funzioni ed è possibile lavorare con le informazioni utente dal codice di funzione. Per altre informazioni, vedere [Autenticazione e autorizzazione in Servizio app di Azure](../articles/app-service/overview-authentication-authorization.md) e [Utilizzo delle identità client](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Usare Gestione API di Azure (APIM) per autenticare le richieste

APIM offre un'ampia gamma di opzioni di sicurezza di API per le richieste in ingresso. Per altre informazioni, vedere [Criteri di autenticazione di Gestione API di Azure](../articles/api-management/api-management-authentication-policies.md). Con APIM, è possibile configurare l'app per le funzioni in modo che accetti le richieste solo dall'indirizzo IP dell'istanza APIM. Per altre informazioni, vedere [Restrizioni degli indirizzi IP](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
