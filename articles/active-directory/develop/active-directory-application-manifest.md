---
title: Informazioni sul manifesto dell'applicazione Azure Active Directory | Documentazione Microsoft
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Manifesto dell'applicazione di Azure Active Directory
Le app che si integrano con Azure AD devono essere registrate con un tenant di Azure AD. Questa app può essere configurata utilizzando il manifesto dell'applicazione (sotto il pannello di Azure AD) nei [portale di Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Riferimento al manifesto

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Chiave  |Tipo di valore |Valore di esempio  |DESCRIZIONE  |
|---------|---------|---------|---------|
|appID     |  Stringa dell'identificatore       |""|  Identificatore univoco per l'applicazione che viene assegnato a un'app da Azure AD.|
|appRoles     |    Tipo di matrice     |[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"Utente"<br>&emsp;],<br>&emsp;"descrizione": "Lettura solo l'accesso a informazioni sul dispositivo",<br>&emsp;"displayName": "Sola lettura",<br>&emsp;"id": guid,<br>&emsp;"isEnabled": true,<br>&emsp;"value": "ReadOnly"<br>}]|La raccolta di ruoli che un'applicazione può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio.|
|AvailableToOtherTenants|boolean|true|Se questo valore è impostato su true, l'applicazione è disponibile per altri tenant.  Se impostato su false, l'app è solo disponibile per il tenant è registrato.  Per ulteriori informazioni, vedere: [come accedere a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |stringa         |MyRegisteredApp         |Il nome visualizzato per l'applicazione. |
|errorURL     |stringa         |http:<i></i>//MyRegisteredAppError         |L'URL per gli errori rilevati in un'applicazione. |
|GroupMembershipClaims     |    stringa     |    1     |   Maschera di bit che configura l'attestazione "groups" rilasciata in un utente o un token di accesso OAuth 2.0 che prevede l'applicazione. I valori della maschera di bit sono: 0: Nessuno, 1: gruppi di protezione e ruoli di Azure AD, 2: riservato e 4: riservato. Impostando la maschera di bit su 7 è possibile ottenere tutti i gruppi di sicurezza, i gruppi di distribuzione e i ruoli della directory Azure AD che l'utente connesso è un membro di.      |
|optionalClaims     |  stringa       |     Null    |    Le attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica applicazione.     |
|acceptMappedClaims    |      boolean   | true        |    Se questo valore è impostato su true, consente a un'applicazione può utilizzare le attestazioni mapping senza specificare una chiave di firma personalizzata.|
|Home Page     |  stringa       |http:<i></i>//MyRegistererdApp         |    L'URL alla pagina iniziale dell'applicazione.     |
|identifierUris     |  Matrice di stringhe       | http:<i></i>//MyRegistererdApp        |   Definite dall'utente uno o più URI che identificano in modo univoco un'applicazione Web in relativi tenant di Azure AD o in un dominio personalizzato verificato se l'applicazione è multi-tenant.      |
|keyCredentials     |   Tipo di matrice      |   [{<br>&nbsp;"customKeyIdentifier": null,<br>"endDate": "2018-09-13T00:00:00Z",<br>"keyId": "\<guid >",<br>"startDate": "2017-09-12T00:00:00Z",<br>"type": "AsymmetricX509Cert",<br>"utilizzo": "Verifica",<br>"value": null<br>}]      |   Questa proprietà contiene riferimenti a credenziali applicazione assegnata, i segreti condivisi basati su stringa e certificati x. 509.  Queste credenziali entrano in gioco per la richiesta di token di accesso (quando l'app funge da un client piuttosto che come risorsa).     |
|knownClientApplications     |     Tipo di matrice    |    [guid]     |     Il valore viene utilizzato il consenso aggregazione se si dispone di una soluzione che contiene due parti, un'applicazione client e l'applicazione API web personalizzate. Se si immette l'appID dell'applicazione client in questo valore, l'utente sarà solo necessario il consenso una volta per l'applicazione client. Azure AD verrà sapere che il consenso al client indica in modo implicito il consenso all'API web e verrà automaticamente eseguito il provisioning entità servizio per il client e l'API web nello stesso momento (sia il client e l'applicazione dell'API web devono essere registrate nella stessa tenant).|
|logoutUrl     |   stringa      |     http:<i></i>//MyRegisteredAppLogout    |   URL di disconnessione dell'applicazione.      |
|oauth2AllowImplicitFlow     |   boolean      |  false       |       Specifica se l'applicazione web può richiedere token di flusso implicita OAuth 2.0. Il valore predefinito è false. Viene utilizzato per le app basate su browser, ad esempio App singola pagina Javascript. |
|oauth2AllowUrlPathMatching     |   boolean      |  false       |   Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consentirà la corrispondenza del percorso dell'URI di reindirizzamento in base all'oggetto replyUrls dell'applicazione. Il valore predefinito è false.      |
|oauth2Permissions     | Tipo di matrice         |      [{<br>"adminConsentDescription": "Consenti l'applicazione di accedere alle risorse per conto dell'utente connesso.",<br>"adminConsentDisplayName": "Accesso risorsa1",<br>"id": "\<guid >",<br>"isEnabled": true,<br>"type": "User",<br>"userConsentDescription": "Consenti all'applicazione di accedere risorsa1 per conto dell'utente.",<br>"userConsentDisplayName": "Accedere alle risorse"<br>"value": "user_impersonation"<br>}]   |  Raccolta di ambiti di autorizzazione OAuth 2.0 che l'applicazione dell'API web (risorsa) espone alle applicazioni client. Questi ambiti di autorizzazione possono essere concessi alle applicazioni client durante il consenso. |
|oauth2RequiredPostResponse     | boolean        |    false     |      Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consentirà le richieste POST, opposizione alle richieste GET. Il valore predefinito è false, che specifica che sono consentite solo le richieste GET.   
|objectId     | Stringa dell'identificatore        |     ""    |    Identificatore univoco per l'applicazione nella directory.  Questa operazione è l'identificatore utilizzato per identificare l'app in qualsiasi transazione di protocollo.  È l'utente per il riferimento all'oggetto nelle query di directory.|
|passwordCredentials     | Tipo di matrice        |   [{<br>"customKeyIdentifier": null,<br>"endDate": "2018-10-19T17:59:59.6521653Z",<br>"keyId": "\<guid >",<br>"startDate": "2016-10-19T17:59:59.6521653Z",<br>"value": null<br>}]      |    Vedere la descrizione per la proprietà keyCredentials.     |
|PublicClient     |  boolean       |      false   | Specifica se un'applicazione è un client pubblico (ad esempio, un'applicazione installata in esecuzione in un dispositivo mobile). Il valore predefinito è false.        |
|supportsConvergence     |  boolean       |   false      | Questa proprietà non deve essere modificata.  Accettare il valore predefinito.        |
|all'oggetto replyUrls     |  Matrice di stringhe       |   http:<i></i>//localhost     |  Questa proprietà multivalore contiene l'elenco dei valori registrati redirect_uri che Azure AD accetterà come destinazioni quando i token returining. |
|RequiredResourceAccess     |     Tipo di matrice    |    [{<br>"resourceAppId": "00000002-0000-0000-c000-000000000000",<br>"resourceAccess": [{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type": "Scope".<br>&nbsp;&nbsp;}]<br>}]     |   Specifica le risorse che l'applicazione richiede l'accesso e il set di ambiti di autorizzazione OAuth e i ruoli applicazione necessari per ognuno di tali risorse. Questa pre-configurazione dell'accesso alle risorse richieste unità l'esperienza di consenso.|
|resourceAppId     |    Stringa dell'identificatore     |  ""      |   Identificatore univoco per la risorsa a cui l'applicazione richiede l'accesso. Questo valore deve essere uguale a appId dichiarato nell'applicazione di risorsa di destinazione.     |
|resourceAccess     |  Tipo di matrice       | Il valore di esempio per la proprietà requiredResourceAccess, vedere.        |   L'elenco di ambiti di autorizzazione OAuth 2.0 ruoli app e che l'applicazione richiede la risorsa specificata (contiene i valori di ID e tipo di risorse specificate)        |
|samlMetadataUrl|stringa|http:<i></i>//MyRegisteredAppSAMLMetadata|URL dei metadati SAML per l'applicazione.| 

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni sulla relazione tra gli oggetti applicazione e dell'entità servizio dell'applicazione, vedere [applicazione e oggetti entità servizio in Azure AD][AAD-APP-OBJECTS].
* Per le definizioni di alcuni concetti di base per gli sviluppatori di Azure Active Directory (AD), vedere il [Glossario per gli sviluppatori di Azure Active Directory][AAD-DEVELOPER-GLOSSARY].

Utilizzare la seguente sezione commenti per fornire commenti e suggerimenti che consente di ridefinire e definire il contenuto.

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

