---
title: 'Azure Active Directory B2C: criteri predefiniti | Microsoft Docs'
description: Argomento che descrive il framework di criteri estendibile di Azure Active Directory B2C e che illustra come creare vari tipi di criteri
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mtillman
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: f0aa3d19e15837b75888293f0cd19683b7621a6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: criteri predefiniti


Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono le esperienze di identità, ad esempio iscrizione, accesso o modifica del profilo. Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

* Tipi di account (account di social network come Facebook o account locali come un indirizzo di posta elettronica) che gli utenti possono usare per effettuare l'iscrizione all'applicazione
* Attributi (ad esempio nome, codice postale, numero di scarpe) da raccogliere dall'utente durante la procedura di iscrizione
* Uso di Azure Multi-Factor Authentication
* Aspetto di tutte le pagine di iscrizione
* Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione dei criteri

È possibile creare più criteri di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I criteri possono essere usati per più applicazioni. Gli sviluppatori possono in questo modo definire e modificare le esperienze delle identità degli utenti con modifiche minime al codice o anche senza alcuna modifica al codice.

Per usare i criteri, è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva i criteri tramite una richiesta di autenticazione HTTP standard (passando un parametro criteri nella richiesta) e riceve un token personalizzato come risposta. L'unica differenza tra le richieste che richiamano i criteri di iscrizione e le richieste che richiamano i criteri di accesso risiede nel nome del criterio usato nel parametro della stringa di query "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Per altre informazioni sul framework criteri, vedere [questo post di blog su Azure AD B2C sul blog relativo a Enterprise Mobility and Security](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creare un criterio di iscrizione o accesso

Questo criterio consente di gestire le esperienze di iscrizione e accesso dei consumatori tramite una singola configurazione. I consumatori vengono indirizzati sul percorso corretto (iscrizione o accesso) a seconda del contesto. Vengono inoltre descritti i contenuti dei token che l'applicazione riceverà al completamento dell'iscrizione o dell'accesso.  Un esempio di codice per i criteri di iscrizione o di accesso è [disponibile qui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  È consigliabile usare questo criterio invece di criteri di iscrizione e di accesso.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Creare un criterio di iscrizione

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Creare un criterio di accesso

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Come si collegano i criteri di iscrizione o accesso ai criteri di reimpostazione delle password?
Quando si creano i criteri di iscrizione o accesso (con gli account locali), l'utente visualizza un collegamento **Password dimenticata?** nella prima pagina. Se si fa clic su questo collegamento non si attivano automaticamente i criteri di reimpostazione delle password. 

Viene invece restituito il codice di errore **`AADB2C90118`** all'app. L'applicazione deve gestire questo codice di errore e richiamare criteri di reimpostazione delle password specifici. Per altre informazioni, vedere un [esempio che illustra come collegare i criteri](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>È consigliabile usare criteri di iscrizione o accesso o criteri di iscrizione e criteri di accesso?
È consigliabile usare criteri di iscrizione o accesso al posto di criteri di iscrizione e criteri di accesso.  

I criteri di iscrizione o accesso dispongono di più funzionalità rispetto ai criteri di accesso. Consentono anche di usare la personalizzazione dell'interfaccia utente di pagina e sono meglio supportati nella localizzazione. 

I criteri di accesso sono consigliati se non si desidera localizzare i criteri, servono solo funzionalità di personalizzazione secondarie per la personalizzazione e si desidera disporre della funzionalità di reimpostazione delle password integrata.

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione di token, sessione e accesso Single Sign-On](active-directory-b2c-token-session-sso.md)
* [Disabilitare la verifica della posta elettronica durante l'iscrizione dell'utente](active-directory-b2c-reference-disable-ev.md)

