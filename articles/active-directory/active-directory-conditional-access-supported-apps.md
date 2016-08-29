
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
	ms.date="08/12/2016"
	ms.author="markvi"/>  


# Supporto dell'accesso condizionale per le applicazioni

Le regole di accesso condizionale sono supportate in applicazioni connesse di Azure Active Directory, applicazioni SaaS federate preintegrate, applicazioni che usano l'accesso Single Sign-On basato su password, applicazioni line-of-business e proxy dell'applicazione di Azure AD. Per un elenco dettagliato delle applicazioni in cui è possibile abilitare l'accesso condizionale, vedere [Servizi abilitati con l'accesso condizionale](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). L'accesso condizionale è ideale per le applicazioni desktop e per dispositivi mobili che usano un'autenticazione moderna. Questo argomento illustra le funzionalità supportate nelle versioni desktop e per dispositivi mobili di queste app.

Le applicazioni di questo tipo possono visualizzare le pagine di accesso al servizio Azure Active Directory. In questo modo, a un utente viene chiesto inline di eseguire l'autenticazione Multi-Factor Authentication oppure un utente finale visualizza un messaggio quando viene bloccato l'accesso. È importante comprendere quali applicazioni sono supportate nonché i passaggi che potrebbero essere necessari per la protezione di altri punti di ingresso.

## Applicazioni che utilizzano l'autenticazione moderna
Le applicazioni seguenti sono state testate con l'autenticazione Multi-Factor Authentication (MFA) e i criteri di posizione impostati sul servizio di destinazione.

| Applicazione | Servizio di destinazione | Piattaforma |
|--------------|-----------------|----------------------------------------------------------------|
| Outlook 2016 | Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
| Outlook 2013 (richiede l'abilitazione dell'autenticazione moderna)| Exchange |Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Skype for Business (con autenticazione moderna)|Exchange (accesso a Exchange per il calendario e la cronologia delle conversazioni)| Windows 10, Windows 8.1, Windows 7 |
|App Outlook Mobile|Exchange| iOS e Android |
|Office 2016; Word, Excel, SharePoint|SharePoint| Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
|Outlook 2013 (richiede l'abilitazione dell'autenticazione moderna)|SharePoint|Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Dynamics CRM|Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| App Yammer|Yammer| Windows Mobile 10, iOS, Android|
|App remote di Azure|Servizio app Azure Remote|Windows 10, Windows 8.1, Windows 7, Mac, iOS, Android|





Le applicazioni seguenti supportano criteri basati sul dispositivo impostati nel servizio di destinazione:

| Applicazione | Servizio di destinazione | Piattaforma |
| :--                                     | :--            | :--      |
| Posta elettronica/Calendario/Persone | Exchange | Windows 10, Windows Mobile 10 |
| Office universale: Word/Excel/PowerPoint | SharePoint | Windows 10, Windows Mobile 10 |
| Outlook 2016 | Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7 |
|Outlook 2013 (richiede l'abilitazione dell'autenticazione moderna) | Exchange | Windows 8.1, Windows 7 |


Le applicazioni seguenti non supportano criteri basati sul dispositivo impostati nel servizio di destinazione.

| Applicazione | Servizio di destinazione | Piattaforma |
| :--                                     | :--            | :--      |
| OneDrive for Business usando il client di sincronizzazione di nuova generazione (sia siti personali che del team) | SharePoint | Windows 10, Windows Mobile 10 |
| App Mie app | Qualsiasi | iOS, Android |


## Applicazioni che non utilizzano l'autenticazione moderna

Attualmente, l'accesso delle app che non utilizzano l'autenticazione moderna deve essere bloccato utilizzando altri metodi, poiché esse non sono regolate dall'accesso condizionale. Ciò vale soprattutto per l'accesso a Exchange e SharePoint, poiché le app di versioni precedenti sono state compilate utilizzando protocolli meno recenti.

## SharePoint

I protocolli legacy possono essere disabilitati in SharePoint, tramite il cmdlet Set-SPOTenant, che impedirà ai client di Office che utilizzano protocolli di autenticazione non moderni di accedere alle risorse di SharePoint Online.

**Comando di esempio**: `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## Exchange

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

<!---HONumber=AcomDC_0817_2016-->