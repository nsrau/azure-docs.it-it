---
title: Account della piattaforma Microsoft Identity & profili tenant in Android | Azure
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
ms.openlocfilehash: a0f0f3be1647c820591923a094ef7fce86ab9672
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699445"
---
# <a name="accounts--tenant-profiles-android"></a>Account e profili tenant (Android)

In questo articolo viene fornita una panoramica dell'`account` della piattaforma di identità Microsoft.

L'API Microsoft Authentication Library (MSAL) sostituisce il termine *utente* con il termine *account*. Un motivo è che un utente (Human o software Agent) può avere o può usare più account. These accounts may be in the user's own organization, and/or in other organizations that the user is a member of.

An account in the Microsoft identity platform consists of:

- Identificatore univoco.  
- One or more credentials used to demonstrate ownership/control of the account.
- One or more profiles consisting of attributes such as:
  - Picture, Given Name, Family Name, Title, Office Location
- An account has a source of authority or system of record. This is the system where the account is created and where the credentials associated with that account are stored. In multi-tenant systems like the Microsoft identity platform, the system of record is the `tenant` where the account was created. This tenant is also referred as the `home tenant`.
- Accounts in the Microsoft identity platform have the following systems of record:
  - Azure Active Directory, including Azure Active Directory B2C.
  - Microsoft account (Live).
- Accounts from systems of record outside of the Microsoft identity platform are represented within the Microsoft identity platform including:
  - identities from connected on-premises directories (Windows Server Active Directory)
  - external identities from LinkedIn, GitHub, and so on.
  In these cases, an account has both an origin system of record and a system of record within the Microsoft identity platform.
- The Microsoft identity platform allows one account to be used to access resources belonging to multiple organizations (Azure Active Directory tenants).
  - To record that an account from one system of record (AAD Tenant A) has access to a resource in another system of record (AAD Tenant B), the account must be represented in the tenant where the resource is defined. This is done by creating a local record of the account from system A in system B.
  - This local record, that is the representation of the account, is bound to the original account.
  - MSAL exposes this local record as a `Tenant Profile`.
  - Tenant Profile can have different attributes that are appropriate to the local context, such as Job Title, Office Location, Contact Information, etc.
- Because an account may be present in one or more tenants, an account may have more than one profile.

> [!NOTE]
> MSAL treats the Microsoft account system (Live, MSA) as another tenant within the Microsoft identity platform. The tenant id of the Microsoft account tenant is: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Account overview diagram

![Account Overview Diagram](./media/accounts-overview/accounts-overview.png)

Nel diagramma precedente:

- The account `bob@contoso.com` is created in the on-premises Windows Server Active Directory (origin on-premises system of record).
- The account `tom@live.com` is created in the Microsoft account tenant.
- `bob@contoso.com` has access to at least one resource in the following Azure Active Directory tenants:
  - contoso.com (cloud system of record - linked to on-premises system of record)
  - fabrikam.com
  - woodgrovebank.com
  - A tenant profile for `bob@contoso.com` exists in each of these tenants.
- `tom@live.com` has access to resources in the following Microsoft tenants:
  - contoso.com
  - fabrikam.com
  - A tenant profile for `tom@live.com` exists in each of these tenants.
- Information about Tom and Bob in other tenants may differ from that in the system of record. They may differ by attributes such as Job title, Office Location, and so on. They may be members of groups and/or roles within each organization (Azure Active Directory Tenant). We refer to this information as bob@contoso.com tenant profile.

In the diagram, bob@contoso.com and tom@live.com have access to resources in different Azure Active Directory tenants. For more information, see [Add Azure Active Directory B2B collaboration users in the Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Accounts and single sign-on (SSO)

The MSAL token cache stores a *single refresh token* per account. That refresh token can be used to silently request access tokens from multiple Microsoft identity platform tenants. When a broker is installed on a device, the account is managed by the broker, and device-wide single sign-on becomes possible.

> [!IMPORTANT]
> Business to Consumer (B2C) account and refresh token behavior differs from the rest of the Microsoft identity platform. For more information, see [B2C Policies & Accounts](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Account identifiers

The MSAL account ID isn't an account object ID. It isn't meant to be parsed and/or relied upon to convey anything other than uniqueness within the Microsoft identity platform.

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
- Profilo

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
