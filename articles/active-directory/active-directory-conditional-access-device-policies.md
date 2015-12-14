<properties
	pageTitle="Criteri di accesso condizionale dei dispositivi per i servizi di Office 365| Microsoft Azure"
	description="Informazioni dettagliate sul modo in cui le condizioni basate sul dispositivo controllano l'accesso ai servizi di Office 365. Mentre gli Information Worker vogliono accedere a servizi Office 365 come Exchange e SharePoint Online in azienda o a scuola dai propri dispositivi personali, per l'amministratore IT è importante che l'accesso sia sicuro. Gli amministratori IT possono effettuare il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli Information Worker che usano dispositivi compatibili di accedere ai servizi."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>
# Criteri di accesso condizionale dei dispositivi per i servizi di Office 365

Al termine "accesso condizionale" sono associate molte condizioni, ad esempio l'utente autenticato con più fattori, il dispositivo autenticato, il dispositivo compatibile e così via. Questo argomento si concentra principalmente sulle condizioni basate sul dispositivo per controllare l'accesso ai servizi di Office 365. Mentre gli Information Worker vogliono accedere a servizi Office 365 come Exchange e SharePoint Online al lavoro o a scuola dai propri dispositivi personali, per l'amministratore IT è importante che l'accesso sia sicuro. Gli amministratori IT possono effettuare il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli Information Worker che usano dispositivi conformi di accedere ai servizi. I criteri di accesso condizionale per Office 365 possono essere configurati dal portale di accesso condizionale di Intune.

Azure Active Directory applica criteri di accesso condizionale per proteggere l'accesso ai servizi di Office 365. Un amministratore di tenant può creare criteri di accesso condizionale che impediscano a un utente di un dispositivo non conforme di accedere a un servizio di Office 365. Prima di poter accedere al servizio, l'utente deve rispettare i criteri dei dispositivi applicati della società. In alternativa, l'amministratore può creare criteri che prevedano semplicemente la registrazione dei dispositivi degli utenti per accedere a un servizio di Office 365. I criteri possono essere applicati a tutti gli utenti di un'organizzazione o solo ad alcuni gruppi di destinazione ed estesi nel tempo con l'aggiunta di altri gruppi di destinazione.

La registrazione dei dispositivi degli utenti nel servizio Registrazione dispositivo di Azure Active Directory costituisce un prerequisito per l'applicazione dei criteri per i dispositivi. È possibile scegliere di abilitare l'autenticazione a più fattori (MFA) per registrare i dispositivi nel servizio Registrazione dispositivo di Azure Active Directory. MFA è consigliabile per il servizio Registrazione dispositivo di Azure Active Directory. Quando MFA è abilitata, agli utenti che registrano i dispositivi nel servizio Registrazione dispositivo di Azure Active Directory viene richiesta l'autenticazione a due fattori.

##Come funzionano i criteri di accesso condizionale

Quando un utente richiede l'accesso a un servizio di Office 365 da una piattaforma supportata, Azure Active Directory autentica l'utente e il dispositivo da cui viene avviata la richiesta e concede l'accesso al servizio solo quando l'utente è conforme ai criteri impostati per il servizio. Agli utenti che non hanno registrato il proprio dispositivo vengono fornite le istruzioni necessarie per eseguire la registrazione e ottenere la conformità per accedere ai servizi di Office 365 aziendali. Agli utenti di dispositivi iOS e Android verrà richiesto di eseguire la registrazione con l'applicazione Portale aziendale. Quando un utente registra il proprio dispositivo, questo viene registrato in Azure Active Directory per la conformità e la gestione dei dispositivi. I clienti devono usare il servizio Registrazione dispositivo di Azure Active Directory in combinazione con Microsoft Intune per abilitare la gestione dei dispositivi mobili per il servizio di Office 365. La registrazione del dispositivo è un prerequisito per l'accesso degli utenti ai servizi di Office 365 quando sono applicati i criteri per i dispositivi.

Dopo la registrazione, il dispositivo diventa attendibile. Azure Active Directory fornisce l'accesso Single Sign-On per accedere alle applicazioni aziendali e applica i criteri di accesso condizionale per concedere l'accesso a una servizio non solo la prima volta che viene richiesto, ma a ogni richiesta di rinnovo dell'accesso. L'accesso ai servizi viene negato in caso di modifica delle credenziali di accesso, di furto o smarrimento del dispositivo o di mancato rispetto dei criteri al momento della richiesta di rinnovo.

## Considerazioni sulla distribuzione:
È necessario usare il servizio Registrazione dispositivo di Azure Active Directory per la registrazione del dispositivo.

Quando gli utenti devono essere autenticati in locale, è necessario Active Directory Federation Services (AD FS) 1.0 o versioni successive. Multi-Factor Authentication per l'aggiunta all'area di lavoro non riesce quando il provider di identità non supporta l'autenticazione a più fattori. Ad esempio, AD FS 2.0 non supporta l'autenticazione a più fattori. Prima di abilitare MFA nel servizio Registrazione dispositivo di Azure Active Directory, l'amministratore del tenant deve assicurarsi che l'istanza locale di AD FS supporti MFA e che sia abilitato un metodo MFA valido. Ad esempio, AD FS in Windows Server 2012 R2 offre funzionalità MFA. Prima di abilitare MFA nel servizio Registrazione dispositivo di Azure Active Directory, è necessario abilitare anche un altro metodo di autenticazione (MFA) valido. Per altre informazioni sui metodi MFA supportati in AD FS, vedere Configurare altri metodi di autenticazione per AD FS.

## Domande frequenti

D: Quali sono i criteri di esclusione predefiniti per le piattaforme per dispositivi non supportate?

R: Attualmente i criteri di accesso condizionale sono applicati in modo selettivo per gli utenti che usano dispositivi iOS e Android. Per impostazione predefinita, i criteri di accesso condizionale per dispositivi iOS e Android non incidono sulle applicazioni in altre piattaforme per dispositivi. L'amministratore tenant può comunque decidere di ignorare i criteri globali per disabilitare l'accesso per gli utenti su piattaforme non supportate. Secondo la roadmap, i criteri di accesso condizionale verranno estesi agli utenti su altre piattaforme, tra cui Windows.

D: Quando è previsto che i criteri di accesso condizionale per i servizi di Office 365 vengano estesi alle app basate su browser, ad esempio OWA e SharePoint basato su browser?

R: Attualmente l'accesso condizionale ai servizi di Office 365 è limitato alle applicazioni avanzate su dispositivo. Secondo la roadmap, i criteri di accesso condizionale verranno estesi agli utenti che accedono ai servizi tramite browser.

<!---HONumber=AcomDC_1203_2015-->