<properties 
	pageTitle="Procedure consigliate sulla sicurezza per l'uso di Azure MFA" 
	description="Questo documento illustra le procedure consigliate per l'uso di Azure MFA con account Azure" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Procedure consigliate sulla sicurezza per usare Azure Multi-Factor Authentication con account Azure AD

Se si vuole migliorare il processo di autenticazione, l'autenticazione a più fattori è la scelta migliore per la maggior parte delle organizzazioni. Azure Multi-Factor Authentication (MFA) consente alle aziende di soddisfare i requisiti di sicurezza e conformità, offrendo un'esperienza di accesso semplice per gli utenti. Questo articolo illustra alcune procedure consigliate che è opportuno considerare quando si pianifica l'adozione di Azure MFA.

## Procedure consigliate per Azure Multi-Factor Authentication nel cloud
Per fornire a tutti gli utenti l'autenticazione a più fattori e sfruttare i vantaggi offerti dalle funzionalità estese di Azure Multi-Factor Authentication, è necessario abilitare Azure Multi-Factor Authentication per tutti gli utenti. A questo scopo, usare uno degli elementi seguenti:

- Azure AD Premium o Enterprise Mobility Suite
- Provider di Multi-Factor Authentication

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Il primo passaggio consigliato per l'adozione di Azure MFA nel cloud con Azure AD Premium o Enterprise Mobility Suite consiste nell'assegnare licenze agli utenti. Azure Multi-Factor Authentication fa parte di queste famiglie di prodotti e di conseguenza l'organizzazione non ha l'esigenza di avere elementi aggiuntivi per estendere la funzionalità di autenticazione a più fattori a tutti gli utenti.
 
Quando si configura l'autenticazione a più fattori, tenere in considerazione quanto segue:

- Non è necessario creare un provider di Multi-Factor Authentication. Azure AD Premium ed Enterprise Mobility Suite sono inclusi in Azure Multi-Factor Authentication. La creazione di un provider di autenticazione potrebbe comportare una doppia fatturazione.
- Azure AD Connect è un requisito solo se si sincronizza l'ambiente Active Directory locale con una directory di Azure AD. Se si usa solo una directory di Azure AD non sincronizzata con un'istanza locale di Active Directory, Azure AD Connect non è necessario.


### Provider di Multi-Factor Authentication

