---
title: Aggiunta di provider di identità OpenID Connect nei criteri predefiniti - Azure Active Directory B2C | Microsoft Docs
description: Guida di panoramica su come aggiungere i provider di OpenID Connect in criteri incorporati all'interno di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.assetid: 357d193a-e33b-469c-8a93-0a8f45a60a9f
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.openlocfilehash: 46223f1ec6b82828983861dae26ec8d777b77b86
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308985"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: aggiunta di provider di identità OpenID Connect nei criteri predefiniti

>[!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica. Non utilizzare la funzionalità negli ambienti di produzione.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) è un protocollo di autenticazione, basato su OAuth 2.0, che può essere usato per l’accesso sicuro degli utenti. La maggior parte dei provider di identità che utilizzano questo protocollo, quali [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), sono supportati in Azure Active Directory B2C. In questo articolo viene illustrato come aggiungere provider di identità OpenID Connect personalizzati nei criteri predefiniti.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Configurazione di un provider di identità OpenID Connect personalizzato

Per aggiungere un provider di identità OpenID Connect personalizzato:

1. Eseguire la procedura di seguito per [passare alle impostazioni di Azure Active Directory B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
1. Fare clic su **Provider di identità**.
1. Fare clic su **Aggiungi**.
1. Per il **tipo di provider di identità**, selezionare **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Configurare il provider di identità OpenID Connect

#### <a name="metadata-url"></a>URL dei metadati

In base alla specifica, ogni provider di identità OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie per eseguire accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Il documento di metadati OpenID Connect si trova sempre in un endpoint che termina con `.well-known\openid-configuration`.

Per il provider di identità OpenID Connect che si desidera aggiungere, immettere l'URL dei metadati.

#### <a name="client-id-and-secret"></a>ID client e segreto

Per consentire agli utenti di accedere, il provider di identità richiederà agli sviluppatori di registrare un'applicazione nel proprio servizio. Questa applicazione avrà un ID (definito come l’**ID client**) e un **segreto client**. Copiare questi valori dal provider di identità e immetterli nei campi corrispondenti.

> [!NOTE]
> Il segreto client è facoltativo. Tuttavia, è necessario immettere un segreto client se si desidera utilizzare il [flusso del codice di autorizzazione](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), che utilizza il segreto per scambiare il codice con il token.

#### <a name="scope"></a>Scope

Gli ambiti definiscono le informazioni e le autorizzazioni desiderate che devono essere raccolte dal proprio provider di identità personalizzato. Le richieste di OpenID Connect devono contenere il `openid` valore dell'ambito per ricevere il token ID dal provider di identità. Senza il token ID, gli utenti non saranno in grado di accedere ad Azure Active Directory B2C utilizzando il provider di identità personalizzato.

È possibile aggiungere altri ambiti (separati da spazio). Fare riferimento alla documentazione del provider di identità personalizzato per vedere quale potrebbero essere altri ambiti disponibili.

#### <a name="response-type"></a>Tipo di risposta

Il tipo di risposta descrive il tipo di informazioni che saranno inviate nella chiamata iniziale al `authorization_endpoint` del provider di identità personalizzato. 

* `code`: come per il [flusso di codice di autorizzazione](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), verrà restituito un codice ad Azure Active Directory B2C. Azure Active Directory B2C procederà quindi alla chiamata di `token_endpoint` per scambiare il codice con il token.
* `token`: un token di accesso verrà restituito in Azure Active Directory B2C dal provider di identità personalizzato.
* `id_token`: un token ID verrà restituito in Azure Active Directory B2C dal provider di identità personalizzato.


#### <a name="response-mode"></a>Modalità di risposta

La modalità di risposta definisce il metodo che deve essere utilizzato per reinviare i dati dal provider di identità personalizzato ad Azure Active Directory B2C.

* `form_post`: questa modalità di risposta è consigliata per motivi di sicurezza. La risposta viene trasmessa tramite metodo HTTP `POST`, con il codice o token codificati nel corpo utilizzando il formato `application/x-www-form-urlencoded`.
* `query`: il codice o token verranno restituiti come un parametro di query.


#### <a name="domain-hint"></a>Hint di dominio

L'hint di dominio può essere utilizzato per passare direttamente alla pagina del provider di identità specificata, invece di richiedere all'utente di fare una selezione tra l'elenco dei provider di identità disponibili. Per consentire questo tipo di comportamento, immettere un valore per l'hint di dominio.

Per passare al provider di identità personalizzato, accodare il parametro `domain_hint=<domain hint value>` alla fine della richiesta durante la chiamata ad Azure Active Directory B2C per l'accesso.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapping di attestazioni dal provider di identità OpenID Connect

Dopo che il provider di identità personalizzato reinvia un token ID ad Azure Active Directory B2C, Azure Active Directory B2C deve essere in grado di mappare le attestazioni dal token ricevuto con le attestazioni che Azure Active Directory B2C riconosce e usa. 

Per ogni mapping indicato di seguito, consultare la documentazione del provider di identità personalizzato per comprendere le attestazioni che vengono restituite nel token del provider di identità.

* `User ID`: immettere l'attestazione che costituisce l'identificatore univoco per l'utente connesso.
* `Display Name`: immettere l'attestazione che fornisce il nome visualizzato o il nome e cognome per l'utente.
* `Given Name`: immettere l'attestazione che fornisce il nome dell'utente.
* `Surname`: immettere l'attestazione che fornisce il cognome dell'utente.
* `Email`: immettere l'attestazione che fornisce l’indirizzo e-mail dell'utente.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere il provider di identità OpenID Connect personalizzato al [criterio predefinito](active-directory-b2c-reference-policies.md).
