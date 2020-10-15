---
title: Autenticazione e autorizzazione del bus di servizio di Azure | Documentazione Microsoft
description: Autenticare le app sul bus di servizio usando l'autenticazione con firma di accesso condiviso (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d5d0ed03c869bd574e4cfaa52ac7b62e8cb7fb98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066862"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticazione e autorizzazione del bus di servizio
Esistono due modi per autenticare e autorizzare l'accesso alle risorse del bus di servizio di Azure: Azure Activity directory (Azure AD) e le firme di accesso condiviso (SAS). Questo articolo fornisce informazioni dettagliate sull'uso di questi due tipi di meccanismi di sicurezza. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD integrazione per le risorse del bus di servizio fornisce il controllo degli accessi in base al ruolo (RBAC) per un controllo con granularità fine sull'accesso di un client alle risorse. È possibile usare il controllo degli accessi in base al ruolo per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2,0. Il token può essere usato per autorizzare una richiesta di accesso a una risorsa del bus di servizio (coda, argomento e così via).

Per ulteriori informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:

- [Eseguire l'autenticazione con le identità gestite](service-bus-managed-service-identity.md)
- [Eseguire l'autenticazione da un'applicazione](authenticate-application.md)

> [!NOTE]
> L' [API REST del bus di servizio](/rest/api/servicebus/) supporta l'autenticazione OAuth con Azure ad.

> [!IMPORTANT]
> L'autorizzazione di utenti o applicazioni che usano il token OAuth 2,0 restituito da Azure AD offre sicurezza e facilità d'uso superiori rispetto alle firme di accesso condiviso (SAS). Con Azure AD, non è necessario archiviare i token nel codice e rischiare potenziali vulnerabilità della sicurezza. Quando possibile, è consigliabile usare Azure AD con le applicazioni del bus di servizio di Azure. 

## <a name="shared-access-signature"></a>Firma di accesso condiviso
L'[autenticazione della firma di accesso condiviso](service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client possono quindi ottenere l'accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi del bus di servizio. La chiave si applica a tutte le entità di messaggistica nello spazio dei nomi. È anche possibile configurare le chiavi nelle code e negli argomenti del bus di servizio. SAS è anche supportato in [Inoltro di Azure](../azure-relay/relay-authentication-and-authorization.md).

Per usare la firma di accesso condiviso, è possibile configurare un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in uno spazio dei nomi, una coda o un argomento. Questa regola include gli elementi seguenti:

* *KeyName*: identifica la regola.
* *PrimaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *SecondaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *Rights*: rappresenta la raccolta di diritti **Listen**, **Send** o **Manage** concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le entità in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino a 12 regole di autorizzazione. Per impostazione predefinita, un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con tutti i diritti viene configurato per ogni spazio dei nomi quando ne viene eseguito il provisioning per la prima volta.

Per accedere a un'entità, è necessario un token di firma di accesso condiviso generato usando un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Nella firma di accesso condiviso è incluso il supporto per un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

> [!IMPORTANT]
> Se si usa Azure Active Directory controllo di accesso (noto anche come servizio di controllo di accesso o ACS) con il bus di servizio, si noti che il supporto per questo metodo è ora limitato ed è necessario [eseguire la migrazione dell'applicazione per usare la firma di](service-bus-migrate-acs-sas.md) accesso condiviso o usare l'autenticazione OAuth 2,0 con Azure ad (scelta consigliata). Per ulteriori informazioni sulla deprecazione di ACS, vedere [questo post di Blog](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'autenticazione con Azure AD, vedere gli articoli seguenti:

- [Autenticazione con identità gestite](service-bus-managed-service-identity.md)
- [Autenticazione da un'applicazione](authenticate-application.md)

Per ulteriori informazioni sull'autenticazione con SAS, vedere gli articoli seguenti:

- [Autenticazione con SAS](service-bus-sas.md)