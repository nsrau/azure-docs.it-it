<properties 
	pageTitle="Usare Active Directory per l'autenticazione nel servizio app di Azure" 
	description="Informazioni sulle differenti opzioni di autenticazione e autorizzazione per applicazioni line-of-business distribuite ad App Web del servizio app di Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Usare Active Directory per l'autenticazione nel servizio app di Azure #

[App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) consente scenari di applicazione line-of-business aziendali supportando la modalità Single Sign-On (SSO) di utenti che accedono all'applicazione dall'ambiente locale o Internet pubblico. Può essere integrato con [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) o con il servizio token di sicurezza (STS) locale, come Active Directory Federation Services (AD FS), per autenticare gli utenti Active Directory (AD) interni e autorizzarli correttamente.

## Autenticazione e autorizzazione senza problemi ##

Con pochi clic è possibile abilitare l'autenticazione e l'autorizzazione per l'app Web. La configurazione dello stile della casella di testo in ciascuna app Web di Azure fornisce il controllo di accesso di base dell'app Web line-of-business. A tale scopo, è necessario rafforzare HTTPS e l'autenticazione per un tenant di Azure AD di propria scelta prima di garantire agli utenti l'accesso a tutti i contenuti delle app Web. Per ulteriori informazioni, vedere [Autenticazione/Autorizzazione app Web](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE]Questa funzionalità è attualmente in anteprima.

## Implementare manualmente l'autenticazione e l'autorizzazione ##

In molti scenari, si richiede la personalizzazione del comportamento di autenticazione e autorizzazione dell'applicazione, ad esempio, di una pagina di accesso e disconnessione, della logica di autorizzazione personalizzata, del comportamento dell'applicazione multitenant e così via. In tali casi, potrebbe essere più conveniente configurare manualmente l'autenticazione e l'autorizzazione per una maggiore flessibilità delle proprie funzionalità. Di seguito sono disponibili due opzioni principali

-	[Azure AD](web-sites-dotnet-lob-application-azure-ad.md): è possibile implementare l'autenticazione e l'autorizzazione per l'app Web con Azure AD. Usando Azure AD come provider di identità, le caratteristiche sono le seguenti:
	-	supporto dei popolari protocolli di autenticazione, come [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) e [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Per l'elenco completo dei protocolli supportati, vedere [Protocolli di autenticazione di Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	È possibile utilizzare un provider di identità solo di Azure senza infrastruttura locale.
	-	È possibile inoltre configurare la sincronizzazione della directory con un'istanza di AD locale (gestita in locale).
	-	Azure AD con la sincronizzazione della directory dal dominio AD locale abilita un utilizzo SSO uniforme dell'app Web quando gli utenti AD accedono da Intranet e Internet. Dalla intranet, gli utenti AD possono accedere automaticamente all'app Web tramite Autenticazione integrata. Da Internet, gli utenti AD possono accedere all'app Web utilizzando le credenziali Windows.
	-	Fornisce l'accesso SSO a [tutte le applicazioni supportate da Azure AD](/marketplace/active-directory/), tra cui Azure, Office 365, Dynamics CRM Online, Microsoft InTune e migliaia di applicazioni cloud non Microsoft. 
	-	Azure AD delega la gestione delle applicazioni [relying party](http://en.wikipedia.org/wiki/Relying_party) a ruoli di non amministratore, mentre l'accesso dell'applicazione ai dati della directory sensibili deve essere configurato dagli amministratori globali.
	-	Invia un set di scopi generali dei tipi di attestazioni per tutte le applicazioni relying party. Per l'elenco dei tipi di attestazioni, vedere [Tipi di attestazioni e token supportati](http://msdn.microsoft.com/library/azure/dn195587.aspx). Le attestazioni non sono personalizzabili.
	-	L'[API di Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) abilita l'accesso all'applicazione per i dati della directory in Azure AD.
-	[Servizio token di sicurezza (STS) locale, quale ADFS](../web-sites-dotnet-lob-application-adfs/): è possibile implementare l'autenticazione e l'autorizzazione per l'app Web con un STS locale come ADFS. L'uso di ADFS locale presenta le seguenti caratteristiche:
	-	La topologia ADFS deve essere distribuita in locale, con sovraccarico di costi e gestione.
	-	È preferibile che i criteri aziendali richiedano che i dati di AD vengano archiviati in locale.
	-	Solo gli amministratori di ADFS possono configurare i [trust e le regole di attestazione relying party](http://technet.microsoft.com/library/dd807108.aspx).
	-	Possono gestire le [attestazioni](http://technet.microsoft.com/library/ee913571.aspx) su ogni singola applicazione.
	-	È necessario disporre di soluzioni separate per accedere ai dati AD in locale tramite il firewall aziendale.

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->