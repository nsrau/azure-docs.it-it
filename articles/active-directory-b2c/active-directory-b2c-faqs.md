---
title: 'Domande frequenti: Azure AD B2C | Microsoft Docs'
description: Domande frequenti su Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: e8b28bc9ccc12b280b1746272519bd4c9ea9e4a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: domande frequenti 
Questa pagina include le risposte alle domande frequenti relative ad Azure Active Directory (Azure AD) B2C. Controllarla costantemente per eventuali aggiornamenti.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>È possibile usare le funzionalità di Azure AD B2C nel tenant di Azure AD esistente per dipendenti aziendali?
Azure AD e Azure AD B2C vengono offerti come prodotti separati e non possono coesistere nello stesso tenant.  Un tenant di Azure AD rappresenta un'organizzazione.  Un tenant di Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni relying party.  Con i criteri personalizzati (in anteprima pubblica), Azure AD B2C può eseguire la federazione di Azure AD consentendo l'autenticazione dei dipendenti in un'organizzazione.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>È possibile usare Azure AD B2C per fornire l'accesso a piattaforme di social networking (Facebook e Google+) in Office 365?
Azure AD B2C non può essere usato per autenticare gli utenti per Microsoft Office 365.  Azure AD è la soluzione Microsoft per la gestione dell'accesso dei dipendenti alle applicazioni SaaS e dispone di funzionalità progettate a tale scopo, ad esempio l'accesso condizionale e le licenze.  Azure AD B2C offre una piattaforma di gestione di identità e accessi per la compilazione di applicazioni web e per dispositivi mobili.  Quando Azure AD B2C è configurato per eseguire la federazione di un tenant di Azure AD, il tenant di Azure AD gestisce l'accesso dei dipendenti alle applicazioni che si basano su Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Che cosa sono gli account locali in Azure AD B2C? In che cosa differiscono dagli account aziendali o dell'istituto di istruzione in Azure AD?
In un tenant di Azure AD, gli utenti che vi appartengono effettuano l'accesso con un indirizzo di posta elettronica del modulo `<xyz>@<tenant domain>`.  `<tenant domain>` è uno dei domini verificati nel tenant o nel dominio `<...>.onmicrosoft.com` iniziale. Questo tipo di account è un account aziendale o dell'istituto di istruzione.

In un tenant di Azure AD B2C, la maggior parte delle app richiede l'accesso dell'utente con un indirizzo di posta elettronica arbitrario, ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com. Questo tipo di account è un account locale.  Sono supportati anche nomi utente arbitrari come account locali (ad esempio, joe, bob, sarah o jim). È possibile scegliere uno di questi due tipi di account locale configurando Azure AD B2C nel portale di Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quali provider di identità di social networking sono attualmente supportati? Per quali provider è previsto il supporto in futuro?
Attualmente sono supportati Facebook, Google +, LinkedIn, Amazon, Twitter (anteprima), WeChat (anteprima), Weibo (anteprima) e QQ (anteprima). Verrà aggiunto il supporto per altri provider di identità di social networking noti, in base alle esigenze dei clienti.

In Azure AD B2C è stato aggiunto anche il supporto per i [criteri personalizzati](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom).  I [criteri personalizzati](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom) consentono agli sviluppatori di creare criteri con qualsiasi provider di identità che supporta [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) o SAML. 

Per un'introduzione ai criteri personalizzati, vedere lo [starter pack sui criteri personalizzati](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>È possibile configurare ambiti per raccogliere altre informazioni sugli utenti da diversi provider di identità di social networking?
No, ma questa funzionalità verrà implementata in futuro. Gli ambiti predefiniti usati per il gruppo di provider di identità di social networking supportato sono:

* Facebook: email
* Google+: email
* Account Microsoft: profilo di posta elettronica openid
* Amazon: profile
* LinkedIn: r_emailaddress e r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Per essere compatibile con Azure AD B2C, un'applicazione deve essere eseguita in Azure?
No, l'applicazione può essere ospitata ovunque (nel cloud o in locale). Per interagire con Azure AD B2C deve avere la possibilità di inviare e ricevere richieste HTTP su endpoint accessibili pubblicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Nel caso di più tenant Azure AD B2C, come è possibile gestirli nel portale di Azure?
Prima di aprire "Azure AD B2C" nel menu sul lato sinistro del portale di Azure, è necessario passare alla directory che si vuole gestire.  Per cambiare directory, fare clic sulla propria identità in alto a destra nel portale di Azure e quindi scegliere una directory nell'elenco a discesa che viene visualizzato.  Per una procedura dettagliata con immagini, vedere [Passare alle impostazioni di B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Come si personalizzano i messaggi di posta elettronica di verifica (il contenuto e il campo "Da:") inviati da Azure AD B2C?
È possibile usare la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md) per personalizzare il contenuto dei messaggi di posta elettronica di verifica. In particolare, è possibile personalizzare i due elementi di posta elettronica seguenti:

