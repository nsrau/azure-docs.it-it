---
title: Account della piattaforma Microsoft Identity e profili tenant (Android) | Azure
description: Panoramica degli account della piattaforma di identità Microsoft per Android
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7beab6759524037f86c83429644c1bb1fffe4d07
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679841"
---
# <a name="accounts--tenant-profiles-android"></a>Account e profili tenant (Android)

In questo articolo viene fornita una panoramica dell'`account` della piattaforma di identità Microsoft.

L'API Microsoft Authentication Library (MSAL) sostituisce il termine *utente* con il termine *account*. Un motivo è che un utente (Human o software Agent) può avere o può usare più account. Questi account possono essere nell'organizzazione dell'utente e/o in altre organizzazioni di cui l'utente è membro.

Un account nella piattaforma di identità Microsoft è costituito da:

  - Identificatore univoco.
  - Una o più credenziali utilizzate per dimostrare la proprietà e il controllo dell'account.
  - Uno o più profili costituiti da attributi come:
    - Immagine, nome, nome della famiglia, titolo, posizione dell'ufficio
- Un account ha un'origine dell'autorità o del sistema di registrazione. Si tratta del sistema in cui viene creato l'account e in cui vengono archiviate le credenziali associate a tale account. Nei sistemi multi-tenant come la piattaforma di identità Microsoft, il sistema di registrazione è il `tenant` in cui è stato creato l'account. Questo tenant viene definito anche `home tenant`.
- Gli account nella piattaforma Microsoft Identity includono i seguenti sistemi di record:
  - Azure Active Directory, incluso Azure Active Directory B2C.
  - Account Microsoft (Live).
- Gli account dei sistemi di record al di fuori della piattaforma di identità Microsoft sono rappresentati nella piattaforma di identità Microsoft, tra cui:
  - identità da directory locali connesse (Windows Server Active Directory)
  - identità esterne da LinkedIn, GitHub e così via.
  In questi casi, un account è costituito da un sistema di origine di record e da un sistema di record nella piattaforma di identità Microsoft.
- La piattaforma di identità Microsoft consente di usare un account per accedere alle risorse appartenenti a più organizzazioni (Azure Active Directory tenant).
  - Per registrare che un account da un sistema di record (tenant di AAD A) ha accesso a una risorsa in un altro sistema di record (tenant di AAD B), l'account deve essere rappresentato nel tenant in cui è definita la risorsa. Questa operazione viene eseguita creando un record locale dell'account dal sistema a nel sistema B.
  - Questo record locale, ovvero la rappresentazione dell'account, è associato all'account originale.
  - MSAL espone questo record locale come `Tenant Profile`.
  - Il profilo tenant può avere attributi diversi appropriati per il contesto locale, ad esempio il titolo del processo, la posizione dell'ufficio, le informazioni di contatto e così via.
 
- Poiché un account può essere presente in uno o più tenant, un account può avere più di un profilo.

> [!NOTE]
> MSAL considera il sistema account Microsoft (Live, MSA) come un altro tenant all'interno della piattaforma di identità Microsoft. L'ID tenant del tenant account Microsoft è: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagramma della panoramica dell'account

