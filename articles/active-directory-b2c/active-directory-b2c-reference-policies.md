---
title: Flussi utente in Azure Active Directory B2C | Microsoft Docs
description: Altre informazioni sul framework di criteri estendibile di Azure Active Directory B2C e su come creare vari tipi di flussi utente.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bcbd26c8e78e29daa78a7e50f2f49b095103f696
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351782"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Flussi utente in Azure Active Directory B2C

Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono in modo completo le esperienze per le identità, ad esempio iscrizione, accesso o modifica del profilo. Per poter configurare le attività di gestione delle identità più comuni, il portale di Azure AD B2C include criteri predefiniti configurabili chiamati **flussi utente**. 

## <a name="what-are-user-flows"></a>Cosa sono i flussi utente?

Un flusso utente consente di controllare i comportamenti nelle applicazioni configurando le impostazioni seguenti:

- I tipi di account usati per l'accesso, ad esempio account di social networking, come Facebook o account locali
- Gli attributi da raccogliere dall'utente, ad esempio nome, codice postale e misura del piede
- Azure Multi-Factor Authentication
- Personalizzazione dell'interfaccia utente
- Informazioni che l'applicazione riceve come attestazioni nei token 

È possibile creare molti flussi utente di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni. Questa flessibilità consente di definire e modificare le esperienze per le identità con modifiche minime al codice o anche senza alcuna modifica al codice. L'applicazione attiva un flusso utente usando una richiesta di autenticazione HTTP standard che include un parametro di flusso utente. Come risposta viene ricevuto un oggetto [token](active-directory-b2c-reference-tokens.md) personalizzato. 

Gli esempi seguenti mostrano il parametro di stringa di query "p" che specifica il flusso utente da usare:

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

## <a name="user-flow-versions"></a>Versioni del flusso utente

Nel portale di Azure vengono aggiunte continuamente nuove [versioni dei flussi utente](user-flow-versions.md). Quando si inizia con Azure AD B2C, è consigliabile usare flussi utente testati. Quando si crea un nuovo flusso utente, è possibile scegliere il flusso utente necessario nella scheda **Consigliati**.

Sono attualmente consigliati i flussi utente seguenti:

- **Iscrizione e accesso** - Consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti vengono indirizzati al percorso corretto in base al contesto. È consigliabile usare questo flusso utente anziché un flusso utente di **iscrizione** o di **accesso**.
- **Modifica del profilo** - Consente agli utenti di modificare le informazioni del profilo personale.
- **Reimpostazione password** - Consente di configurare se e come gli utenti possono reimpostare le password.

## <a name="linking-user-flows"></a>Collegamento dei flussi utente

Un flusso utente di **iscrizione o di accesso** con gli account locali include un collegamento **Password dimenticata?** nella prima pagina dell'esperienza. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password. 

Viene invece restituito il codice di errore `AADB2C90118` all'applicazione. L'applicazione deve gestire questo codice di errore eseguendo un flusso utente specifico che reimposta la password. Per un esempio, vedere un [semplice esempio di ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) che illustra il collegamento di flussi utente.

## <a name="email-address-storage"></a>Archiviazione di indirizzi di posta elettronica

Un indirizzo di posta elettronica può essere richiesto come parte di un flusso utente. Se l'utente viene autenticato con un provider di identità di social networking, l'indirizzo di posta elettronica viene archiviato nella proprietà **otherMails**. Se un account locale è basato su un nome utente, l'indirizzo di posta elettronica viene archiviato in una proprietà con i dettagli dell'autenticazione avanzata. Se un account locale è basato su un indirizzo di posta elettronica, l'indirizzo di posta elettronica viene archiviato nella proprietà **signInNames**.
 
In questi casi non è garantita la verifica dell'indirizzo di posta elettronica. Un amministratore tenant può disabilitare la verifica degli indirizzi di posta elettronica nei criteri di base per gli account locali. Anche se la verifica degli indirizzi di posta elettronica è abilitata, gli indirizzi non vengono verificati se provengono da un provider di identità di social networking e non sono stati modificati.
 
Solo le proprietà **otherMails** e **signInNames** vengono esposte tramite l'API Graph di Active Directory. L'indirizzo di posta elettronica nella proprietà con i dettagli dell'autenticazione avanzata non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

Per creare i flussi utente consigliati, seguire le istruzioni riportate in [Esercitazione: Creare un flusso utente](tutorial-create-tenant.md).


