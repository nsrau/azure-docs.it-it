---
title: Informazioni sul manifesto dell'applicazione Azure Active Directory | Microsoft Docs
description: "Descrizione dettagliata del manifesto dell'applicazione Azure Active Directory, che rappresenta una configurazione dell'identità dell'applicazione in un tenant di Azure AD e che viene usato per facilitare l'autorizzazione OAuth, per l'esperienza di consenso e altro ancora."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Manifesto dell'applicazione di Azure Active Directory
Le app che si integrano con Azure AD devono essere registrate con un tenant di Azure AD. Questa app può essere configurata con il manifesto dell'app (nel pannello di Azure AD) nel [portale di Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Riferimento del manifesto

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Chiave  |Tipo di valore |Valore di esempio  |DESCRIZIONE  |
|---------|---------|---------|---------|
|appID     |  Stringa dell'identificatore       |""|  Identificatore univoco per l'applicazione che viene assegnato a un'app da Azure AD.|
|appRoles     |    Tipo di matrice     |[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]|Raccolta di ruoli che un'applicazione può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio.|
|availableToOtherTenants|boolean|true|Se questo valore è impostato su true, l'applicazione è disponibile per altri tenant.  Se è impostato su false, l'app è disponibile solo per il tenant in cui è registrata.  Per altre informazioni, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |stringa         |MyRegisteredApp         |Nome visualizzato dell'applicazione. |
|errorURL     |stringa         |http:<i></i>//MyRegisteredAppError         |URL per gli errori riscontrati in un'applicazione. |
|groupMembershipClaims     |    stringa     |    1     |   Maschera di bit che configura l'attestazione "groups" rilasciata in un token di accesso utente oppure OAuth 2.0 previsto dall'applicazione. I valori della maschera di bit sono: 0: Nessuno. 1: Gruppi di sicurezza e ruoli di Azure AD. 2: Riservato. 4: Riservato. Se si imposta la maschera di bit su 7, vengono restituiti tutti i gruppi di sicurezza, i gruppi di distribuzione e i ruoli della directory di Azure AD a cui appartiene l'utente connesso.      |
|optionalClaims     |  stringa       |     Null    |    Attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica app.     |
|acceptMappedClaims    |      boolean   | true        |    Se questo valore è impostato su true, consente a un'applicazione di usare il mapping di attestazioni senza specificare una chiave di firma personalizzata.|
|homepage     |  stringa       |http:<i></i>//MyRegistererdApp         |    URL della home page dell'applicazione.     |
|identifierUris     |  Matrice di stringhe       | http:<i></i>//MyRegistererdApp        |   URI definiti dall'utente che identificano in modo univoco un'applicazione Web entro il rispettivo tenant di Azure AD oppure entro un dominio personalizzato verificato se l'applicazione è multi-tenant.      |
|keyCredentials     |   Tipo di matrice      |   [{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]      |   Questa proprietà contiene riferimenti a credenziali assegnate dall'applicazione, segreti condivisi basati su stringa e certificati X.509.  Queste credenziali entrano in gioco in fase di richiesta dei token di accesso (quando l'app funge da client, anziché da risorsa).     |
|knownClientApplications     |     Tipo di matrice    |    [guid]     |     Il valore viene usato per unire il consenso se una soluzione contiene due parti, un'applicazione client e un'applicazione API Web personalizzata. Se si immette l'appID dell'applicazione client in questo valore, l'utente deve fornire il consenso solo una volta per l'applicazione client. Azure AD sa che il consenso al client implica il consenso all'API Web ed effettua automaticamente il provisioning delle entità servizio per il client e l'API Web nello stesso momento (il client e l'applicazione API Web devono essere registrate nello stessa tenant).|
|logoutUrl     |   stringa      |     http:<i></i>//MyRegisteredAppLogout    |   URL di disconnessione dell'applicazione.      |
|oauth2AllowImplicitFlow     |   boolean      |  false       |       Specifica se questa applicazione Web può richiedere token di flusso implicito OAuth 2.0. Il valore predefinito è false. Viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola. |
|oauth2AllowUrlPathMatching     |   boolean      |  false       |   Specifica se, come parte delle richieste di token OAuth 2.0, Azure AD consentirà la verifica della corrispondenza tra percorsi dell'URI di reindirizzamento rispetto al valore replyUrls dell'applicazione. Il valore predefinito è false.      |
|oauth2Permissions     | Tipo di matrice         |      [{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]   |  Raccolta di ambiti di autorizzazione OAuth 2.0 che l'applicazione dell'API web (risorsa) espone alle applicazioni client. Questi ambiti di autorizzazione possono essere concessi alle applicazioni client durante il consenso. |
|oauth2RequiredPostResponse     | boolean        |    false     |      Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consente richieste POST, anziché richieste GET. L'impostazione predefinita è false, che specifica che sono consentite solo richieste GET.   
|objectId     | Stringa dell'identificatore        |     ""    |    Identificatore univoco per l'applicazione nella directory.  Non si tratta dell'identificatore usato per identificare l'app in una transazione di protocollo.  Viene usato per fare riferimento all'oggetto nelle query di directory.|
|passwordCredentials     | Tipo di matrice        |   [{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]      |    Vedere la descrizione per la proprietà keyCredentials.     |
|publicClient     |  boolean       |      false   | Specifica se un'applicazione è un client pubblico, ad esempio un'applicazione installata in esecuzione in un dispositivo mobile. Il valore predefinito è false.        |
|supportsConvergence     |  boolean       |   false      | Questa proprietà non deve essere modificata.  Accettare il valore predefinito.        |
|replyUrls     |  Matrice di stringhe       |   http:<i></i>//localhost     |  Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. |
|requiredResourceAccess     |     Tipo di matrice    |    [{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}]     |   Specifica le risorse a cui questa applicazione deve accedere e il set di ambiti di autorizzazioni OAuth e ruoli applicazione necessari per ogni risorsa. Questa preconfigurazione dell'accesso alle risorse necessarie definisce l'esperienza di consenso.|
|resourceAppId     |    Stringa dell'identificatore     |  ""      |   Identificatore univoco per la risorsa a cui l'applicazione richiede l'accesso. Questo valore deve essere uguale all'appId dichiarato nell'applicazione della risorsa di destinazione.     |
|resourceAccess     |  Tipo di matrice       | Vedere il valore di esempio della proprietà requiredResourceAccess.        |   Elenco dei ruoli delle app e degli ambiti delle autorizzazioni OAuth 2.0 che l'applicazione richiede dalla risorsa specificata (contiene i valori di tipo e ID delle risorse specificate)        |
|samlMetadataUrl|stringa|http:<i></i>//MyRegisteredAppSAMLMetadata|URL dei metadati SAML per l'applicazione.| 

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD][AAD-APP-OBJECTS].
* Per le definizioni di alcuni concetti di base per gli sviluppatori di Azure Active Directory (AD), vedere il [Glossario per gli sviluppatori di Azure Active Directory][AAD-DEVELOPER-GLOSSARY].

Usare la sezione dei commenti seguente per offrire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

