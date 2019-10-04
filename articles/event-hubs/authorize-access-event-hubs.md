---
title: Autorizzare l'accesso a Hub eventi di Azure
description: Questo articolo fornisce informazioni sulle diverse opzioni per autorizzare l'accesso alle risorse di hub eventi di Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: f01758c70e52f96fcd22a94e9b83f910cbf200c9
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035881"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizzare l'accesso a Hub eventi di Azure
Ogni volta che si pubblicano o utilizzano eventi/dati da un hub eventi, il client sta provando ad accedere alle risorse di hub eventi. Ogni richiesta a una risorsa protetta deve essere autorizzata, in modo che il servizio possa garantire che il client disponga delle autorizzazioni necessarie per pubblicare/utilizzare i dati. 

Hub eventi di Azure offre le opzioni seguenti per autorizzare l'accesso a risorse protette:

## <a name="azure-active-directory"></a>Azure Active Directory
L'integrazione di Azure Active Directory (Azure AD) per le risorse di hub eventi fornisce il controllo degli accessi in base al ruolo (RBAC) per un controllo con granularità fine sull'accesso di un client alle risorse. È possibile usare il controllo degli accessi in base al ruolo (RBAC) per concedere le autorizzazioni all'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2,0. Il token può essere usato per autorizzare una richiesta di accesso a una risorsa di hub eventi.

Per ulteriori informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:

- [Autenticare le richieste a hub eventi di Azure usando Azure Active Directory](authenticate-application.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="share-access-signatures"></a>Condividi firme di accesso 
Le firme di accesso condiviso (SAS) per le risorse di hub eventi forniscono un accesso delegato limitato alle risorse di hub eventi. L'aggiunta di vincoli all'intervallo di tempo per cui la firma è valida o per le autorizzazioni concesse garantisce flessibilità nella gestione delle risorse. Per altre informazioni, vedere [eseguire l'autenticazione con firme di accesso condiviso (SAS)](authenticate-shared-access-signature.md). 

L'autorizzazione di utenti o applicazioni tramite un token OAuth 2,0 restituito da Azure AD garantisce una sicurezza e una facilità di utilizzo superiori rispetto alle firme di accesso condiviso (SAS). Con Azure AD, non è necessario archiviare i token di accesso con il codice e rischiare potenziali vulnerabilità della sicurezza. Sebbene sia possibile continuare a usare le firme di accesso condiviso (SAS) per concedere l'accesso con granularità fine alle risorse di hub eventi, Azure AD offre funzionalità simili senza dover gestire i token SAS o preoccuparsi di revocare una firma di accesso condiviso compromessa. 

Per impostazione predefinita, tutte le risorse di hub eventi sono protette e sono disponibili solo per il proprietario dell'account. Sebbene sia possibile usare una delle strategie di autorizzazione descritte in precedenza per concedere ai client l'accesso alle risorse dell'hub eventi. Solo le risorse di hub eventi create con Azure Resource Manager modello di distribuzione supportano Azure AD autorizzazione. Microsoft consiglia di utilizzare Azure AD quando possibile per la massima sicurezza e semplicità d'uso.

Per altre informazioni sull'autorizzazione con SAS, vedere [autorizzazione dell'accesso alle risorse di hub eventi tramite firme di accesso condiviso](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Passaggi successivi
- Esaminare gli [esempi RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) pubblicati nel repository GitHub. 
- Vedere gli articoli seguenti:
    - [Autenticare le richieste a hub eventi di Azure da un'applicazione usando Azure Active Directory](authenticate-application.md)
    - [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md)
    - [Autenticare le richieste a hub eventi di Azure usando le firme di accesso condiviso](authenticate-shared-access-signature.md)
    - [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizzare l'accesso alle risorse di hub eventi usando le firme di accesso condiviso](authorize-access-shared-access-signature.md)

