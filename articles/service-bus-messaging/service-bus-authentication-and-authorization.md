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
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>Autenticazione e autorizzazione del bus di servizio

Le applicazioni possono eseguire l'autenticazione al bus di servizio di Azure usando l'autenticazione con firma di accesso condiviso (SAS). L'autenticazione della firma di accesso condiviso consente alle applicazioni di eseguire l'autenticazione al bus di servizio tramite una chiave di accesso configurata nello spazio dei nomi o nell'entità a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso che i client possono usare per eseguire l'autenticazione al bus di servizio.

> [!IMPORTANT]
> È consigliabile utilizzare SAS invece di Azure AD Access Control (anche noto come Servizio di controllo di accesso o ACS), perché quest'ultimo verrà deprecato. La firma di accesso condiviso offre uno schema di autenticazione per il bus di servizio semplice, flessibile e di facile utilizzo. Le applicazioni possono usare la firma di accesso condiviso in scenari in cui non è necessario gestire la nozione di "utente" autorizzato. Per altre informazioni, vedere [questo post di blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Autenticazione della firma di accesso condiviso

[autenticazione della firma di accesso condiviso](service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client possono quindi ottenere l'accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi del bus di servizio. La chiave si applica a tutte le entità di messaggistica nello spazio dei nomi. È anche possibile configurare le chiavi nelle code e negli argomenti del bus di servizio. SAS è anche supportato in [Inoltro di Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Per usare la firma di accesso condiviso, è possibile configurare un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in uno spazio dei nomi, una coda o un argomento. Questa regola include gli elementi seguenti:

* *KeyName* che identifica la regola.
* *PrimaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *SecondaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *Rights* che rappresenta la raccolta di diritti Listen, Send o Manage concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le entità in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino a 12 regole di autorizzazione. Per impostazione predefinita, un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con tutti i diritti viene configurato per ogni spazio dei nomi quando ne viene eseguito il provisioning per la prima volta.

Per accedere a un'entità, è necessario un token di firma di accesso condiviso generato usando un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Nella firma di accesso condiviso è incluso il supporto per un oggetto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su SAS, vedere [Autenticazione del bus di servizio con firme di accesso condiviso](service-bus-sas.md).
- [Changes To ACS Enabled namespaces](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) (modifiche agli spazi dei nomi basati su ACS).
- Per informazioni corrispondenti sull'autenticazione in Inoltro di Azure, vedere [Autenticazione e autorizzazione di Inoltro di Azure](../service-bus-relay/relay-authentication-and-authorization.md). 


