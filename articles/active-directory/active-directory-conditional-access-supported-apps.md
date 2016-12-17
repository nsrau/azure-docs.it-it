---
title: Applicazioni che usano regole di accesso condizionale in Azure Active Directory | Documentazione Microsoft
description: Grazie al controllo di accesso condizionale, Azure Active Directory verifica condizioni specifiche quando esegue l&quot;autenticazione di un utente e consente l&quot;accesso all&quot;applicazione.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 87c73981c74fc763fd1aec6c283e934c77008441
ms.openlocfilehash: 94a212b88d080510ad8a96bb40bb2c48d7f88a4f


---
# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Applicazioni che usano regole di accesso condizionale in Azure Active Directory
Le regole di accesso condizionale sono supportate in applicazioni connesse di Azure AD (Azure Active Directory), applicazioni SaaS (Software as a Service) federate preintegrate, applicazioni che usano l'accesso SSO (Single Sign-On) basato su password, applicazioni line-of-business e applicazioni che usano il proxy dell'applicazione di Azure AD. Per un elenco dettagliato delle applicazioni per cui è possibile abilitare l'accesso condizionale, vedere [Servizi abilitati con l'accesso condizionale](active-directory-conditional-access-technical-reference.md). L'accesso condizionale è ideale sia per le applicazioni desktop che per i dispositivi mobili che usano un'autenticazione moderna. In questo articolo viene illustrato il funzionamento dell'accesso condizionale nelle app desktop e in quelle per dispositivi mobili.

È possibile usare le pagine di accesso di Azure AD nelle applicazioni che usano l'autenticazione moderna. In una pagina di accesso all'utente viene richiesto di effettuare un'autenticazione a più fattori. Se l'accesso dell'utente risulta bloccato, viene visualizzato un messaggio di errore. L'autenticazione moderna è necessaria per consentire al dispositivo di eseguire l'autenticazione con Azure AD, in modo che vengano valutati i criteri di accesso condizionale basato su dispositivo.

È importante sapere quali applicazioni possono usare le regole di accesso condizionale. È inoltre necessario conoscere i passaggi che possono essere necessari per la protezione di altri punti di ingresso dell'applicazione.

## <a name="applications-that-use-modern-authentication"></a>Applicazioni che usano l'autenticazione moderna
> [!NOTE]
> Se in Azure AD è presente un criterio di accesso condizionale equivalente a un analogo criterio di Office 365, configurare entrambi i criteri di accesso condizionale. Questo scenario si applica, ad esempio, ai criteri di accesso condizionale per Exchange Online o SharePoint Online.
>
>

Le applicazioni seguenti supportano l'accesso condizionale per Office 365 e altre applicazioni di servizio connesse ad Azure AD:

| Servizio di destinazione | Piattaforma | Applicazione |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |App Mail/Calendar/People, Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna) |
| Office 365 Exchange Online |Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (con l'autenticazione moderna), Skype for Business (con l'autenticazione moderna) |
| Office 365 Exchange Online |iOS, Android |App Outlook Mobile |
| Office 365 Exchange Online |Mac OS X |Outlook 2016 solo per l'autenticazione a più fattori e la posizione; il supporto dei criteri basati su dispositivo è pianificato per il futuro, il supporto per Skype for Business è pianificato per il futuro |
| Office 365 SharePoint Online |Windows 10 |App Office 2016, Universal Office, Office 2013 (con l'autenticazione moderna), il supporto per l'app OneDrive for Business (NNGSC (Next Generation Sync Client)) è pianificato per il futuro, il supporto dei gruppi di Office è pianificato per il futuro, il supporto dell'app SharePoint è pianificato per il futuro |
| Office 365 SharePoint Online |Windows 8.1, Windows 7 |App Office 2016, Office 2013 (con l'autenticazione moderna), app OneDrive for Business (client di sincronizzazione Groove) |
| Office 365 SharePoint Online |iOS, Android |App Office per dispositivi mobili |
| Office 365 SharePoint Online |Mac OS X |App Office 2016 solo per l'autenticazione a più fattori e la posizione; il supporto dei criteri basati su dispositivo è pianificato per il futuro |
| Office 365 Yammer |Windows 10, iOS e Android |App Office Yammer |
| Dynamics CRM |Windows 10, Windows 8.1, Windows 7, iOS e Android |Dynamics CRM |
| Servizio PowerBI |Windows 10, Windows 8.1, Windows 7, iOS e Android |App PowerBI |
| Servizio app Azure Remote |Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X |App Azure Remote |
| Qualsiasi servizio app Mie app |Android e iOS |Qualsiasi servizio app Mie app |

## <a name="applications-that-do-not-use-modern-authentication"></a>Applicazioni che non utilizzano l'autenticazione moderna
Attualmente è necessario usare altri metodi per bloccare l'accesso ad app che non usano l'autenticazione moderna. Le regole per le app che non usano l'autenticazione moderna non vengono applicate in base all'accesso condizionale. Questo vale soprattutto per l'accesso a Exchange e SharePoint. La maggior parte delle versioni meno recenti delle app usa protocolli di controllo dell'accesso non moderni.

### <a name="control-access-in-office-365-sharepoint-online"></a>Controllo dell'accesso in Office 365 SharePoint Online
È possibile disabilitare protocolli legacy per l'accesso a SharePoint usando il cmdlet Set-SPOTenant. Usare questo cmdlet per impedire ai client di Office che usano protocolli di autenticazione non moderni di accedere alle risorse di SharePoint Online.

**Comando di esempio**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Controllo dell'accesso in Office 365 Exchange Online
Exchange offre due categorie principali di protocolli. Esaminare le opzioni seguenti e quindi selezionare il criterio più adatto alle esigenze dell'organizzazione.

* **Exchange ActiveSync**. Per impostazione predefinita, i criteri di accesso condizionale per l'autenticazione a più fattori e i criteri di posizione non vengono applicati per Exchange ActiveSync. È necessario proteggere l'accesso a questi servizi configurando i criteri di Exchange ActiveSync in modo diretto oppure bloccando Exchange ActiveSync con le regole di AD FS (Active Directory Federation Services).
* **Protocolli legacy.**. È possibile bloccare protocolli legacy con AD FS. In questo modo viene bloccato l'accesso ai client di Office meno recenti, ad esempio Office 2013 senza l'autenticazione moderna abilitata e le versioni precedenti di Office.

### <a name="use-ad-fs-to-block-legacy-protocol"></a>Usare AD FS per bloccare protocolli legacy
È possibile usare le regole di esempio seguenti per bloccare l'accesso da parte di protocolli legacy a livello di AD FS. Scegliere una delle due configurazioni più comuni.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opzione 1: consentire l'accesso a Exchange ActiveSync e alle app legacy, ma solo nella rete Intranet
Applicando le tre regole seguenti al trust della relying party di AD FS per la piattaforma di identità di Microsoft Office 365, hanno accesso il traffico di Exchange ActiveSync e il traffico di autenticazione moderna e del browser. Le applicazioni legacy vengono bloccate dalla rete Extranet.

##### <a name="rule-1"></a>Regola 1
    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regola 2
    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regola 3
    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opzione 2: consentire l'accesso a Exchange ActiveSync e bloccare le applicazioni legacy
Applicando le tre regole seguenti al trust della relying party di AD FS per la piattaforma di identità di Microsoft Office 365, hanno accesso il traffico di Exchange ActiveSync e il traffico di autenticazione moderna e del browser. Le app legacy vengono bloccate da qualunque percorso.

##### <a name="rule-1"></a>Regola 1
    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regola 2
    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regola 3
    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");



<!--HONumber=Nov16_HO3-->