![Provider di Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se Azure AD Premium o Enterprise Mobility Suite non è disponibile, il primo passaggio consigliato per l'adozione di Azure MFA nel cloud consiste nel creare un provider di Multi-Factor Authentication. Anche se MFA è disponibile per impostazione predefinita per gli amministratori globali che hanno Azure Active Directory, quando si distribuisce MFA per l'organizzazione è necessario estendere la funzionalità di autenticazione a più fattori a tutti gli utenti e per eseguire questa operazione è necessario un provider di Multi-Factor Authentication. Quando si seleziona il provider di autenticazione, è necessario selezionare una directory e considerare quanto segue:

- Non è necessaria una directory di Azure AD per creare un Provider Multi-Factor Authentication.
- Se si vuole estendere l'autenticazione a più fattori a tutti gli utenti e/o si vuole consentire agli amministratori globali di sfruttare i vantaggi offerti da funzionalità come il portale di gestione, i messaggi di saluto personalizzati e i report, sarà necessario associare il provider di Multi-Factor Authentication a una directory di Azure AD.
- Le funzionalità DirSync o AAD Sync sono necessarie solo se si esegue la sincronizzazione dell'ambiente Active Directory locale con una directory di Azure AD. Se si utilizza solo una directory di Azure AD che non è sincronizzata con un'istanza locale di Active Directory, non è necessario disporre delle funzionalità DirSync o AAD Sync.
- Se si dispone di Azure AD Premium o di Enterprise Mobility Suite, non è necessario creare un Provider Multi-Factor Authentication. È sufficiente assegnare una licenza a un utente per poter iniziare ad attivare MFA per gli utenti.
- Assicurarsi di scegliere il modello di utilizzo corretto per l'azienda, cioè per autenticazione o per utente abilitato, perché dopo aver selezionato il modello di utilizzo non sarà possibile modificarlo.

### Account utente
Quando si abilita MFA per gli utenti, gli account utente possono essere in uno dei tre stati principali: disabilitato, abilitato o applicato. Usare le linee guida che seguono per assicurarsi di usare l'opzione più appropriata per la propria distribuzione:

- Quando lo stato dell'utente è impostato su disabilitato, l'utente non usa l'autenticazione a più fattori. Questo è lo stato predefinito.
- Quando lo stato dell'utente è impostato su abilitato, l'utente è abilitato ma non ha completato il processo di registrazione. All'utente verrà chiesto di completare la procedura all'accesso successivo. Questa impostazione non influisce sulle app non basate su browser. Tutte le app continueranno a funzionare fino al completamento del processo di registrazione.
- Quando lo stato dell'utente è impostato su applicato, l'utente può avere o meno completato la registrazione. Se la procedura di registrazione è stata completata, l'utente utilizzerà la modalità Multi-Factor Authentication. In caso contrario, all'utente verrà richiesto di completare la procedura di registrazione all'accesso successivo. Questa impostazione influisce sulle app non basate su browser. Queste app non funzioneranno finché non verranno create e usate password per le app.
- Usare il modello di notifica utente disponibile nell'articolo [Introduzione ad Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-get-started-cloud.md) per inviare agli utenti un messaggio di posta elettronica riguardante l'adozione di MFA.

### Supporto

Poiché quasi tutti gli utenti sono abituati a usare le password solo per l'autenticazione, è importante che l'azienda renda consapevoli tutti gli utenti in merito a questo processo. Questa consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti MFA. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

- Assicurarsi che il personale di supporto tecnico sappia come gestire gli scenari in cui l'app per dispositivi mobili o il telefono non riceva una notifica o una chiamata telefonica e in cui, per questo motivo, l'utente non riesca ad accedere. In questi casi può essere abilitata un'opzione bypass monouso per consentire all'utente di autenticarsi una sola volta "ignorando" l'autenticazione a più fattori. Il bypass è temporaneo e scade dopo il numero di secondi specificato.
- Se necessario, è possibile sfruttare la funzionalità Indirizzi IP attendibili di Azure MFA. Questa funzionalità consente agli amministratori di un tenant gestito o federato di ignorare l'autenticazione a più fattori per gli utenti che accedono dalla rete Intranet locale dell'azienda. Le funzionalità sono disponibili per i tenant di Azure AD che dispongono di licenze Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.


## Procedure consigliate per Azure Multi-Factor Authentication locale
Se la società ha deciso di sfruttare la propria infrastruttura per abilitare MFA, sarà necessario distribuire un server Azure Multi-Factor Authentication locale. I componenti del server MFA sono illustrati nel diagramma seguente:

![Provider di Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/server.png) *Non installato per impostazione predefinita* Installato ma non abilitato per impostazione predefinita


Il server Azure Multi-Factor Authentication può essere usato per proteggere risorse cloud e locali a cui accedono gli account Azure AD. Questa operazione è possibile solo usando la federazione. Ciò significa che è necessario avere AD FS federato con un tenant di Azure AD. Quando si configura il server Multi-Factor Authentication, tenere in considerazione quanto segue:

- Se non si proteggono le risorse di Azure AD tramite Active Directory Federation Services, il primo fattore di autenticazione viene eseguito in locale tramite AD FS e il secondo fattore viene eseguito in locale rispettando l'attestazione.
- Non è un requisito che il Server Azure multi-Factor Authentication essere installata nel server federativo ADFS tuttavia l'Adapter per l'autenticazione a più fattori per ADFS deve essere installato in un'esecuzione di ADFS di Windows Server 2012 R2. È possibile installare il server in un computer diverso, purché è una versione supportata e installare separatamente la scheda ADFS nel server federativo ADFS. Vedere la procedura seguente per istruzioni sull'installazione dell'adapter separatamente.
- L'installazione guidata scheda ADFS di multi-Factor Authentication consente di creare un gruppo di sicurezza denominato PhoneFactor Admins in Active Directory e quindi aggiunge l'account del servizio ADFS del servizio federativo a questo gruppo. È consigliabile verificare nel controller di dominio che effettivamente creato il gruppo PhoneFactor Admins e account del servizio AD FS è un membro di questo gruppo. Se necessario, aggiungere l'account del servizio ADFS manualmente al gruppo PhoneFactor Admins sul controller di dominio.

### Portale per gli utenti
Questo portale viene eseguito in un sito Web Internet Information Server (IIS), che consente l'uso delle funzionalità self-service e fornisce un set completo di funzionalità di amministrazione degli utenti. Per configurare questo componente, usare le linee guida riportate di seguito:

- È necessario IIS 6 o versione successiva
- ASP.NET v2.0.507207 deve essere installato e registrato
- Questo server può essere distribuito in una rete perimetrale.



### Password dell'app
Se l'organizzazione è federata e usa SSO con Azure AD e si prevede di usare Azure MFA, tenere presente quanto segue quando si usano password dell'app, ricordando però che queste informazioni si applicano solo in caso di federazione (SSO):

- La password dell'app viene verificata da Azure AD e di conseguenza ignora la federazione. La federazione viene usata solo quando si configura la password dell'app.
- Per gli utenti federati (SSO) le password saranno archiviate nell'ID dell'organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync in tempo reale. La disabilitazione o l'eliminazione dell'account può richiedere fino a 3 ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione della password dell'app in Azure AD.
- Le impostazioni locali di controllo dell'accesso client non vengono rispettate dalla password dell'app
- Per la password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale
- Per il client Microsoft Lync 2013 è necessaria ulteriore formazione per gli utenti finali.
- Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa Multi-Factor Authentication con i client, a seconda della posizione in cui viene eseguita l'autenticazione. Per i client che si autenticano con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione. Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.
- Per impostazione predefinita, gli utenti non possono creare password dell'app, ma se la società lo richiede o se è necessario consentire agli utenti di creare password dell'app in alcuni scenari, assicurarsi che sia selezionata l'opzione Consentire agli utenti di creare password dell'app per accedere alle applicazioni non basate su browser.

## Considerazione aggiuntive
Usare l'elenco seguente per comprendere alcune considerazioni e procedure consigliate aggiuntive per ogni componente che sarà distribuito in locale:

Metodo|Descrizione
:------------- | :------------- | 
[Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md)|Informazioni sull'impostazione di Azure Multi-Factor Authentication con ADFS.
[Autenticazione RADIUS](multi-factor-authentication-get-started-server-radius.md)| Informazioni sull'installazione e la configurazione del Server di autenticazione a più fattori di Azure con RADIUS.
[Autenticazione IIS](multi-factor-authentication-get-started-server-iis.md)|Informazioni sull'installazione e la configurazione del Server di autenticazione a più fattori di Azure con IIS.
[Autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md)| Informazioni sull'installazione e la configurazione del Server di autenticazione a più fattori di Azure con l'autenticazione di Windows.
[Autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informazioni sull'installazione e la configurazione del Server di autenticazione a più fattori di Azure con l'autenticazione LDAP.
[Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](multi-factor-authentication-get-started-server-rdg.md)| Informazioni sull'installazione e configurazione del Server di autenticazione a più fattori di Azure con Gateway Desktop remoto utilizzando RADIUS.
[Sincronizzazione con Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Informazioni sull'installazione e la configurazione della sincronizzazione tra Active Directory e il Server di autenticazione a più fattori di Azure.
[Distribuzione del servizio Web App Mobile di Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-webservice.md)|Informazioni sull'installazione e configurazione del servizio web del server Azure di autenticazione a più fattori.
[Configurazione VPN avanzata con Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md)|Informazioni sulla configurazione di dispositivi VPN Cisco ASA, Citrix Netscaler e Juniper/Pulse Secure tramite LDAP o RADIUS. 


## Risorse aggiuntive
Anche se questo articolo evidenzia alcune procedure consigliate per Azure MFA, sono disponibili anche altre risorse che è possibile usare quando si pianifica la distribuzione di MFA. L'elenco seguente include alcuni articoli importanti che possono risultare utili durante questo processo:

- [Report in Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [Esperienza di installazione per Azure multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
- [Domande frequenti su Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0810_2016-->