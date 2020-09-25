---
title: Ottenere la certificazione AppSource per Azure Active Directory | Microsoft Docs
description: Informazioni dettagliate su come ottenere la certificazione AppSource dell'applicazione per Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 65d6151536eb92e2ab9b740a99984fb0b5977cab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256875"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Ottenere la certificazione AppSource per Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) è la posizione in cui gli utenti aziendali possono trovare, provare e gestire applicazioni SaaS line-of-business (soluzioni SaaS autonome e componenti aggiuntivi per prodotti Microsoft SaaS esistenti).

Per inserire un'applicazione SaaS autonoma in AppSource, è necessario che l'applicazione accetti l'accesso Single Sign-On dagli account aziendali di qualunque società o organizzazione che usa Azure Active Directory (Azure AD). Il processo di accesso deve usare il protocollo [OpenID Connect](v2-protocols-oidc.md) o [OAuth 2.0](v2-oauth2-auth-code-flow.md). L'integrazione SAML non è accettata per la certificazione AppSource.

## <a name="multi-tenant-applications"></a>Applicazioni multi-tenant

Un'*applicazione multi-tenant* è un'applicazione che accetta l'accesso degli utenti da qualsiasi società o organizzazione con Azure AD senza richiedere un'istanza, una configurazione o una distribuzione separata. AppSource consiglia che le applicazioni implementino la multi-tenancy per abilitare l'esperienza di valutazione gratuita con un *unico clic*.

Per abilitare la multi-tenancy nell'applicazione, seguire questi passaggi:
1. Impostare `Multi-Tenanted` proprietà `Yes` sulle informazioni di registrazione dell'applicazione nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Per impostazione predefinita, le applicazioni create nel portale di Azure sono configurate come *[a tenant singolo](#single-tenant-applications)*.
1. Aggiornare il codice per l'invio delle richieste all'`common`endpoint. A tale scopo, aggiornare l'endpoint da `https://login.microsoftonline.com/{yourtenant}` a `https://login.microsoftonline.com/common*`.
1. Per alcune piattaforme, ad esempio ASP.NET, è necessario aggiornare anche il codice per accettare più autorità emittenti.

Per altre informazioni sulla multi-tenancy, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (Azure AD) usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Applicazioni a tenant singolo

Un'*applicazione a singolo tenant* è un'appliczione che accetta solo l'accesso degli utenti di un'istanza di Azure AD. Gli utenti esterni, inclusi gli account aziendali o degli istituti di istruzione di altre organizzazioni o gli account personali, possono accedere a un'applicazione a tenant singolo dopo l'aggiunta di ogni utente come account guest all'istanza di AD a cui l'applicazione è registrata. 

È possibile aggiungere utenti come account guest in Azure AD tramite la [collaborazione B2B di Azure AD](../external-identities/what-is-b2b.md) e l'operazione può essere eseguita [a livello di codice](../../active-directory-b2c/code-samples.md). Quando si usano B2B, gli utenti possono creare un portale self-service che non richiede un invito per accedere. Per altre informazioni, consulta [Portale self-service per l'iscrizione a Collaborazione B2B di Azure AD](../external-identities/self-service-portal.md).

Le applicazioni a tenant singolo possono consentire l'esperienza *Desidero essere contattato*, ma per abilitare l'esperienza di valutazione gratuita/singolo clic consigliata da AppSource è necessario abilitare la multi-tenancy per l'applicazione.

## <a name="appsource-trial-experiences"></a>Esperienze di valutazione di AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Versione di valutazione gratuita (esperienza di valutazione gestita dal cliente)

La valutazione gestita dal cliente è l'esperienza consigliata da AppSource che offre accesso all'applicazione con un solo clic. L'esempio seguente mostra l'aspetto di questa esperienza:

1.  Un utente trova l'applicazione nel sito Web AppSource, quindi seleziona l'opzione **versione di valutazione gratuita** .

    ![Mostra la versione di valutazione gratuita per l'esperienza di valutazione gestita dal cliente](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource reindirizza l'utente a un URL nel sito Web. Il sito Web avvia automaticamente il processo *Single Sign-on* (al caricamento della pagina).

    ![Mostra in che modo l'utente viene reindirizzato a un URL nel sito Web](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  L'utente viene reindirizzato alla pagina di accesso di Microsoft e l'utente fornisce le credenziali per l'accesso.

    ![Mostra la pagina di accesso di Microsoft](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. L'utente fornisce il consenso per l'applicazione.

    ![Esempio: pagina di consenso per un'applicazione](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web.  L'utente avvia la versione di valutazione gratuita.

    ![Mostra l'esperienza che l'utente vede quando viene reindirizzato di nuovo al sito](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Desidero essere contattato (esperienza di valutazione gestita dal partner)

L'esperienza di valutazione gestita dal partner può essere usata quando è necessaria un'operazione manuale o a lungo termine per effettuare il provisioning dell'utente o dell'azienda: ad esempio, l'applicazione deve effettuare il provisioning di macchine virtuali, istanze di database o operazioni che richiedono molto tempo per il completamento. In questo caso, dopo che l'utente seleziona il pulsante **Richiedi versione di valutazione** e compila un modulo, AppSource invia le informazioni di contatto dell'utente. Dopo la ricezione di queste informazioni, è possibile effettuare il provisioning dell'ambiente e inviare all'utente le istruzioni per l'accesso all'esperienza di valutazione:<br/><br/>

1. Un utente trova l'applicazione nel sito Web AppSource, quindi seleziona **contattami**.

    ![Visualizza il contatto per l'esperienza di valutazione guidata dal partner](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. L'utente compila un modulo con le informazioni di contatto.

    ![Mostra un modulo di esempio con le informazioni di contatto](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Si ricevono le informazioni dell'utente, si configura un'istanza di valutazione e si invia il collegamento ipertestuale per accedere all'applicazione all'utente.

    ![Mostra segnaposto per le informazioni utente](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. L'utente accede all'applicazione e completa il processo di Single Sign-On.

    ![Mostra la schermata di accesso dell'applicazione](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. L'utente fornisce il consenso per l'applicazione.

    ![Mostra una pagina di consenso di esempio per un'applicazione](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. L'accesso viene completato e l'utente viene reindirizzato di nuovo al sito Web. L'utente avvia la versione di valutazione gratuita.

    ![Mostra l'esperienza che l'utente vede quando viene reindirizzato di nuovo al sito](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Ulteriori informazioni

Per altre informazioni sull'esperienza di valutazione di AppSource, vedere [questo video](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Ottenere supporto

Per l'integrazione di Azure AD, viene usato [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource), con il supporto della community.

È consigliabile provare prima a porre le domande in Stack Overflow ed esplorare le problematiche esistenti per vedere se un utente ha già posto la stessa domanda. Assicurarsi che le domande o i commenti siano contrassegnati con [ `[azure-active-directory]` e `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla creazione di applicazioni che supportano gli accessi di Azure AD, vedere [Scenari di autenticazione per Azure AD](authentication-flows-app-scenarios.md).
- Per informazioni su come inserire un'app SaaS in AppSource, vedere le [informazioni per i partner di AppSource](https://appsource.microsoft.com/partners)