---
title: Autenticazione e autorizzazione di Inoltro di Azure | Documentazione Microsoft
description: Panoramica dell'autenticazione con firma di accesso condiviso (SAS) in Inoltro di Azure
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 86a9cf2c1106180ba5c8c65849042784bfd2afcd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticazione e autorizzazione di Inoltro di Azure

Le applicazioni possono eseguire l'autenticazione a Inoltro di Azure usando l'autenticazione con firma di accesso condiviso (SAS). L'autenticazione con firma di accesso condiviso consente alle applicazioni di eseguire l'autenticazione al servizio di inoltro di Azure con una chiave di accesso configurata nello spazio dei nomi dell'inoltro. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso che i client possono usare per eseguire l'autenticazione al servizio di inoltro.

## <a name="shared-access-signature-authentication"></a>Autenticazione della firma di accesso condiviso

L'[autenticazione SAS](../service-bus-messaging/service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del servizio Inoltro di Azure con diritti specifici. Nel bus di servizio l'autenticazione SAS implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa. I client possono quindi ottenere l'accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi di Inoltro. A differenza della messaggistica del bus di servizio, le [connessioni ibride di inoltro](relay-hybrid-connections-protocol.md) supportano mittenti non autorizzati o anonimi. È possibile abilitare l'accesso anonimo per l'entità quando la si crea, come illustrato nella schermata riportata di seguito dal portale:

![][0]

Per usare SAS, è possibile configurare un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) in uno spazio dei nomi costituito dai parametri seguenti:

* *KeyName* che identifica la regola.
* *PrimaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *SecondaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.
* *Rights* che rappresenta la raccolta di diritti Listen, Send o Manage concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le connessioni di inoltro in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi di Inoltro è possibile configurare fino a 12 regole di autorizzazione di questo tipo. Per impostazione predefinita, un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con tutti i diritti viene configurato per ogni spazio dei nomi quando ne viene eseguito il provisioning per la prima volta.

Per accedere a un'entità, è necessario un token di firma di accesso condiviso generato usando un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per Inoltro di Azure è incluso in Azure .NET SDK 2.0 e versioni successive. Nella firma di accesso condiviso è incluso il supporto per un oggetto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su SAS, vedere [Autenticazione del bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md).
- Per informazioni dettagliate sulla funzionalità delle connessioni ibride, vedere [Protocollo per le connessioni ibride di Inoltro di Azure](relay-hybrid-connections-protocol.md).
- Per informazioni corrispondenti sull'autenticazione della messaggistica del bus di servizio, vedere [Autenticazione e autorizzazione del bus di servizio](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png