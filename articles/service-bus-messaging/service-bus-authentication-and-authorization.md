---
title: Autenticazione e autorizzazione del bus di servizio di Azure | Documentazione Microsoft
description: Autenticare le app sul bus di servizio usando l'autenticazione con firma di accesso condiviso (SAS).
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: b8b5887f2003dd793ae7a50f066b893f685002a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticazione e autorizzazione del bus di servizio

Le applicazioni accedono alle risorse del bus di servizio di Azure usando l'autenticazione con token per la firma di accesso condiviso. Con la firma di accesso condiviso, le applicazioni presentano un token al bus di servizio che è stato firmato con una chiave simmetrica nota sia all'emittente del token che al bus di servizio, ovvero "condivisa"; tale chiave è direttamente associata a una regola che concede diritti di accesso specifici, ad esempio l'autorizzazione alla ricezione/all'ascolto o all'invio di messaggi. Le regole relative alla firma di accesso condiviso sono configurate nello spazio dei nomi o direttamente nell'entità, ad esempio una coda o un argomento, consentendo il controllo di accesso granulare.

I token della firma di accesso condiviso possono essere generati direttamente da un client del bus di servizio o da un token intermedio che emette endpoint con cui il client interagisce. Ad esempio, un sistema può richiedere al client di chiamare un endpoint del servizio Web protetto mediante autorizzazione di Active Directory per dimostrare i diritti di accesso di identità e di sistema; il servizio Web restituisce quindi il token del bus di servizio appropriato. Questo token di firma di accesso condiviso può essere facilmente generato usando il provider di token del bus di servizio incluso nel SDK di Azure. 

> [!IMPORTANT]
> Se si usa il controllo di accesso di Azure Active Directory, anche noto come Servizio di controllo di accesso o ACS, con il bus di servizio, si noti che il supporto per questo metodo è ora limitato ed è necessario eseguire la migrazione dell'applicazione per l'uso di SAS. Per altre informazioni, vedere [questo post di blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e [questo articolo](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Autenticazione della firma di accesso condiviso

L'[autenticazione della firma di accesso condiviso](service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client possono quindi ottenere l'accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi del bus di servizio. La chiave si applica a tutte le entità di messaggistica nello spazio dei nomi. È anche possibile configurare le chiavi nelle code e negli argomenti del bus di servizio. SAS è anche supportato in [Inoltro di Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Per usare la firma di accesso condiviso, è possibile configurare un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in uno spazio dei nomi, una coda o un argomento. Questa regola include gli elementi seguenti:

* *KeyName*: identifica la regola.
* *PrimaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *SecondaryKey*: è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *Rights*: rappresenta la raccolta di diritti **Listen**, **Send** o **Manage** concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le entità in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino a 12 regole di autorizzazione. Per impostazione predefinita, un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con tutti i diritti viene configurato per ogni spazio dei nomi quando ne viene eseguito il provisioning per la prima volta.

Per accedere a un'entità, è necessario un token di firma di accesso condiviso generato usando un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Nella firma di accesso condiviso è incluso il supporto per un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su SAS, vedere [Autenticazione del bus di servizio con firme di accesso condiviso](service-bus-sas.md).
- Come [eseguire la migrazione dall'autorizzazione del Controllo di accesso di Azure Active Directory all'autorizzazione con firma di accesso condiviso](service-bus-migrate-acs-sas.md).
- [Modifiche agli spazi dei nomi basati su ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Per informazioni corrispondenti sull'autenticazione in Inoltro di Azure, vedere [Autenticazione e autorizzazione di Inoltro di Azure](../service-bus-relay/relay-authentication-and-authorization.md). 

