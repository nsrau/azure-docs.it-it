---
title: Configurare l'iscrizione e l'accesso con OpenID Connect tramite Azure Active Directory B2C | Microsoft Docs
description: Configurare l'iscrizione e l'accesso con OpenID Connect tramite Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9f6b65a4253b9cc7c04f397dde7ecab9a64d5ae0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845980"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con OpenID Connect tramite Azure Active Directory B2C

>[!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica. Non utilizzare la funzionalità negli ambienti di produzione.


[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) è un protocollo di autenticazione, basato su OAuth 2.0, che può essere usato per l’accesso sicuro degli utenti. La maggior parte dei provider di identità che utilizzano questo protocollo, quali [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), sono supportati in Azure Active Directory B2C. In questo articolo viene illustrato come aggiungere provider di identità OpenID Connect personalizzati nei flussi utente.


## <a name="add-the-identity-provider"></a>Aggiungere il provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi fare clic su **Aggiungi**.
5. Per il **Tipo di provider di identità**, selezionare **OpenID Connect (anteprima)**.

## <a name="configure-the-identity-provider"></a>Configurare il provider di identità

Ogni provider di identità OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie per eseguire l'accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Il documento di metadati OpenID Connect si trova sempre in un endpoint che termina con `.well-known\openid-configuration`. Per il provider di identità OpenID Connect che si desidera aggiungere, immettere l'URL dei metadati.

Per consentire agli utenti di accedere, il provider di identità richiederà agli sviluppatori di registrare un'applicazione nel proprio servizio. Questa applicazione ha un ID definito come l'**ID client** e un **segreto client**. Copiare questi valori dal provider di identità e immetterli nei campi corrispondenti.

> [!NOTE]
> Il segreto client è facoltativo. Tuttavia, è necessario immettere un segreto client se si desidera utilizzare il [flusso del codice di autorizzazione](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), che utilizza il segreto per scambiare il codice con il token.

Gli ambiti definiscono le informazioni e le autorizzazioni che si intende raccogliere dal proprio provider di identità personalizzato. Le richieste di OpenID Connect devono contenere il `openid` valore dell'ambito per ricevere il token ID dal provider di identità. Senza il token ID, gli utenti non sono in grado di accedere ad Azure Active Directory B2C usando il provider di identità personalizzato. È possibile aggiungere altri ambiti separati da spazio. Fare riferimento alla documentazione del provider di identità personalizzato per vedere quale potrebbero essere altri ambiti disponibili.

Il tipo di risposta descrive il tipo di informazioni che vengono inviate nella chiamata iniziale all'elemento `authorization_endpoint` del provider di identità personalizzato. È possibile usare i tipi di risposta seguenti:

- `code`: come per il [flusso di codice di autorizzazione](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), verrà restituito un codice ad Azure Active Directory B2C. Azure Active Directory B2C procede quindi alla chiamata di `token_endpoint` per scambiare il codice con il token.
- `token`: un token di accesso viene restituito in Azure Active Directory B2C dal provider di identità personalizzato.
- `id_token`: un ID token viene restituito in Azure Active Directory B2C dal provider di identità personalizzato.

La modalità di risposta definisce il metodo che deve essere utilizzato per reinviare i dati dal provider di identità personalizzato ad Azure Active Directory B2C. È possibile usare le modalità di risposta seguenti:

- `form_post`: questa modalità di risposta è consigliata per motivi di sicurezza. La risposta viene trasmessa tramite metodo HTTP `POST`, con il codice o token codificati nel corpo utilizzando il formato `application/x-www-form-urlencoded`.
- `query`: il codice o token viene restituito come un parametro di query.

L'hint di dominio può essere utilizzato per passare direttamente alla pagina del provider di identità specificata, invece di richiedere all'utente di fare una selezione tra l'elenco dei provider di identità disponibili. Per consentire questo tipo di comportamento, immettere un valore per l'hint di dominio. Per passare al provider di identità personalizzato, accodare il parametro `domain_hint=<domain hint value>` alla fine della richiesta durante la chiamata ad Azure Active Directory B2C per l'accesso.

Dopo che il provider di identità personalizzato reinvia un token ID ad Azure Active Directory B2C, Azure Active Directory B2C deve essere in grado di mappare le attestazioni dal token ricevuto con le attestazioni che Azure Active Directory B2C riconosce e usa. Per ogni mapping indicato di seguito, consultare la documentazione del provider di identità personalizzato per comprendere le attestazioni che vengono restituite nel token del provider di identità:

- `User ID`: immettere l'attestazione che costituisce l'identificatore univoco per l'utente connesso.
- `Display Name`: immettere l'attestazione che fornisce il nome visualizzato o il nome e cognome per l'utente.
- `Given Name`: immettere l'attestazione che fornisce il nome dell'utente.
- `Surname`: immettere l'attestazione che fornisce il cognome dell'utente.
- `Email`: immettere l'attestazione che fornisce l’indirizzo e-mail dell'utente.

