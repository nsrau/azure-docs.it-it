---
title: 'Azure Active Directory B2C: domande frequenti | Documentazione Microsoft'
description: Domande frequenti su Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 1b62ab8fe4f8a736821eb9da3d77fe2f654de745
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: domande frequenti
Questa pagina include le risposte alle domande frequenti relative ad Azure Active Directory (Azure AD) B2C. Controllarla costantemente per eventuali aggiornamenti.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>È possibile usare le funzionalità di Azure AD B2C nel tenant di Azure AD esistente per dipendenti aziendali?
Attualmente le funzionalità di Azure AD B2C non possono essere attivate nel tenant di Azure AD esistente. È consigliabile creare un tenant separato per usare le funzionalità di Azure AD B2C per gestire gli utenti.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>È possibile usare Azure AD B2C per fornire l'accesso a piattaforme di social networking (Facebook e Google+) in Office 365?
Non è possibile usare Azure AD B2C con Microsoft Office 365 e in generale per fornire l'autenticazione per app SaaS (Office 365, Salesforce, Workday e così via). Fornisce solo la gestione di identità e accessi per applicazioni Web e per dispositivi mobili destinate agli utenti e non è applicabile a scenari di dipendenti o partner.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Che cosa sono gli account locali in Azure AD B2C? In che cosa differiscono dagli account aziendali o dell'istituto di istruzione in Azure AD?
In un tenant di Azure AD ogni utente del tenant, tranne quelli con account Microsoft esistenti, accede con un indirizzo di posta elettronica nel formato `<xyz>@<tenant domain>`, dove `<tenant domain>` è uno dei domini verificati nel tenant o il dominio `<...>.onmicrosoft.com` iniziale. Questo tipo di account è un account aziendale o dell'istituto di istruzione.

In un tenant di Azure AD B2C, la maggior parte delle app richiede l'accesso dell'utente con un indirizzo di posta elettronica arbitrario, ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com. Questo tipo di account è un account locale. Attualmente sono supportati anche nomi utente arbitrari (semplici stringhe) come account locali (ad esempio, joe, bob, sarah o jim). Nel servizio Azure AD B2C è possibile scegliere uno di questi due tipi di account locale.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quali provider di identità di social networking sono attualmente supportati? Per quali provider è previsto il supporto in futuro?
Sono attualmente supportati Facebook, Google+, LinkedIn e Amazon. Verrà aggiunto il supporto per altri provider di identità di social networking noti, in base alle esigenze dei clienti.

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
Ogni tenant di Azure AD B2C ha uno specifico pannello delle funzionalità B2C nel portale di Azure. Per informazioni su come passare al pannello delle funzionalità B2C di un tenant specifico nel portale di Azure, vedere [Azure AD B2C: registrare l'applicazione](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) . Quando si passa da una directory di Azure AD B2C all'altra nel portale di Azure, il pannello delle funzionalità B2C non rimane aperto nella maggior parte dei browser.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Come si personalizzano i messaggi di posta elettronica di verifica (il contenuto e il campo "Da:") inviati da Azure AD B2C?
È possibile usare la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md) per personalizzare il contenuto dei messaggi di posta elettronica di verifica. In particolare, è possibile personalizzare i due elementi di posta elettronica seguenti:

