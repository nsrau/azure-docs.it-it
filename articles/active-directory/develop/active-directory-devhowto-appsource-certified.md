---
title: Come ottenere la certificazione AppSource per Azure Active Directory | Documentazione Microsoft
description: Leggere i dettagli su come ottenere la certificazione AppSource per Azure Active Directory.
services: active-directory
documentationcenter: 
author: andretms
manager: mtillman
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5601ad80e271364fec519cf34bcdc2f650f3bb92
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Come ottenere la certificazione AppSource per Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) è la posizione in cui gli utenti aziendali possono trovare, provare e gestire applicazioni SaaS line-of-business (soluzioni SaaS autonome e componenti aggiuntivi per prodotti Microsoft SaaS esistenti).

Per inserire un'applicazione SaaS autonoma in AppSource, è necessario che l'applicazione accetti l'accesso Single Sign-On dagli account aziendali di qualunque società o organizzazione che usa Azure Active Directory. Il processo di accesso deve usare il protocollo [OpenID Connect](./active-directory-protocols-openid-connect-code.md) o [OAuth 2.0](./active-directory-protocols-oauth-code.md). L'integrazione SAML non è accettata per la certificazione AppSource.

## <a name="guides-and-code-samples"></a>Guide ed esempi di codice
Per informazioni su come integrare l'applicazione con Azure Active Directory usando OpenID Connect, seguire le guide e gli esempi di codice della [Guida per gli sviluppatori di Azure Active Directory](./active-directory-developers-guide.md#get-started "Introduzione ad Azure AD per sviluppatori").

## <a name="multi-tenant-applications"></a>Applicazioni multi-tenant

Un'applicazione che accetta l'accesso degli utenti da qualsiasi società o organizzazione con Azure Active Directory senza richiedere un'istanza, una configurazione o una distribuzione separata è nota come *applicazione multi-tenant*. AppSource consiglia che le applicazioni implementino la multi-tenancy per abilitare l'esperienza di valutazione gratuita con un *unico clic*.

Per abilitare la multi-tenancy nell'applicazione:
- Impostare la proprietà `Multi-Tenanted` su `Yes` nelle informazioni di registrazione dell'applicazione nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (per impostazione predefinita, le applicazioni create nel portale di Azure sono configurate come a *tenant singolo*)
- Aggiornare il codice per inviare le richieste all'endpoint "`common`" (aggiornare l'endpoint da *https://login.microsoftonline.com/{tenantinuso}* a *https://login.microsoftonline.com/common*)
- Per alcune piattaforme, ad esempio ASP.NET, è anche necessario aggiornare il codice per accettare più emittenti

Per altre informazioni sulla multi-tenancy, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Applicazioni a tenant singolo
Le applicazioni che accettano solo l'accesso degli utenti di un'istanza di Azure Active Directory definita sono note come *applicazioni a tenant singolo*. Gli utenti esterni, inclusi gli account aziendali o degli istituti di istruzione di altre organizzazioni o gli account personali, possono accedere a un'applicazione a tenant singolo dopo l'aggiunta di ogni utente come *account guest* all'istanza di Azure Active Directory a cui l'applicazione è registrata. È possibile aggiungere utenti come account guest in Azure Active Directory tramite la [*collaborazione B2B di Azure AD*](../active-directory-b2b-what-is-azure-ad-b2b.md) e l'operazione può essere eseguita [a livello di codice](../active-directory-b2b-code-samples.md). Quando si aggiunge un utente come account guest in Azure Active Directory, viene inviato un messaggio di posta elettronica di invito all'utente, che deve accettare l'invito facendo clic sul collegamento in esso contenuto. Gli inviti inviati a un utente aggiuntivo in un'organizzazione che emette l'invito che è anche membro dell'organizzazione partner non devono essere necessariamente accettati come condizione per l'accesso.

Le applicazioni a tenant singolo possono consentire l'esperienza *Desidero essere contattato*, ma per abilitare l'esperienza di valutazione gratuita/singolo clic consigliata da AppSource è necessario abilitare la multi-tenancy per l'applicazione.


## <a name="appsource-trial-experiences"></a>Esperienze di valutazione di AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Versione di valutazione gratuita (esperienza di valutazione gestita dal cliente) 
La *valutazione gestita dal cliente* è l'esperienza consigliata da AppSource che offre accesso all'applicazione con un solo clic. Di seguito è illustrata questa esperienza:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>L'utente trova l'applicazione nel sito Web AppSource</li><li>Seleziona l'opzione "Versione di valutazione gratuita"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource reindirizza l'utente a un URL nel sito Web correlato all'app</li><li>Il sito Web avvia automaticamente il processo <i>Single Sign-On</i> (al caricamento della pagina)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>L'utente viene reindirizzato alla pagina di accesso Microsoft</li><li>L'utente fornisce le credenziali per l'accesso</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>L'utente dà il consenso per l'applicazione</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web</li><li>L'utente inizia a usare la versione di valutazione gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Desidero essere contattato (esperienza di valutazione gestita dal partner)
L'*esperienza di valutazione gestita dal partner* può essere usata quando è necessaria un'operazione manuale o a lungo termine per effettuare il provisioning dell'utente o dell'azienda: ad esempio, l'applicazione deve effettuare il provisioning di macchine virtuali, istanze di database o operazioni che richiedono molto tempo per il completamento. In questo caso, dopo che l'utente seleziona il pulsante *"Richiedi versione di valutazione"* e compila un modulo, AppSource invia le informazioni di contatto dell'utente. Dopo la ricezione di queste informazioni, è possibile effettuare il provisioning dell'ambiente e inviare all'utente le istruzioni per l'accesso all'esperienza di valutazione:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>L'utente trova l'applicazione nel sito Web AppSource</li><li>Seleziona l'opzione "Desidero essere contattato"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Compila un modulo con le informazioni di contatto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Le informazioni dell'utente vengono ricevute</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>L'ambiente viene configurato</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>L'utente viene contattato con le informazioni sulla versione di valutazione</td>
        </tr>
        </table><br/><br/>
        <ul><li>Le informazioni dell'utente vengono ricevute e viene configurata l'istanza di valutazione</li><li>Viene inviato all'utente il collegamento ipertestuale per accedere all'applicazione</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>L'utente accede all'applicazione e completa il processo Single Sign-On</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>L'utente dà il consenso per l'applicazione</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web</li><li>L'utente inizia a usare la versione di valutazione gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Altre informazioni
Per altre informazioni sull'esperienza di valutazione di AppSource, vedere [questo video](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla creazione di applicazioni che supportano gli accessi di Azure Active Directory, vedere [Scenari di autenticazione per Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Per informazioni su come inserire un'app SaaS in AppSource, vedere le [informazioni per i partner di AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Ottenere supporto
Per l'integrazione di Azure Active Directory, viene usato [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource), con il supporto della community. 

È consigliabile provare prima a porre le domande in Stack Overflow ed esplorare le problematiche esistenti per vedere se un utente ha già posto la stessa domanda. Assicurarsi di aggiungere alle domande o ai commenti i tag [`[azure-active-directory]` e `[appsource]`](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->