![Diagramma della panoramica dell'account](./media/accounts-overview/accounts-overview.png)

Nel diagramma precedente:

- L'account `bob@contoso.com` viene creato in Windows Server Active Directory locale (sistema locale di origine del record).
- L'account `tom@live.com` viene creato nel tenant di account Microsoft.
- `bob@contoso.com` ha accesso ad almeno una risorsa nei seguenti tenant di Azure Active Directory:
  - contoso.com (sistema cloud di record collegato al sistema locale di record)
  - fabrikam.com
  - woodgrovebank.com
  - Un profilo tenant per `bob@contoso.com` esiste in ognuno di questi tenant.
- `tom@live.com` ha accesso alle risorse nei tenant Microsoft seguenti:
  - contoso.com
  - fabrikam.com
  - Un profilo tenant per `tom@live.com` esiste in ognuno di questi tenant.
- Le informazioni su Tom e Bob in altri tenant possono essere diverse da quelle del sistema di registrazione. Possono variare in base ad attributi quali titolo del processo, posizione dell'ufficio e così via. Possono essere membri di gruppi e/o ruoli all'interno di ogni organizzazione (Azure Active Directory tenant). Si fa riferimento a queste informazioni come bob@contoso.com profilo tenant.

Nel diagramma bob@contoso.com e tom@live.com hanno accesso alle risorse in tenant Azure Active Directory diversi. Per ulteriori informazioni, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Account e Single Sign-On (SSO)

La cache del token MSAL archivia un *singolo token di aggiornamento* per ogni account. Il token di aggiornamento può essere usato per richiedere automaticamente i token di accesso da più tenant della piattaforma Microsoft Identity. Quando un broker viene installato in un dispositivo, l'account viene gestito dal broker e il Single Sign-On a livello di dispositivo diventa possibile.

> [!IMPORTANT]
> L'account business to consumer (B2C) e il comportamento del token di aggiornamento differiscono dal resto della piattaforma Microsoft Identity. Per altre informazioni, vedere [criteri B2C & account](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identificatori di account

L'ID dell'account MSAL non è un ID di oggetto account. Non è concepito per essere analizzato e/o basato su per fornire un valore diverso dall'univocità all'interno della piattaforma di identità Microsoft.

Per la compatibilità con la libreria Autenticazione di Azure AD (ADAL) e per semplificare la migrazione da ADAL a MSAL, MSAL può cercare gli account usando qualsiasi identificatore valido per l'account disponibile nella cache MSAL.  Ad esempio, il codice seguente recupera sempre lo stesso oggetto account per tom@live.com perché ognuno degli identificatori è valido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Accesso alle attestazioni relative a un account

Oltre a richiedere un token di accesso, MSAL richiede sempre un token ID da ogni tenant. Questa operazione viene eseguita richiedendo sempre gli ambiti seguenti:

- openid
- profile

Il token ID contiene un elenco di attestazioni. `Claims` sono le coppie nome/valore dell'account e vengono usate per eseguire la richiesta.

Come indicato in precedenza, ogni tenant in cui è presente un account può archiviare informazioni diverse sull'account, inclusi, a titolo esemplificativo, attributi come il titolo del processo, la posizione dell'ufficio e così via.

Anche se un account può essere un membro o un guest in più organizzazioni, MSAL non esegue una query su un servizio per ottenere un elenco dei tenant di cui l'account è membro. MSAL compila invece un elenco di tenant in cui è presente l'account, in seguito a richieste di token effettuate.

Le attestazioni esposte nell'oggetto account sono sempre le attestazioni del/{Authority}' tenant principale ' per un account. Se tale account non è stato usato per richiedere un token per il tenant principale, MSAL non può fornire attestazioni tramite l'oggetto account.  Ad esempio:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Per visualizzare un elenco di attestazioni disponibili nell'oggetto account, fare riferimento alle [attestazioni in un id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Per includere altre attestazioni nel id_token, vedere la documentazione facoltativa sulle attestazioni in [procedura: fornire attestazioni facoltative all'app Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Accesso alle attestazioni del profilo tenant

Per accedere alle attestazioni relative a un account così come vengono visualizzate in altri tenant, è prima di tutto necessario eseguire il cast dell'oggetto account a `IMultiTenantAccount`. Tutti gli account possono essere multi-tenant, ma il numero di profili tenant disponibili tramite MSAL è basato sui tenant per i quali sono stati richiesti token usando l'account corrente.  Ad esempio:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Criteri B2C & account

I token di aggiornamento per un account non vengono condivisi tra i criteri B2C. Di conseguenza, non è possibile Single Sign-On l'uso di token. Ciò non significa che Single Sign-On non è possibile. Ciò significa che Single Sign-On necessario utilizzare un'esperienza interattiva in cui è disponibile un cookie per abilitare l'Single Sign-On.

Questo significa anche che, nel caso di MSAL, se si acquisiscono token usando criteri B2C diversi, questi vengono considerati come account distinti, ognuno con il proprio identificatore. Se si vuole usare un account per richiedere un token usando `acquireTokenSilent`, è necessario selezionare l'account dall'elenco di account che corrisponde al criterio usato con la richiesta di token. Ad esempio:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
