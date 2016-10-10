
<properties
	pageTitle="Accesso condizionale - Applicazioni supportate | Microsoft Azure"
	description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."
    services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="09/26/2016"
	ms.author="markvi"/>  


# Supporto dell'accesso condizionale per le applicazioni

Le regole di accesso condizionale sono supportate in applicazioni connesse di Azure Active Directory, applicazioni SaaS federate preintegrate, applicazioni che usano l'accesso Single Sign-On basato su password, applicazioni line-of-business e proxy dell'applicazione di Azure AD. Per un elenco dettagliato delle applicazioni in cui è possibile abilitare l'accesso condizionale, vedere [Servizi abilitati con l'accesso condizionale](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). L'accesso condizionale è ideale per le applicazioni desktop e per dispositivi mobili che usano un'autenticazione moderna. Questo argomento illustra le funzionalità supportate nelle versioni desktop e per dispositivi mobili di queste app.

Le applicazioni di questo tipo possono visualizzare le pagine di accesso al servizio Azure Active Directory. In questo modo, a un utente viene chiesto inline di eseguire l'autenticazione Multi-Factor Authentication oppure un utente finale visualizza un messaggio quando viene bloccato l'accesso. L'autenticazione moderna è necessaria anche per consentire al dispositivo di eseguire l'autenticazione con Azure AD, in modo che vengano valutati i criteri di accesso condizionale basato sul dispositivo.

È importante comprendere quali applicazioni sono supportate nonché i passaggi che potrebbero essere necessari per la protezione di altri punti di ingresso.

## Applicazioni che utilizzano l'autenticazione moderna
Le applicazioni seguenti supportano l'accesso condizionale quando accedono a Office 365 e ad altre applicazioni di servizio connesse ad Azure AD:

| Servizio di destinazione | Piattaforma | Applicazione |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|App Mail/Calendar/People, Outlook 2016, Outlook 2013 (con l'autenticazione moderna abilitata), Skype for Business (con l'autenticazione moderna)|
|Office 365 Exchange Online| Windows 7, Windows 8.1, |Outlook 2016, Outlook 2013 (con l'autenticazione moderna abilitata), Skype for Business (con l'autenticazione moderna)|
|Office 365 Exchange Online|iOS, Android| App Outlook Mobile|
|Office 365 Exchange Online|Mac OSX| Outlook 2016 solo per MFA/posizione; il supporto dei criteri basati sul dispositivo è previsto per il futuro, il supporto per Skype for Business è previsto per il futuro|
|Office 365 SharePoint Online|Windows 10| App Office 2016, app Office Universal, Office 2013 (con l'autenticazione moderna abilitata), il supporto dell'app OneDrive for Business (NGSC o client di sincronizzazione di nuova generazione) è previsto per il futuro, il supporto dei gruppi di Office è previsto per il futuro, il supporto dell'app SharePoint è previsto per il futuro|
|Office 365 SharePoint Online|Windows 7, Windows 8.1,|App Office 2016, Office 2013 (con l'autenticazione moderna abilitata), app OneDrive for Business (client di sincronizzazione Groove)|
|Office 365 SharePoint Online|iOS, Android| App Office per dispositivi mobili |
|Office 365 SharePoint Online|Mac OSX| App Office 2016 solo per MFA/posizione; il supporto per i criteri basati sui dispositivi è previsto per il futuro|
|Office 365 Yammer|Windows 10, iOS e Android | App Office Yammer|
|Dynamics CRM|Windows 10, 7, 8.1, iOS e Android | Dynamics CRM|
|Servizio PowerBI|Windows 10, 7, 8.1, iOS e Android | App PowerBI|
|Servizio app Azure Remote|Windows 10, 7, 8.1, iOS e Android, Mac OSX |App Azure Remote|
|Qualsiasi servizio app Mie app|Android e iOS|Qualsiasi servizio app Mie app |


## Applicazioni che non utilizzano l'autenticazione moderna

Attualmente, l'accesso delle app che non utilizzano l'autenticazione moderna deve essere bloccato utilizzando altri metodi, poiché esse non sono regolate dall'accesso condizionale. Ciò vale soprattutto per l'accesso a Exchange e SharePoint, poiché le app di versioni precedenti sono state compilate utilizzando protocolli meno recenti.

## Office 365 SharePoint Online

I protocolli legacy possono essere disabilitati in SharePoint, tramite il cmdlet Set-SPOTenant, che impedirà ai client di Office che utilizzano protocolli di autenticazione non moderni di accedere alle risorse di SharePoint Online.

**Comando di esempio**: `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## Office 365 Exchange Online

In Exchange esistono due categorie principali di revisione dei protocolli e occorre selezionare il criterio corretto per la propria organizzazione:

1. Exchange ActiveSync. Per impostazione predefinita, i criteri di accesso condizionale per l'autenticazione MFA e i criteri di posizione non vengono applicati per Exchange ActiveSync. Ciò consente di proteggere l'accesso configurando i criteri di Exchange ActiveSync in modo diretto oppure bloccando Exchange ActiveSync con le regole di AD FS.
2. Protocolli legacy. I protocolli legacy possono essere bloccati in AD FS. Così facendo, verrà bloccato l'accesso ai client di Office meno recenti, come Office 2013 senza l'autenticazione moderna abilitata e le versioni precedenti.


### Regole di AD FS di esempio
Le seguenti regole consentono di impedire ai protocolli legacy di accedere ad AD FS in due configurazioni comuni.

### Opzione 1: consentire Exchange ActiveSync e solo le applicazioni legacy nella intranet

Applicando le tre regole seguenti al trust della relying party di AD FS per la piattaforma di identità di Microsoft Office 365, il traffico di Exchange ActiveSync sarà consentito, insieme al traffico di autenticazione moderna e del browser. Le applicazioni legacy verranno bloccate dalla rete Extranet.

Regola 1

    `@RuleName = “Allow all intranet traffic”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Regola 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Regola 3

	@RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### Opzione 2: consentire Exchange ActiveSync e bloccare le applicazioni legacy 
Applicando le tre regole seguenti al trust della relying party di AD FS per la piattaforma di identità di Microsoft Office 365, il traffico di Exchange ActiveSync sarà consentito, insieme al traffico di autenticazione moderna e del browser. Le applicazioni legacy verranno bloccate da qualunque percorso.

Regola 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regola 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regola 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

<!---HONumber=AcomDC_0928_2016-->