---
title: Autorizzare l'accesso a Hub eventi di Azure
description: Questo articolo fornisce informazioni sulle diverse opzioni per autorizzare l'accesso alle risorse di Hub eventi di Azure.This article provides information about different options for authorizing access to Azure Event Hubs resources.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: d4304abf0ca089fbbea86f12cd03dea836db612e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368364"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizzare l'accesso a Hub eventi di Azure
Ogni volta che si pubblicano o si utilizzano eventi/dati da un hub eventi, il client tenta di accedere alle risorse di Hub eventi. Ogni richiesta a una risorsa protetta deve essere autorizzata in modo che il servizio possa garantire che il client disponga delle autorizzazioni necessarie per pubblicare/utilizzare i dati. 

Hub eventi di Azure offre le opzioni seguenti per autorizzare l'accesso alle risorse protette:Azure Event Hubs offers the following options for authorizing access to secure resources:

- Azure Active Directory
- Firma di accesso condiviso

> [!NOTE]
> Questo articolo si applica a entrambi gli scenari Hub eventi e [Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
L'integrazione di Azure Active Directory (Azure AD) per le risorse di Hub eventi fornisce il controllo degli accessi in base al ruolo per un controllo granulare sull'accesso di un client alle risorse. È possibile utilizzare il controllo degli accessi in base al ruolo per concedere autorizzazioni all'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2.0.The security principal is authenticated by Azure AD to return an OAuth 2.0 token. Il token può essere utilizzato per autorizzare una richiesta di accesso a una risorsa Hub eventi.

Per altre informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:For more information about authenticating with Azure AD, see the following articles:

- [Autenticare le richieste agli hub eventi di Azure usando Azure Active DirectoryAuthenticate requests to Azure Event Hubs using Azure Active Directory](authenticate-application.md)
- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active Directory.](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>Firme di accesso condiviso 
Le firme di accesso condiviso per le risorse di Hub eventi forniscono un accesso delegato limitato alle risorse di Hub eventi. L'aggiunta di vincoli sull'intervallo di tempo per il quale la firma è valida o per le autorizzazioni concesse offre flessibilità nella gestione delle risorse. Per altre informazioni, vedere [Eseguire l'autenticazione tramite firme](authenticate-shared-access-signature.md)di accesso condiviso. 

L'autorizzazione di utenti o applicazioni tramite un token OAuth 2.0 restituito da Azure AD offre una sicurezza e un utilizzo superiori rispetto alle firme di accesso condiviso. Con Azure AD non è necessario archiviare i token di accesso con il codice e rischiare potenziali vulnerabilità della sicurezza. Sebbene sia possibile continuare a usare le firme di accesso condiviso per concedere un accesso granulare alle risorse di Hub eventi, Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o preoccuparsi di revocare una firma di accesso condiviso compromessa. 

Per impostazione predefinita, tutte le risorse di Hub eventi sono protette e sono disponibili solo per il proprietario dell'account. Sebbene sia possibile utilizzare una delle strategie di autorizzazione descritte in precedenza per concedere ai client l'accesso alle risorse dell'Hub eventi. Solo le risorse di Hub eventi create con il modello di distribuzione di Azure Resource Manager supportano l'autorizzazione di Azure AD. Microsoft consiglia di usare Azure AD quando possibile per garantire la massima sicurezza e facilità d'uso.

Per altre informazioni sull'autorizzazione tramite sAS, vedere [Autorizzazione dell'accesso alle risorse di Hub eventi tramite firme](authorize-access-shared-access-signature.md)di accesso condiviso .

## <a name="next-steps"></a>Passaggi successivi
- Esaminare [gli esempi di controllo degli accessi in base](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) al ruolo pubblicati nel repository GitHub.Review RBAC samples published in our GitHub repository. 
- Vedere gli articoli seguenti:
    - [Autenticare le richieste agli hub eventi di Azure da un'applicazione tramite Azure Active DirectoryAuthenticate requests to Azure Event Hubs from an application using Azure Active Directory](authenticate-application.md)
    - [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources](authenticate-managed-identity.md)
    - [Autenticare le richieste agli hub eventi di Azure usando le firme di accesso condivisoAuthenticate requests to Azure Event Hubs using Shared Access Signatures](authenticate-shared-access-signature.md)
    - [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizzare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthorize access to Event Hubs resources using Shared Access Signatures](authorize-access-shared-access-signature.md)

