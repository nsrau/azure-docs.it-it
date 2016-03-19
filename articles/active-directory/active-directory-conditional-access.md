<properties
	pageTitle="Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory | Microsoft Azure"  
    description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."  
    services="active-directory" 
	keywords="accesso alle app, accesso sicuro alle risorse aziendali, criteri di accesso condizionale" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/10/2016"
	ms.author="femila"/>


# Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory  
  
La protezione dell'accesso alle risorse aziendali è importante per ogni organizzazione. Con l'avvento dei servizi cloud e dei dispositivi mobili, le modalità in cui gli utenti accedono alle risorse aziendali sono cambiate significativamente. Sono quindi necessarie modifiche alla strategia di protezione delle risorse aziendali.
  
## Vantaggi dell'accesso condizionale  
 Le funzionalità di controllo dell'accesso condizionale in Azure Active Directory consentono alle società di proteggere con facilità le proprie risorse sul cloud e locali. Azure Active Directory offre la soluzione ottimale per, ad esempio, "impedire l'accesso alle risorse tramite una password rubata" o "richiedere un dispositivo integro e gestito per l'accesso al contenuto aziendale".

## Applicazione del controllo di accesso condizionale  
 Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Accesso alle risorse basato sull'utente
  
- **Attributi utente**: a livello di attributi utente è possibile applicare criteri per assicurare che solo gli utenti autorizzati possano accedere alle risorse aziendali.   
- **Appartenenza a gruppi di un utente**: è anche possibile controllare il livello di accesso consentito a un utente in base alla rispettiva appartenenza a uno o più gruppi.   
- **Multi Factor Authentication (MFA)**: è anche possibile applicare criteri che richiedono all'utente di autenticare la propria identità mediante un sistema di autenticazione a più fattori. Ad esempio, è possibile imporre a un utente di confermare un PIN in un telefono cellulare personale per assicurare un livello aggiuntivo di sicurezza. L'autenticazione MFA protegge le risorse dall'accesso da parte di un utente non autorizzato che ha ottenuto l'accesso al nome utente e alla password di un utente valido. 

## Accesso condizionale basato su dispositivo 

- **Dispositivi registrati**: a livello di dispositivo è possibile configurare criteri per fare in modo che solo i dispositivi registrati o noti siano autorizzati all'accesso. È anche possibile usare una soluzione di gestione di dispositivi mobili (MDM, Mobile Device Management), ad esempio Microsoft Intune, per assicurarsi che solo i dispositivi gestiti possano accedere alle risorse. L'accesso condizionale a livello di dispositivo assicura che solo i dispositivi conformi ai criteri definiti, ad esempio l'applicazione di un PIN in un dispositivo, siano autorizzati ad accedere. L'uso di soluzioni MDM consente anche di assicurare che i dati aziendali in un dispositivo perso/rubato possano essere cancellati in modalità remota.  
- **Criteri dei dispositivi**: è anche possibile configurare criteri per limitare l'accesso solo sulla base di singole applicazioni. Si può anche configurare il livello di accesso in base alla posizione fisica del dispositivo, ovvero al fatto che la richiesta provenga da una rete aziendale nota o dall'esterno.  

Il livello di accesso configurabile mediante questi criteri può essere applicato a risorse sul cloud o locali. Le risorse sul cloud possono essere app come Office 365 e app SaaS di terze parti. Possono anche essere app line-of-business ospitate sul cloud.
  
## Accesso condizionale - Mappa dei contenuti  
La mappa dei contenuti seguente elenca i documenti a cui è necessario fare riferimento per ottenere altre informazioni sull'abilitazione dell'accesso condizionale nella distribuzione corrente.


| Scenario | Articoli |
|------------------------------------------------------|----------|
| Protezione delle risorse aziendali da attacchi di phishing |[Introduzione all'accesso condizionale alle app SaaS di AAD con MFA e la Extranet](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Accesso condizionale alle app di Azure AD](active-directory-conditional-access-technical-reference.md)<br><br>[Come configurare MFA](multi-factor-authentication-get-started-cloud.md)<br><br>[Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Considerazioni su MFA per i singoli utenti](multi-factor-authentication-end-user-manage-settings.md)<br><br>[MFA dalla Extranet](multi-factor-authentication-get-started-adfs-cloud.md)|
| Protezione dei dati aziendali su dispositivi persi/rubati |[Servizio Registrazione dispositivo](active-directory-conditional-access-device-registration-overview.md)<br><br> [Registrazione di dispositivi aggiunti al dominio](active-directory-azureadjoin-setup.md)<br><br> [Uso di dispositivi registrati in Azure AD per l'accesso condizionale locale](active-directory-conditional-access-on-premises-setup.md) <br><br>[Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Configurare la registrazione automatica per i dispositivi Windows 8.1 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Criteri di accesso condizionale dei dispositivi per i servizi di Office 365](active-directory-conditional-access-device-policies.md)|
| Informazioni aggiuntive |[Domande frequenti sull'accesso condizionale](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0218_2016-->