* **Logo del banner**: in basso a destra.
* **Colore di sfondo**: in alto.

    ![Screenshot di un messaggio di posta elettronica di verifica personalizzato](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma della posta elettronica contiene il nome del tenant B2C fornito al momento della creazione del tenant B2C. È possibile modificare il nome seguendo queste istruzioni:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione.
1. Accedere al tenant B2C.
1. Fare clic sulla scheda **Configura**.
1. Modificare il campo **Nome** nella sezione **Proprietà directory**.
1. Fare clic su **Save** nella parte inferiore della pagina.

Attualmente non è possibile modificare il campo "Da:" del messaggio di posta elettronica. Votare su [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) se si è interessati alla personalizzazione del corpo del messaggio di posta elettronica di verifica.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Come si esegue la migrazione di nomi utente, password e profili esistenti dal database personale in Azure AD B2C?
È possibile usare l'API Graph di Azure AD per creare lo strumento di migrazione. Per informazioni dettagliate, vedere l' [esempio relativo all'API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) .

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quali sono i criteri password usati per gli account locali in Azure AD B2C?
I criteri password di Azure AD B2C per gli account locali si basano su quelli di Azure AD. I criteri di iscrizione, iscrizione o accesso e reimpostazione della password di Azure AD B2C usano un livello di complessità della password "avanzato" e senza scadenza delle password. Per altre informazioni dettagliate, vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>È possibile usare Azure AD Connect per eseguire la migrazione in Azure AD B2C delle identità utente archiviate in Active Directory locale?
No, Azure AD Connect non è progettato per funzionare con Azure AD B2C. È consigliabile usare l'[API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) per la migrazione dell'utente.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>L'applicazione può aprire le pagine di Azure AD B2C all'interno di un iFrame?
No. Per motivi di sicurezza, le pagine di Azure AD B2C non possono essere aperte in un iFrame.  Il servizio comunica con il browser per impedire iFrames.  L'uso di iFrames per le esperienze di gestione delle identità è sconsigliato dalla community della sicurezza in generale e dalla specifica OAUTH2 a causa del rischio di clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C è compatibile con i sistemi CRM come Microsoft Dynamics?
È disponibile l'integrazione con il portale di Microsoft Dynamics 365.  Vedere l'articolo relativo alla [configurazione del portale di Dynamics 365 per usare Azure AD B2C per l'autenticazione](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C è compatibile con SharePoint 2016 locale o versione precedente?
Azure AD B2C non è concepito per lo scenario di condivisione esterna con i partner di SharePoint. A questo scopo, vedere invece il blog su [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>È necessario usare Azure AD B2C o B2B per gestire le identità esterne?
Per altre informazioni su come applicare le funzionalità appropriate agli scenari relativi alle identità esterne, vedere l'articolo sulle [identità esterne](../active-directory/active-directory-b2b-compare-external-identities.md) .

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quali funzionalità di reporting e controllo offre Azure AD B2C? Offre le stesse funzionalità di Azure AD Premium?
No, Azure AD B2C non supporta lo stesso set di report di Azure AD Premium. Esistono tuttavia molte caratteristiche comuni:

* I report degli accessi contengono un record per ogni accesso con dettagli ridotti.
* I report di controllo sono disponibili nel portale di Azure selezionando Azure Active Directory > ATTIVITÀ > Log di controllo > B2C e applicando i filtri desiderati. Sono incluse sia le attività amministrative che quelle delle applicazioni. 
* Un report di utilizzo, che include il numero di utenti, il numero di accessi e il volume di autenticazioni MFA, è disponibile tramite l'[API per la creazione di report di utilizzo](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api).

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>È possibile localizzare l'interfaccia utente delle pagine servite da Azure AD B2C? Quali lingue sono supportate?
È possibile usarlo.  Altre informazioni sulla [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md) che è in anteprima pubblica.  Offriamo traduzioni per 36 lingue ed è possibile eseguire l'override di qualsiasi stringa in base alle esigenze.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>È possibile usare l'URL personale nelle pagine di iscrizione e accesso servite da Azure AD B2C? È ad esempio possibile sostituire l'URL login.microsoftonline.com con login.contoso.com?
No, per il momento. Questa funzionalità verrà implementata in futuro. La verifica del dominio nella scheda **Domini** nel portale di Azure classico non raggiungere questo obiettivo.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Come si elimina il tenant di Azure AD B2C?
Attenersi a questa procedura per eliminare il tenant di Azure AD B2C:

1. Seguire questa procedura per [passare alle impostazioni di Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
1. Passare ad **Applicazioni**, **Provider di identità** e **Tutti i criteri** ed eliminare tutte le voci rispettivamente visualizzate.
1. Accedere ora al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Usare lo stesso account aziendale o dell'istituto d'istruzione o lo stesso account Microsoft usato per l'iscrizione ad Azure.
1. Passare all'estensione di Active Directory a sinistra e fare clic sul nome del tenant B2C.
1. Fare clic sulla scheda **Utenti** .
1. Selezionare un utente alla volta, escludendo l'amministratore della sottoscrizione con il quale si è connessi attualmente. Fare clic su **Elimina** nella parte inferiore della pagina, quindi su **SÌ** quando richiesto.
1. Fare clic sulla scheda **Applicazioni** .
1. Selezionare **Applications my company owns** (Applicazioni di proprietà dell'azienda) nel campo a discesa **Mostra** e fare clic sul segno di spunta.
1. Un'applicazione denominata **b2c-extensions-app**. Fare clic su **Elimina** nella parte inferiore della pagina, quindi su **SÌ** quando richiesto.
1. Passare di nuovo all'estensione di Active Directory e selezionare il tenant di B2C.
1. Nella parte inferiore della pagina fare clic su **Elimina** . Seguire le istruzioni sullo schermo per completare il processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>È possibile ottenere Azure AD B2C come parte di Enterprise Mobility Suite?
No, Azure AD B2C è un servizio di Azure con pagamento in base al consumo e non fa parte di Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Come è possibile segnalare problemi relativi ad Azure AD B2C?
Vedere [Azure Active Directory B2C: Inviare richieste di supporto](active-directory-b2c-support.md).
