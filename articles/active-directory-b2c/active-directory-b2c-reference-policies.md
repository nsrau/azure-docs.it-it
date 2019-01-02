---
title: Flussi utente in Azure Active Directory B2C | Microsoft Docs
description: Argomento che descrive il framework di criteri estendibile di Azure Active Directory B2C e che illustra come creare vari tipi di flussi utente.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877083"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C: Flussi degli utenti


Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono le esperienze di identità, ad esempio iscrizione, accesso o modifica del profilo. Per poter configurare le attività di gestione delle identità più comuni, il portale di Azure AD B2C include criteri predefiniti configurabili chiamati **flussi utente**. Ad esempio, i flussi utente di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

* Tipi di account (account di social network come Facebook o account locali come un indirizzo di posta elettronica) che gli utenti possono usare per effettuare l'iscrizione all'applicazione
* Attributi (ad esempio nome, codice postale, numero di scarpe) da raccogliere dall'utente durante la procedura di iscrizione
* Uso di Azure Multi-Factor Authentication
* Aspetto di tutte le pagine di iscrizione
* Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione del flusso utente

È possibile creare più flussi utente di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni. Gli sviluppatori possono in questo modo definire e modificare le esperienze delle identità degli utenti con modifiche minime al codice o anche senza alcuna modifica al codice.

Per usare i flussi utente è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva un flusso utente tramite una richiesta di autenticazione HTTP standard (passando un parametro di flusso utente nella richiesta) e riceve un token personalizzato come risposta. Ad esempio, l'unica differenza tra le richieste che richiamano un flusso utente di iscrizione e le richieste che richiamano un flusso utente di accesso risiede nel nome del flusso utente usato nel parametro della stringa di query "p":

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Creare un flusso utente di iscrizione o accesso

Il flusso utente consente di gestire le esperienze di iscrizione e accesso dei consumatori tramite una singola configurazione. I consumatori vengono indirizzati sul percorso corretto (iscrizione o accesso) a seconda del contesto. Vengono inoltre descritti i contenuti dei token che l'applicazione riceverà al completamento dell'iscrizione o dell'accesso.  Il codice di esempio per il flusso utente di **iscrizione o di accesso** è [disponibile qui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  È consigliabile usare questo flusso utente anziché un flusso utente di **iscrizione** o di **accesso**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Creare un flusso utente di iscrizione

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Creare un flusso utente di accesso

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Visualizzare i flussi utente in anteprima

Al momento del rilascio di nuove funzionalità, alcune di queste potrebbero non essere disponibili nei criteri o flussi utente esistenti.  È prevista la sostituzione delle versioni precedenti con la versione più recente dello stesso tipo dopo l'inserimento dei flussi utente nel GA.  I criteri o flussi utente esistenti non cambieranno e per sfruttare queste nuove funzionalità sarà necessario creare nuovi flussi utente.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Come si collegano un flusso utente di iscrizione o di accesso al flusso utente di reimpostazione della password?
Quando si crea il flusso utente di **iscrizione o di accesso** (con gli account locali), l'utente visualizza un collegamento **Password dimenticata?** nella prima pagina. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password. 

Viene invece restituito il codice di errore **`AADB2C90118`** all'app. L'app deve gestire questo codice di errore e richiamare un flusso utente di reimpostazione della password specifico. Per altre informazioni, vedere un [esempio che illustra come collegare i flussi utente](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>È necessario usare un flusso utente di iscrizione o di accesso oppure un flusso utente di iscrizione e un flusso utente di accesso?
È consigliabile usare un flusso utente **di iscrizione o di accesso** anziché un flusso utente di **iscrizione** e un flusso utente di **accesso**.  

Il flusso utente di **iscrizione o di accesso** offre più funzionalità rispetto a un flusso utente di **accesso**. Consentono anche di usare la personalizzazione dell'interfaccia utente di pagina e sono meglio supportati nella localizzazione. 

Il flusso utente di **accesso** è consigliato quando non è necessario localizzare i flussi utente, quando servono solo funzionalità di personalizzazione secondarie per il marchio e quando si vuole che la funzionalità di reimpostazione della password sia integrata.

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione di token, sessione e accesso Single Sign-On](active-directory-b2c-token-session-sso.md)
* [Disabilitare la verifica della posta elettronica durante l'iscrizione dell'utente](active-directory-b2c-reference-disable-ev.md)

