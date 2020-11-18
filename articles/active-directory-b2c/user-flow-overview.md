---
title: Flussi utente in Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Altre informazioni sul framework di criteri estendibile di Azure Active Directory B2C e su come creare vari tipi di flussi utente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840192"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Flussi utente in Azure Active Directory B2C

Per semplificare la configurazione delle attività di identità più comuni per le applicazioni, il portale di Azure AD B2C include criteri predefiniti e configurabili detti **flussi utente**. Un flusso utente consente di determinare il modo in cui gli utenti interagiscono con l'applicazione quando eseguono operazioni come l'accesso, l'iscrizione, la modifica di un profilo o la reimpostazione della password. Con i flussi utente è possibile controllare le funzionalità seguenti:

- I tipi di account usati per l'accesso, ad esempio account di social networking, come Facebook o account locali
- Gli attributi da raccogliere dall'utente, ad esempio nome, codice postale e misura del piede
- Azure AD Multi-Factor Authentication
- Personalizzazione dell'interfaccia utente
- Informazioni che l'applicazione riceve come attestazioni nei token

È possibile creare molti flussi utente di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni. Questa flessibilità consente di definire e modificare le esperienze per le identità con modifiche minime al codice o anche senza alcuna modifica al codice. L'applicazione attiva un flusso utente usando una richiesta di autenticazione HTTP standard che include un parametro di flusso utente. Come risposta viene ricevuto un oggetto [token](tokens-overview.md) personalizzato.

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

Azure AD B2C include diversi tipi di flussi utente:

- **Iscrizione e accesso** - Consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti vengono indirizzati al percorso corretto in base al contesto. Sono inclusi anche flussi utente di **iscrizione** o **accesso** distinti. Tuttavia, in genere è consigliabile usare il flusso di accesso combinato e l'accesso dell'utente.
- **Modifica del profilo** - Consente agli utenti di modificare le informazioni del profilo personale.
- **Reimpostazione password** - Consente di configurare se e come gli utenti possono reimpostare le password.

La maggior parte dei tipi di flusso utente ha una versione **consigliata** e una versione **standard** . Per informazioni dettagliate, vedere [versioni dei flussi utente](user-flow-versions.md).

> [!IMPORTANT]
> Se si è già lavorato con i flussi utente in Azure AD B2C prima, si noterà che è stato modificato il modo in cui si fa riferimento alle versioni del flusso utente. In precedenza, venivano offerte le versioni V1 (pronte per l'ambiente di produzione) e le versioni V 1.1 e V2 (anteprima). A questo punto, i flussi utente sono stati consolidati in due versioni:
>
>- I flussi utente **consigliati** sono le nuove versioni di anteprima dei flussi utente. Sono testati accuratamente e combinano tutte le funzionalità delle versioni **v2** e **v 1.1** legacy. In futuro, i nuovi flussi utente consigliati verranno mantenuti e aggiornati. Quando si passa a questi nuovi flussi utente consigliati, sarà possibile accedere alle nuove funzionalità non appena vengono rilasciate.
>- I flussi utente standard, precedentemente noti come **V1**, sono disponibili a **livello** generale, flussi utente pronti per la produzione. Se i flussi utente sono cruciali e dipendono da versioni altamente stabili, è possibile continuare a usare i flussi utente standard, rendendo conto che tali versioni non verranno gestite e aggiornate.
>
>Tutti i flussi utente di anteprima legacy (V 1.1 e v2) si trovano in un percorso deprecato entro **il 1 agosto 2021**. Laddove possibile, è consigliabile [passare ai nuovi flussi utente **consigliati** il](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) prima possibile, in modo da poter sfruttare sempre le funzionalità e gli aggiornamenti più recenti.

## <a name="linking-user-flows"></a>Collegamento dei flussi utente

Un flusso utente di **iscrizione o di accesso** con gli account locali include un collegamento **Password dimenticata?** nella prima pagina dell'esperienza. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password.

Viene invece restituito il codice di errore `AADB2C90118` all'applicazione. L'applicazione deve gestire questo codice di errore eseguendo un flusso utente specifico che reimposta la password. Per un esempio, vedere un [semplice esempio di ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) che illustra il collegamento di flussi utente.

## <a name="email-address-storage"></a>Archiviazione di indirizzi di posta elettronica

Un indirizzo di posta elettronica può essere richiesto come parte di un flusso utente. Se l'utente viene autenticato con un provider di identità di social networking, l'indirizzo di posta elettronica viene archiviato nella proprietà **otherMails**. Se un account locale è basato su un nome utente, l'indirizzo di posta elettronica viene archiviato in una proprietà con i dettagli dell'autenticazione avanzata. Se un account locale è basato su un indirizzo di posta elettronica, l'indirizzo di posta elettronica viene archiviato nella proprietà **signInNames**.

In questi casi non è garantita la verifica dell'indirizzo di posta elettronica. Un amministratore tenant può disabilitare la verifica degli indirizzi di posta elettronica nei criteri di base per gli account locali. Anche se la verifica degli indirizzi di posta elettronica è abilitata, gli indirizzi non vengono verificati se provengono da un provider di identità di social networking e non sono stati modificati.

Solo le proprietà **otherMails** e **signInNames** vengono esposte tramite l'API Microsoft Graph. L'indirizzo di posta elettronica nella proprietà con i dettagli dell'autenticazione avanzata non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

Per creare i flussi utente consigliati, seguire le istruzioni riportate in [esercitazione: creare un flusso utente](tutorial-create-user-flows.md).
