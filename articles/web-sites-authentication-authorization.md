<properties 
	pageTitle="Autenticazione e autorizzazione di applicazioni line-of-business in Siti Web di Azure" 
	description="Informazioni sulle diverse opzioni di autenticazione e autorizzazione per applicazioni line-of-business che vengono distribuite in Siti Web di Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Autenticazione e autorizzazione degli utenti nelle applicazioni line-of-business in Siti Web di Azure #

[Siti Web di Azure](http://azure.microsoft.com/services/websites/) abilita scenari di applicazioni line-of-business aziendali grazie al supporto di Single Sign-On (SSO) per gli utenti che accedono all'applicazione dall'ambiente locale o dalla rete pubblica Internet. Può essere integrato con [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) o con il servizio token di sicurezza locale, ad esempio Active Directory Federation Services (ADFS), per autenticare gli utenti di Active Directory (AD) interni e autorizzarli correttamente.

## Autorizzazione e autenticazione senza attrito ##

Con pochi clic del pulsante, è possibile abilitare l'autenticazione e l'autorizzazione per un sito Web. La configurazione tramite caselle di controllo in ogni sito Web di Azure fornisce il controllo di accesso di base per il sito Web line-of-business. Questo risultato si ottiene applicando HTTPS e l'autenticazione a un tenant di Azure AD a propria scelta prima di concedere agli utenti l'accesso a tutto il contenuto del sito Web. Per altre informazioni, vedere la pagina relativa all'[autenticazione e all'autorizzazione di Siti Web di Azure](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Questa funzionalità è attualmente disponibile in versione di anteprima.

## Implementare manualmente l'autenticazione e l'autorizzazione ##

In molti scenari è possibile personalizzare il comportamento di autenticazione e autorizzazione dell'applicazione, ad esempio una pagina di accesso e disconnessione, una logica di autorizzazione personalizzata, un comportamento di applicazione multi-tenant e così via. In questi casi, è preferibile configurare l'autenticazione e l'autorizzazione manualmente per una maggiore flessibilità delle funzionalità. Di seguito vengono illustrate due opzioni principali.  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/) - È possibile implementare l'autenticazione e autorizzazione per un sito Web con Azure AD. L'uso di Azure AD come provider di identità presenta le seguenti caratteristiche:
	-	Supporta i protocolli di autenticazione più diffusi, ad esempio [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) e [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Per l'elenco completo dei protocolli supportati, vedere [Protocolli di autenticazione di Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	Consente di usare un provider di identità solo di Azure senza alcuna infrastruttura locale.
	-	Consente inoltre di configurare la sincronizzazione della directory con Active Directory locale (gestito in locale).
	-	Azure AD con sincronizzazione della directory dal dominio Active Directory locale facilita l'uso di SSO in un sito Web quando gli utenti di Active Directory accedono dalla rete Intranet e da Internet. Dalla rete Intranet, gli utenti di Active Directory possono automaticamente accedere al sito Web tramite l'autenticazione integrata. Da Internet, gli utenti di Active Directory possono accedere al sito Web usando le credenziali di Windows.
	-	Fornisce l'accesso SSO a [tutte le applicazioni supportate da Azure AD](http://azure.microsoft.com/marketplace/active-directory/), tra cui Azure, Office 365, Dynamics CRM Online, Windows InTune e migliaia di applicazioni cloud non Microsoft. 
	-	Azure AD delega la gestione delle applicazioni [relying party](http://en.wikipedia.org/wiki/Relying_party) a ruoli non di amministratore, mentre l'accesso di un'applicazione a dati di directory riservate deve comunque essere configurato dagli amministratore globali.
	-	Invia un set generico di tipi di attestazione per tutte le applicazioni relying party. Per l'elenco dei tipi di attestazione, vedere [Token e tipi di attestazioni supportati](http://msdn.microsoft.com/library/azure/dn195587.aspx). Le attestazioni non sono personalizzabili.
	-	[API di Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) consente l'accesso di un'applicazione ai dati di directory in Azure AD.
-	[Servizio token di sicurezza locale, ad esempio ADFS](../web-sites-dotnet-lob-application-adfs/) - È possibile implementare l'autenticazione e l'autorizzazione per un sito Web con un servizio token di sicurezza locale come ADFS. L'uso di ADFS in locale presenta le seguenti caratteristiche:
	-	La topologia di ADFS deve essere distribuita in locale, con un sovraccarico di costo e gestione.
	-	Questa soluzione è indicata se la politica aziendale richiede che i dati Active Directory vengano memorizzati in locale.
	-	Solo gli amministratori di ADFS possono configurare [trust di relying party e regole attestazioni](http://technet.microsoft.com/library/dd807108.aspx).
	-	Consente di gestire le [attestazioni](http://technet.microsoft.com/library/ee913571.aspx) in base alle singole applicazioni.
	-	È necessario disporre di una soluzione separata per l'accesso ai dati di Active Directory locale attraverso il firewall aziendale.


<!--HONumber=42-->