* **Logo del banner**: in basso a destra.
* **Colore di sfondo**: in alto.
  
    ![Screenshot di un messaggio di posta elettronica di verifica personalizzato](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma della posta elettronica contiene il nome del tenant B2C fornito al momento della creazione del tenant B2C. È possibile modificare il nome seguendo queste istruzioni:

* Accedere al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione.
* Accedere al tenant B2C.
* Fare clic sulla scheda **Configura** .
* Modificare il campo **Nome** nella sezione **Proprietà directory**.
* Fare clic su **Save** nella parte inferiore della pagina.

Attualmente non è possibile modificare il campo "Da:" del messaggio di posta elettronica. Se si è interessati a questa funzionalità e alla completa personalizzazione del corpo del messaggio di posta elettronica di verifica, votare la funzionalità su [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Come si esegue la migrazione di nomi utente, password e profili esistenti dal database personale in Azure AD B2C?
È possibile usare l'API Graph di Azure AD per creare lo strumento di migrazione. Per informazioni dettagliate, vedere l' [esempio relativo all'API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) . In futuro Microsoft fornirà vari strumenti e opzioni di migrazione pronti per l'uso.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quali sono i criteri password usati per gli account locali in Azure AD B2C?
I criteri password di Azure AD B2C per gli account locali si basano su quelli di Azure AD. I criteri di iscrizione, iscrizione o accesso e reimpostazione della password di Azure AD B2C usano un livello di complessità della password "avanzato" e senza scadenza delle password. Per altre informazioni dettagliate, vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>È possibile usare Azure AD Connect per eseguire la migrazione in Azure AD B2C delle identità utente archiviate in Active Directory locale?
No, Azure AD Connect non è progettato per funzionare con Azure AD B2C. In futuro Microsoft fornirà vari strumenti e opzioni di migrazione pronti per l'uso.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>L'applicazione può aprire le pagine di Azure AD B2C all'interno di un iFrame?
No. Per motivi di sicurezza, le pagine di Azure AD B2C non possono essere aperte in un iFrame.  Il servizio comunica con il browser per impedirlo.  L'uso di iFrame per le esperienze di gestione delle identità è sconsigliato dalla community della sicurezza in generale e dalla specifica OAUTH2 a causa del rischio di clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C è compatibile con i sistemi CRM come Microsoft Dynamics?
No, per il momento. L'integrazione di questi sistemi verrà implementata in futuro.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C è compatibile con SharePoint 2016 locale o versione precedente?
No, per il momento. Azure AD B2C non fornisce i token SAML 1.1 che i portali e le applicazioni e-commerce costruiscono sulla base delle esigenze di SharePoint in locale. Si noti che Azure AD B2C non è concepito per lo scenario di condivisione esterna con i partner di SharePoint. A questo scopo, vedere invece il blog su [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>È necessario usare Azure AD B2C o B2B per gestire le identità esterne?
Per altre informazioni su come applicare le funzionalità appropriate agli scenari relativi alle identità esterne, vedere l'articolo sulle [identità esterne](../active-directory/active-directory-b2b-compare-external-identities.md) .

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quali funzionalità di reporting e controllo offre Azure AD B2C? Offre le stesse funzionalità di Azure AD Premium?
No, Azure AD B2C non supporta lo stesso set di report di Azure AD Premium. Esistono tuttavia molte caratteristiche comuni.  
* I report degli accessi contengono un record per ogni accesso con dettagli ridotti.  
* I report di controllo sono disponibili nel portale di Azure selezionando Azure Active Directory > ATTIVITÀ > Log di controllo > B2C e applicando i filtri desiderati. Sono incluse sia le attività amministrative che quelle delle applicazioni. 
* Un report di utilizzo, che include il numero di utenti, il numero di accessi e il volume di autenticazioni MFA, è disponibile tramite l'[API per la creazione di report di utilizzo](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api).

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>È possibile localizzare l'interfaccia utente delle pagine servite da Azure AD B2C? Quali lingue sono supportate?
Attualmente, Azure AD B2C è ottimizzato solo per l'inglese. Si prevede di implementare le funzionalità di localizzazione appena possibile.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>È possibile usare l'URL personale nelle pagine di iscrizione e accesso servite da Azure AD B2C? È ad esempio possibile sostituire l'URL login.microsoftonline.com con login.contoso.com?
No, per il momento. Questa funzionalità verrà implementata in futuro. Si noti anche che la procedura di verifica del dominio personalizzato nella scheda **Domini** del tenant nel portale di Azure classico non eseguirà questa operazione.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Come si elimina il tenant di Azure AD B2C?
Attenersi a questa procedura per eliminare il tenant di Azure AD B2C:

* Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
* Passare ai pannelli **Applications** (Applicazioni), **Identiry providers** (Provider di identità) e **All policies** (Tutti i criteri) ed eliminare tutte le voci in ognuno di essi.
* Accedere ora al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Si tratta dello stesso account aziendale o dell'istituto d'istruzione o dello stesso account Microsoft usato per l'iscrizione ad Azure.
* Passare all'estensione di Active Directory a sinistra e fare clic sul nome del tenant B2C.
* Fare clic sulla scheda **Utenti** .
* Selezionare un utente alla volta, escludendo l'utente con il quale si è connessi attualmente, ovvero l'amministratore della sottoscrizione. Fare clic su **Elimina** nella parte inferiore della pagina, quindi su **SÌ** quando richiesto.
* Fare clic sulla scheda **Applicazioni** .
* Selezionare **Applications my company owns** (Applicazioni di proprietà dell'azienda) nel campo a discesa **Mostra** e fare clic sul segno di spunta.
* Nell'elenco verrà visualizzata un'applicazione denominata **b2c-extensions-app** . Fare clic su **Elimina** nella parte inferiore della pagina, quindi su **SÌ** quando richiesto.
* Passare di nuovo all'estensione di Active Directory e selezionare il tenant di B2C.
* Nella parte inferiore della pagina fare clic su **Elimina** . Seguire le istruzioni sullo schermo per completare il processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>È possibile ottenere Azure AD B2C come parte di Enterprise Mobility Suite?
No, Azure AD B2C è un servizio di Azure con pagamento in base al consumo e non fa parte di Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Come è possibile segnalare problemi relativi ad Azure AD B2C?
Vedere [Azure Active Directory B2C: Inviare richieste di supporto](active-directory-b2c-support.md).



