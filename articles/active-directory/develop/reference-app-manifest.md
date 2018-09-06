---
title: Informazioni sul manifesto dell'app Azure Active Directory | Microsoft Docs
description: Descrizione dettagliata del manifesto dell'app Azure Active Directory, che rappresenta una configurazione dell'identità dell'applicazione in un tenant di Azure AD e che viene usato per facilitare l'autorizzazione OAuth, per l'esperienza di consenso e altro ancora.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127021"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto dell'app Azure Active Directory

Le app che si integrano con Azure Active Directory (Azure AD) devono essere registrate con un tenant di Azure AD. È possibile configurare l'app nel [portale di Azure](https://portal.azure.com) selezionando **Azure Active Directory**, scegliendo l'app da configurare e quindi selezionando **Manifesto**.

## <a name="manifest-reference"></a>Riferimento del manifesto

> [!NOTE]
> Se non è possibile visualizzare le descrizioni, ingrandire la finestra del browser o scorrere per visualizzare le descrizioni.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Chiave  | Tipo di valore | Valore di esempio | Descrizione  |
|---------|---------|---------|---------|
| `appID` | Stringa dell'identificatore | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Specifica l'identificatore univoco per l'app assegnato a un'app da Azure AD. |
| `appRoles` | Tipo di matrice | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Specifica la raccolta di ruoli che un'app può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio. |
| `availableToOtherTenants`| boolean | `true` | Se questo valore è impostato su true, l'app è disponibile per altri tenant. Se è impostato su false, l'app è disponibile solo per il tenant in cui è registrata. Per altre informazioni, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | stringa | `MyRegisteredApp` | Nome visualizzato dell'app. |
| `errorURL` | stringa | `http://MyRegisteredAppError` | URL per gli errori riscontrati in un'app. |
| `groupMembershipClaims` | stringa | `1` | Maschera di bit che configura l'attestazione `groups` rilasciata in un token di accesso utente oppure OAuth 2.0 previsto dall'app. I valori della maschera di bit sono:<br>0: Nessuno<br>1: Gruppi di sicurezza e ruoli di Azure AD<br>2: Riservato<br>4: Riservato<br>Se si imposta la maschera di bit su 7, vengono restituiti tutti i gruppi di sicurezza, i gruppi di distribuzione e i ruoli della directory di Azure AD a cui appartiene l'utente connesso. |
| `optionalClaims` | stringa | `null` | Attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica app. Per altre informazioni, vedere l'articolo relativo alle [attestazioni facoltative](active-directory-optional-claims.md). |
| `acceptMappedClaims` | boolean | `true` | Se questo valore è impostato su `true`, consente all'app di usare il mapping di attestazioni senza specificare una chiave di firma personalizzata. |
| `homepage` | stringa | `http://MyRegisteredApp` | Specifica l'URL della home page dell'app. |
| `informationalUrls` | stringa | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Specifica i collegamenti alle condizioni d'uso del servizio e all'informativa sulla privacy dell'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy vengono presentate agli utenti tramite l'esperienza di consenso dell'utente Per altre informazioni, vedere l'articolo [Condizioni per l'utilizzo del servizio e informativa sulla privacy per le app di Azure Active Directory registrate](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Matrice di stringhe | `http://MyRegistererdApp` | URI definiti dall'utente che identificano in modo univoco un'app Web entro il rispettivo tenant di Azure AD oppure entro un dominio personalizzato verificato se l'app è multi-tenant. |
| `keyCredentials` | Tipo di matrice | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Contiene riferimenti a credenziali assegnate dall'app, segreti condivisi basati su stringa e certificati X.509. Queste credenziali sono utilizzate in fase di richiesta dei token di accesso (quando l'app funge da client, anziché da risorsa). |
| `knownClientApplications` | Tipo di matrice | `[GUID]` | Viene usato per unire il consenso se una soluzione contiene due parti, un'app client e un'app API Web personalizzata. Se si immette l'appID dell'app client in questo valore, l'utente deve fornire il consenso solo una volta per l'app client. Azure AD sa che il consenso al client implica il consenso all'API Web ed effettua automaticamente il provisioning delle entità servizio per il client e l'API Web contemporaneamente. Sia il client sia l'app dell'API Web devono essere registrati nello stesso tenant. |
| `logoutUrl` | stringa | `http://MyRegisteredAppLogout` | URL per disconnettersi dall'app. |
| `oauth2AllowImplicitFlow` | boolean | `false` | Specifica se questa app Web può richiedere token di flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola. |
| `oauth2AllowUrlPathMatching` | boolean | `false` | Specifica se, come parte delle richieste di token OAuth 2.0, Azure AD consentirà la verifica della corrispondenza tra percorsi dell'URI di reindirizzamento rispetto al valore replyUrls dell'app. Il valore predefinito è false. |
| `oauth2Permissions` | Tipo di matrice | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Specifica la raccolta di ambiti di autorizzazione OAuth 2.0 che l'app dell'API Web (risorsa) espone alle app client. Questi ambiti di autorizzazione possono essere concessi alle app client durante il consenso. |
| `oauth2RequiredPostResponse` | boolean | `false` | Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consente richieste POST, anziché richieste GET. L'impostazione predefinita è false, che specifica che sono consentite solo richieste GET. |
| `objectId` | Stringa dell'identificatore | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Identificatore univoco per l'app nella directory. Questo ID non è l'identificatore usato per identificare l'app nelle transazioni di protocollo. Viene usato per fare riferimento all'oggetto nelle query di directory. |
| `parentalControlSettings` | stringa | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` specifica i paesi in cui l'app è bloccata per i minori.<br>`legalAgeGroupRule` specifica la regola gruppo relativa all'età legale che si applica agli utenti dell'app. Può essere impostata su `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` o `BlockMinors`.  |
| `passwordCredentials` | Tipo di matrice | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Vedere la descrizione per la proprietà `keyCredentials`. |
| `publicClient` | boolean | `false` | Specifica se un'app è un client pubblico, ad esempio un'app installata in esecuzione in un dispositivo mobile. Il valore predefinito è False. |
| `replyUrls` | Matrice di stringhe | `"http://localhost"` | Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. |
| `requiredResourceAccess` | Tipo di matrice | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | Con il consenso dinamico, `requiredResourceAccess` attiva l'esperienza di consenso dell'amministratore e l'esperienza di consenso dell'utente per gli utenti che usano il consenso statico. Tuttavia, ciò non attiva l'esperienza di consenso dell'utente per i casi generici.<br>`resourceAppId` è l'identificatore univoco per la risorsa a cui l'app richiede l'accesso. Questo valore deve essere uguale all'appId dichiarato nell'app della risorsa di destinazione.<br>`resourceAccess` è una matrice che contiene l'elenco dei ruoli delle app e degli ambiti delle autorizzazioni OAuth 2.0 che l'app richiede dalla risorsa specificata. Contiene i valori di tipo `id` e `type` per le risorse specificate. |
| `samlMetadataUrl` | stringa | `http://MyRegisteredAppSAMLMetadata` | URL dei metadati SAML per l'app. |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'app, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).
* Per le definizioni di alcuni concetti di Azure Active Directory (AD) fondamentali per gli sviluppatori, vedere il [Glossario per gli sviluppatori di Azure Active Directory](developer-glossary.md).

Usare la sezione dei commenti seguente per offrire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

