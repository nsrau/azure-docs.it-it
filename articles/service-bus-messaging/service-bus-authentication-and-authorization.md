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
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 5ed7558cfff9991734e909e06e8bac9181131381
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="service-bus-authentication-and-authorization"></a>Autenticazione e autorizzazione del bus di servizio

Le applicazioni possono eseguire l'autenticazione al bus di servizio di Microsoft Azure tramite l'autenticazione della firma di accesso condiviso o tramite il servizio di controllo di accesso di Microsoft Azure Active Directory (anche noto come Servizio di controllo di accesso o ACS). L'autenticazione della firma di accesso condiviso consente alle applicazioni di eseguire l'autenticazione al bus di servizio tramite una chiave di accesso configurata nello spazio dei nomi o nell'entità a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso che i client possono usare per eseguire l'autenticazione al bus di servizio.

> [!IMPORTANT]
> La firma di accesso condiviso offre uno schema di autenticazione per il bus di servizio semplice, flessibile e di facile utilizzo. Le applicazioni possono usare la firma di accesso condiviso in scenari in cui non è necessario gestire la nozione di "utente" autorizzato. 

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

## <a name="acs-authentication"></a>Autenticazione tramite il servizio di controllo di accesso (ACS)
L'autenticazione del bus di servizio tramite ACS viene gestita con uno spazio dei nomi ACS "-sb" specifico. Se si vuole che venga creato uno spazio dei nomi ACS specifico per uno spazio dei nomi del bus di servizio, non è possibile creare lo spazio dei nomi del bus di servizio usando il portale di Azure classico, ma è necessario usare il cmdlet di PowerShell [New-AzureSBNamespace](/powershell/module/azure/new-azuresbnamespace?view=azuresmps-3.7.0) . Ad esempio:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Per evitare di creare uno spazio dei nomi ACS, eseguire il comando seguente:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Se ad esempio si crea uno spazio dei nomi del bus di servizio denominato **contoso.servicebus.windows.net**, viene generato automaticamente uno spazio dei nomi ACS denominato **contoso-sb.accesscontrol.windows.net** corrispondente. Per tutti gli spazi dei nomi creati prima di agosto 2014, è stato creato il relativo spazio dei nomi ACS.

Per impostazione predefinita, in questo spazio dei nomi ACS viene eseguito il provisioning di un "proprietario" dell'identità del servizio predefinito con tutti i relativi diritti. Per ottenere un controllo con granularità fine su qualsiasi entità del bus di servizio tramite ACS, configurare le relazioni di trust appropriate. È anche possibile configurare identità del servizio aggiuntive per la gestione dell'accesso alle entità del bus di servizio.

Per accedere a un'entità, il client richiede un token in formato Token Web semplice (SWT) da ACS con le attestazioni appropriate, presentando le relative credenziali. Il token SWT deve quindi essere inviato come parte della richiesta al bus di servizio per abilitare l'autorizzazione del client per l'accesso all'entità.

Il supporto per l'autenticazione tramite ACS per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Questa autenticazione include il supporto per un oggetto [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione ACS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su SAS, vedere [Autenticazione del bus di servizio con firme di accesso condiviso](service-bus-sas.md).

Per informazioni corrispondenti sull'autenticazione in Inoltro di Azure, vedere [Autenticazione e autorizzazione di Inoltro di Azure](../service-bus-relay/relay-authentication-and-authorization.md). 


