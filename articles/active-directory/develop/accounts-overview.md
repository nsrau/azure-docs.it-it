---
title: Account della piattaforma di identità Microsoft & profili tenant su Android Azure
description: Panoramica degli account della piattaforma di identità Microsoft per Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611868"
---
# <a name="accounts--tenant-profiles-android"></a>Account e profili tenant (Android)

Questo articolo fornisce una `account` panoramica di ciò che è un nella piattaforma di identità Microsoft.This article provides an overview of what is an in the Microsoft identity platform.

L'API Microsoft Authentication Library (MSAL) sostituisce il termine *utente* con il termine *account*. Uno dei motivi è che un utente (utente o agente software) può avere o può utilizzare più account. Questi account possono trovarsi nell'organizzazione dell'utente e/o in altre organizzazioni di cui l'utente è membro.

Un account nella piattaforma di identità Microsoft è costituito da:An account in the Microsoft identity platform consists of:

- Identificatore univoco.  
- Una o più credenziali utilizzate per dimostrare la proprietà/controllo dell'account.
- Uno o più profili costituiti da attributi quali:
  - Immagine, Nome dato, Nome famiglia, Titolo, Posizione ufficio
- Un account dispone di un'origine di autorità o di un sistema di record. Si tratta del sistema in cui viene creato l'account e in cui sono archiviate le credenziali associate a tale account. Nei sistemi multi-tenant come la piattaforma di identità Microsoft, il sistema di record è il `tenant` punto in cui è stato creato l'account. Questo tenant è anche `home tenant`denominato .
- Gli account nella piattaforma di identità Microsoft hanno i seguenti sistemi di record:
  - Azure Active Directory, incluso Azure Active Directory B2C.
  - Account Microsoft (Live).
- Gli account dei sistemi di record esterni alla piattaforma di identità Microsoft sono rappresentati all'interno della piattaforma di identità Microsoft, tra cui:
  - identità da directory locali connesse (Windows Server Active Directory)
  - identità esterne da LinkedIn, GitHub e così via.
  In questi casi, un account dispone sia di un sistema di origine di record e un sistema di record all'interno della piattaforma di identità Microsoft.In these cases, an account has both an origin system of record and a system of record within the Microsoft identity platform.
- La piattaforma di identità Microsoft consente di utilizzare un account per accedere alle risorse appartenenti a più organizzazioni (tenant di Azure Active Directory).
  - Per registrare che un account di un sistema di record (AAD Tenant A) ha accesso a una risorsa in un altro sistema di record (AAD Tenant B), l'account deve essere rappresentato nel tenant in cui è definita la risorsa. Questo viene fatto creando un record locale del conto dal sistema A nel sistema B.
  - Questo record locale, ovvero la rappresentazione dell'account, è associato all'account originale.
  - MSAL espone questo record `Tenant Profile`locale come file .
  - Profilo tenant può avere attributi diversi appropriati per il contesto locale, ad esempio Posizione professionale, Ubicazione dell'ufficio, Informazioni di contatto e così via.
- Poiché un account può essere presente in uno o più tenant, un account può avere più di un profilo.

> [!NOTE]
> MSAL considera il sistema di account Microsoft (Live, MSA) come un altro tenant all'interno della piattaforma di identità Microsoft. L'ID tenant del tenant dell'account Microsoft è:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagramma di panoramica dell'account

