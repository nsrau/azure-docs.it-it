---
title: Come ottenere la certificazione AppSource per Azure Active Directory | Documentazione Microsoft
description: Leggere i dettagli su come ottenere la certificazione AppSource per Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 034c02c89c6e720311b3dc36428035e8cbdd2b3b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835207"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Come ottenere la certificazione AppSource per Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) è la posizione in cui gli utenti aziendali possono trovare, provare e gestire applicazioni SaaS line-of-business (soluzioni SaaS autonome e componenti aggiuntivi per prodotti Microsoft SaaS esistenti).

Per inserire un'applicazione SaaS autonoma in AppSource, è necessario che l'applicazione accetti l'accesso Single Sign-On dagli account aziendali di qualunque società o organizzazione che usa Azure Active Directory (Azure AD). Il processo di accesso deve usare il protocollo [OpenID Connect](v1-protocols-openid-connect-code.md) o [OAuth 2.0](v1-protocols-oauth-code.md). L'integrazione SAML non è accettata per la certificazione AppSource.

## <a name="guides-and-code-samples"></a>Guide ed esempi di codice

Per informazioni su come integrare l'applicazione con Azure AD usando OpenID Connect, seguire le guide e gli esempi di codice della [Guida per gli sviluppatori di Azure Active Directory](v1-overview.md#get-started "Introduzione ad Azure AD per sviluppatori").

## <a name="multi-tenant-applications"></a>Applicazioni multi-tenant

Un'*applicazione multi-tenant* è un'applicazione che accetta l'accesso degli utenti da qualsiasi società o organizzazione con Azure AD senza richiedere un'istanza, una configurazione o una distribuzione separata. AppSource consiglia che le applicazioni implementino la multi-tenancy per abilitare l'esperienza di valutazione gratuita con un *unico clic*.

Per abilitare la multi-tenancy nell'applicazione, seguire questi passaggi:
1. Impostare `Multi-Tenanted` proprietà `Yes` sulle informazioni di registrazione dell'applicazione nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Per impostazione predefinita, le applicazioni create nel portale di Azure sono configurate come  *[a tenant singolo](#single-tenant-applications)* .
1. Aggiornare il codice per l'invio delle richieste all'`common`endpoint. A tale scopo, aggiornare l'endpoint da `https://login.microsoftonline.com/{yourtenant}` a `https://login.microsoftonline.com/common*`.
1. Per alcune piattaforme, ad esempio ASP .NET, è anche necessario aggiornare il codice per accettare più emittenti.

Per altre informazioni sulla multi-tenancy, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (Azure AD) usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Applicazioni a tenant singolo

Un'*applicazione a singolo tenant* è un'appliczione che accetta solo l'accesso degli utenti di un'istanza di Azure AD. Gli utenti esterni, inclusi gli account aziendali o degli istituti di istruzione di altre organizzazioni o gli account personali, possono accedere a un'applicazione a tenant singolo dopo l'aggiunta di ogni utente come account guest all'istanza di AD a cui l'applicazione è registrata. 

È possibile aggiungere utenti come account guest in Azure AD tramite la [collaborazione B2B di Azure AD](../b2b/what-is-b2b.md) e l'operazione può essere eseguita [a livello di codice](../../active-directory-b2c/code-samples.md). Quando si usano B2B, gli utenti possono creare un portale self-service che non richiede un invito per accedere. Per altre informazioni, consulta [Portale self-service per l'iscrizione a Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Le applicazioni a tenant singolo possono consentire l'esperienza *Desidero essere contattato*, ma per abilitare l'esperienza di valutazione gratuita/singolo clic consigliata da AppSource è necessario abilitare la multi-tenancy per l'applicazione.

## <a name="appsource-trial-experiences"></a>Esperienze di valutazione di AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Versione di valutazione gratuita (esperienza di valutazione gestita dal cliente)

La valutazione gestita dal cliente è l'esperienza consigliata da AppSource che offre accesso all'applicazione con un solo clic. L'esempio seguente mostra l'aspetto di questa esperienza:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>L'utente trova l'applicazione nel sito Web AppSource</li><li>Seleziona l'opzione "Versione di valutazione gratuita"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource reindirizza l'utente a un URL nel sito Web correlato all'app</li><li>Il sito Web avvia automaticamente il processo <i>Single Sign-On</i> (al caricamento della pagina)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>L'utente viene reindirizzato alla pagina di accesso Microsoft</li><li>L'utente fornisce le credenziali per l'accesso</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>L'utente dà il consenso per l'applicazione</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web</li><li>L'utente inizia a usare la versione di valutazione gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Desidero essere contattato (esperienza di valutazione gestita dal partner)

L'esperienza di valutazione gestita dal partner può essere usata quando è necessaria un'operazione manuale o a lungo termine per effettuare il provisioning dell'utente o dell'azienda: ad esempio, l'applicazione deve effettuare il provisioning di macchine virtuali, istanze di database o operazioni che richiedono molto tempo per il completamento. In questo caso, dopo che l'utente seleziona il pulsante **Richiedi versione di valutazione** e compila un modulo, AppSource invia le informazioni di contatto dell'utente. Dopo la ricezione di queste informazioni, è possibile effettuare il provisioning dell'ambiente e inviare all'utente le istruzioni per l'accesso all'esperienza di valutazione:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>L'utente trova l'applicazione nel sito Web AppSource</li><li>Seleziona l'opzione "Desidero essere contattato"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Compila un modulo con le informazioni di contatto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Le informazioni dell'utente vengono ricevute</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>L'ambiente viene configurato</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>L'utente viene contattato con le informazioni sulla versione di valutazione</td>
        </tr>
        </table><br/><br/>
        <ul><li>Le informazioni dell'utente vengono ricevute e viene configurata l'istanza di valutazione</li><li>Viene inviato all'utente il collegamento ipertestuale per accedere all'applicazione</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>L'utente accede all'applicazione e completa il processo Single Sign-On</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>L'utente dà il consenso per l'applicazione</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web</li><li>L'utente inizia a usare la versione di valutazione gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Altre informazioni

Per altre informazioni sull'esperienza di valutazione di AppSource, vedere [questo video](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Fasi successive

- Per altre informazioni sulla creazione di applicazioni che supportano gli accessi di Azure AD, vedere [Scenari di autenticazione per Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Per informazioni su come inserire un'app SaaS in AppSource, vedere le [informazioni per i partner di AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Supporto

Per l'integrazione di Azure AD, viene usato [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource), con il supporto della community.

È consigliabile provare prima a porre le domande in Stack Overflow ed esplorare le problematiche esistenti per vedere se un utente ha già posto la stessa domanda. Assicurarsi di aggiungere alle domande o ai commenti i tag [`[azure-active-directory]` e `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
