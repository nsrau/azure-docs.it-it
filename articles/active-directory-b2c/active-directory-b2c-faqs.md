<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Domande frequenti | Microsoft Azure"
	description="Domande frequenti su Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Domande frequenti

Questa pagina include le risposte a domande frequenti relative all'anteprima di Azure Active Directory (AD) B2C. Controllarla costantemente per eventuali aggiornamenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### È possibile usare le funzionalità di Azure AD B2C nel tenant Azure AD esistente per dipendenti aziendali?

Attualmente le funzionalità di Azure AD B2C non possono essere attivate nel tenant di Azure AD esistente. È consigliabile creare un tenant separata per usare le funzionalità di Azure AD B2C, ad esempio per gestire i propri utenti.

### È possibile usare Azure AD B2C per fornire l'accesso a social networking (Facebook e Google+) in Office 365?

Non è possibile usare Azure AD B2C con Office 365 e in generale per fornire l'autenticazione per app SaaS (O365, Salesforce, Workday e così via). Fornisce solo la gestione di identità e accessi per applicazioni Web e per dispositivi mobili destinate agli utenti e non è applicabile a scenari di dipendenti o partner.

### Che cosa sono gli "account locali" in Azure AD B2C? In che cosa differiscono dagli "account aziendali o dell'istituto di istruzione" in Azure AD?

In un tenant di Azure AD, ogni utente nel tenant (tranne quelli con account Microsoft esistenti) accede tramite un indirizzo di posta elettronica nel formato `<xyz>@<tenant domain>`, dove `<tenant domain>` è uno dei domini verificati nel tenant o il dominio `<...>.onmicrosoft.com` iniziale. Questo tipo di account è un "account aziendale o dell'istituto di istruzione", detto anche "account dell'organizzazione".

In un tenant di Azure AD B2C, la maggior parte delle app richiede l'accesso dell'utente con un indirizzo di posta elettronica arbitrario, ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com). Questo tipo di account è un "account locale". Attualmente sono supportati anche nomi utente arbitrari (semplici stringhe) come account locali (ad esempio, joe, bob, sarah o jim). Nel servizio Azure AD B2C è possibile scegliere uno di questi due "tipi" di account locale.

### Quali provider di identità di social networking sono attualmente supportati? Per quali provider è previsto il supporto in futuro?

Sono attualmente supportati Facebook, Google+, LinkedIn e Amazon. Verrà aggiunto il supporto per altri provider di identità di social networking noti, in base alle esigenze dei clienti.

### È possibile configurare ambiti per raccogliere altre informazioni sugli utenti da diversi provider di identità di social networking?

No, ma questa funzionalità verrà implementata in futuro. Gli ambiti predefiniti usati per il gruppo di provider di identità di social networking supportato sono:

- Facebook: email
- Google+: email
- Amazon: profile
- LinkedIn: r\_emailaddress r\_basicprofile

### Per essere compatibile con Azure AD B2C, un'applicazione deve essere eseguita in Azure?

No, l'applicazione può essere ospitata ovunque (nel cloud o in locale). Tutto quello di cui ha bisogno per interagire con Azure AD B2C consiste nella possibilità di inviare e ricevere richieste HTTP su endpoint accessibili pubblicamente.

### Nel caso di più tenant Azure AD B2C, come è possibile gestirli nel portale di Azure?

Ogni tenant Azure AD B2C dispone di uno specifico pannello delle funzionalità B2C nel portale di Azure. Per informazioni su come passare al pannello delle funzionalità B2C di un tenant specifico nel portale di Azure, leggere [questa sezione](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade). Quando si passa da una directory di Azure AD B2C all'altra nel portale di Azure, il pannello delle funzionalità B2C non rimane aperto nella maggior parte dei browser.

### Come si personalizzano i messaggi di posta elettronica di verifica (ad esempio, i campi del contenuto e del mittente) inviati da Azure AD B2C?

Usare la [funzionalità di personalizzazione della società](./active-directory/active-directory-add-company-branding.md) per personalizzare il contenuto dei messaggi di posta elettronica di verifica. Il campo mittente può essere modificato tramite il team di supporto.

### Come si esegue la migrazione di nomi utente, password e profili esistenti dal database personale in Azure AD B2C?

È possibile usare l'API Graph di Azure AD (vedere [qui](active-directory-b2c-devquickstarts-graph-dotnet.md) l'esempio) per creare lo strumento di migrazione. In futuro Microsoft fornirà vari strumenti e opzioni di migrazione pronti per l'uso.

### Quali sono i criteri password usati per gli account locali in Azure AD B2C?

I criteri password di Azure AD B2C per gli account locali si basano su quelli di Azure AD. Azure AD B2C usa il livello "avanzato" di complessità della password e password senza scadenza. Per altri dettagli, vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

### È possibile usare Azure AD Connect per eseguire la migrazione in Azure AD B2C delle identità utente archiviate in Active Directory locale?

No, Azure AD Connect non è progettato per funzionare con Azure AD B2C. In futuro Microsoft fornirà vari strumenti e opzioni di migrazione pronti per l'uso.

### Azure AD B2C è compatibile con i sistemi CRM come Microsoft Dynamics?

No, per il momento. L'integrazione di questi sistemi verrà implementata in futuro.

### Azure AD B2C è compatibile con SharePoint On-Premises 2016 o versione successiva?

No, per il momento. AD B2C di Azure non fornisce i token SAML 1.1 che le applicazioni del portale e-commerce costruiscono sulla base delle esigenze di SP in locale. Si noti che AD B2C di Azure non è utilizzato per lo scenario di condivisione del partner esterno Sharepoint; vedere invece [AD B2B di Azure](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### Quali funzionalità di reporting e controllo fornisce Azure AD B2C? Funziona come Azure AD Premium?

No, Azure AD B2C non supporta lo stesso set di report di Azure AD Premium. Per Azure AD B2C verranno presto rilasciate API di reporting e controllo.

### È possibile localizzare l'interfaccia utente delle pagine servite da Azure AD B2C? Quali lingue sono supportate?

Attualmente, Azure AD B2C è ottimizzato solo per l'inglese. Si prevede di implementare le funzionalità di localizzazione appena possibile.

### È possibile usare l'URL personale nelle pagine di iscrizione e accesso servite da Azure AD B2C? Ad esempio, è possibile sostituire l'URL login.microsoftonline.com con login.contoso.com?

No, per il momento. Questa funzionalità verrà implementata in futuro. Si noti inoltre che la "verifica" del proprio dominio nella scheda **Domini** del tenant nel portale di Azure classico non eseguirà questa operazione.

### È possibile ottenere Azure AD B2C come parte di Enterprise Mobility Suite (EMS)?

No, Azure AD B2C è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS.

### Come è possibile segnalare problemi relativi ad Azure AD B2C?

Leggere l'[argomento relativo al supporto](active-directory-b2c-support.md) per Azure AD B2C.

### Quando sarà disponibile a livello generale Azure AD B2C?

Non è attualmente possibile fornire una stima riguardo alla data di disponibilità generale.

## Altre informazioni

È possibile anche leggere l'[articolo sulle limitazioni, le restrizioni e i vincoli correnti della versione di anteprima](active-directory-b2c-limitations.md).

<!---HONumber=AcomDC_0107_2016-->