![Diagramma di panoramica dell'account](./media/accounts-overview/accounts-overview.svg)

Nel diagramma precedente:

- L'account `bob@contoso.com` viene creato in Active Directory di Windows Server locale (origin on-premise system of record).
- L'account `tom@live.com` viene creato nel tenant dell'account Microsoft.
- `bob@contoso.com`ha accesso ad almeno una risorsa nei tenant di Azure Active Directory seguenti:
  - contoso.com (sistema di record cloud - collegato al sistema di record locale)
  - fabrikam.com
  - woodgrovebank.com
  - Un profilo `bob@contoso.com` tenant per esiste in ognuno di questi tenant.
- `tom@live.com`ha accesso alle risorse nei seguenti tenant Microsoft:
  - contoso.com
  - fabrikam.com
  - Un profilo `tom@live.com` tenant per esiste in ognuno di questi tenant.
- Le informazioni su Tom e Bob in altri inquilini possono differire da quelle del sistema di record. Possono variare in base ad attributi quali titolo del lavoro, posizione dell'ufficio e così via. Possono essere membri di gruppi e/o ruoli all'interno di ogni organizzazione (tenant di Azure Active Directory). Queste informazioni vengono bob@contoso.com considerate profilo tenant.

Nel diagramma bob@contoso.com e tom@live.com avere accesso alle risorse in tenant di Azure Active Directory diversi. Per altre informazioni, vedere Aggiungere utenti di Collaborazione B2B di Azure nel portale di Azure.For more information, see [Add Azure Active Directory B2B collaboration users in the Azure portal.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)

## <a name="accounts-and-single-sign-on-sso"></a>Account e Single Sign-On (SSO)

La cache dei token MSAL archivia un *singolo token* di aggiornamento per account. Tale token di aggiornamento può essere utilizzato per richiedere automaticamente i token di accesso da più tenant della piattaforma di identità Microsoft.That refresh token can be used to silently request access tokens from multiple Microsoft identity platform tenants. Quando un broker è installato su un dispositivo, l'account viene gestito dal broker e l'accesso Single Sign-On a livello di dispositivo diventa possibile.

> [!IMPORTANT]
> Il comportamento dell'account Business to Consumer (B2C) e del token di aggiornamento è diverso dal resto della piattaforma di identità Microsoft. Per ulteriori informazioni, vedere [Criteri B2C & account](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identificatori dell'account

L'ID account MSAL non è un ID oggetto account. Non è pensato per essere analizzato e/o invocato per trasmettere qualcosa di diverso dall'unicità all'interno della piattaforma di identità Microsoft.

Per garantire la compatibilità con Azure AD Authentication Library (ADAL) e per semplificare la migrazione da ADAL a MSAL, MSAL può cercare gli account usando qualsiasi identificatore valido per l'account disponibile nella cache MSAL.  Ad esempio, il seguente recupererà tom@live.com sempre lo stesso oggetto account perché ognuno degli identificatori è valido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Accesso alle rivendicazioni relative a un account

Oltre a richiedere un token di accesso, MSAL richiede sempre un token ID da ogni tenant. Ciò si esegue questa operazione richiedendo sempre gli ambiti seguenti:It does this by always requesting the following scopes:

- openid
- Profilo

Il token ID contiene un elenco di attestazioni. `Claims`sono coppie nome/valore relative all'account e vengono utilizzate per effettuare la richiesta.

Come accennato in precedenza, ogni tenant in cui è presente un account può archiviare informazioni diverse sull'account, inclusi, a titolo enon il limitato, ad attributi quali: titolo professionale, sede dell'ufficio e così via.

Mentre un account può essere un membro o guest in più organizzazioni, MSAL non interroga un servizio per ottenere un elenco dei tenant di cui l'account è membro. Al contrario, MSAL crea un elenco di tenant in cui è presente l'account, in seguito alle richieste di token effettuate.

Le attestazioni esposte nell'oggetto account sono sempre le attestazioni dal 'tenant principale'/'authority' per un account. Se l'account non è stato utilizzato per richiedere un token per il tenant principale, MSAL non è in grado di fornire attestazioni tramite l'oggetto account.  Ad esempio:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Per visualizzare un elenco di attestazioni disponibili dall'oggetto account, fare riferimento a [attestazioni in un'id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Per includere attestazioni aggiuntive nella id_token, vedere la documentazione relativa alle attestazioni facoltative in [Procedura: fornire attestazioni facoltative per l'app Azure ADTo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) include additional claims in the id_token, refer to the optional claims documentation in How to: Provide optional claims to your Azure AD app

### <a name="access-tenant-profile-claims"></a>Accedere alle attestazioni del profilo tenant

Per accedere alle attestazioni relative a un account così come vengono `IMultiTenantAccount`visualizzate in altri tenant, è innanzitutto necessario eseguire il cast dell'oggetto account su . Tutti gli account possono essere multi-tenant, ma il numero di profili tenant disponibili tramite MSAL si basa sui tenant richiesti token dall'utilizzo dell'account corrente.  Ad esempio:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Criteri B2C & account

I token di aggiornamento per un account non vengono condivisi tra i criteri B2C. Di conseguenza, l'accesso Single Sign-On tramite i token non è possibile. Questo non significa che single sign-on non sia possibile. Significa che l'accesso Single Sign-On deve utilizzare un'esperienza interattiva in cui è disponibile un cookie per abilitare l'accesso Single Sign-On.

Ciò significa anche che nel caso di MSAL, se si acquisiscono token utilizzando criteri B2C diversi, questi vengono trattati come account separati, ognuno con il proprio identificatore. Se si desidera utilizzare un account `acquireTokenSilent`per richiedere un token utilizzando , è necessario selezionare l'account dall'elenco di account che corrisponde ai criteri in uso con la richiesta di token. Ad esempio:

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
