<properties
	pageTitle="Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory | Microsoft Azure"  
    description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."  
    services="active-directory" 
	keywords="accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory  
  
La protezione dell'accesso alle risorse aziendali è importante per ogni organizzazione. Con l'avvento dei servizi cloud e dei dispositivi mobili, le modalità in cui gli utenti accedono alle risorse aziendali sono cambiate significativamente. Sono quindi necessarie modifiche alla strategia di protezione delle risorse aziendali.
  
## Vantaggi dell'accesso condizionale  
 Le funzionalità di controllo dell'accesso condizionale in Azure Active Directory consentono alle società di proteggere con facilità le proprie risorse sul cloud e locali. Azure Active Directory offre la soluzione ottimale per, ad esempio, "impedire l'accesso alle risorse tramite una password rubata" o "richiedere un dispositivo integro e gestito per l'accesso al contenuto aziendale".

## Applicazione del controllo di accesso condizionale  
 Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Una volta soddisfatti questi requisiti di accesso, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Accesso alle risorse basato sull'utente
  
- **Attributi utente**: a livello di attributi utente è possibile applicare criteri per assicurare che solo gli utenti autorizzati possano accedere alle risorse aziendali.
- **Appartenenza a gruppi di un utente**: è anche possibile controllare il livello di accesso consentito a un utente in base alla rispettiva appartenenza a uno o più gruppi.
- **Multi Factor Authentication (MFA)**: è anche possibile applicare criteri che richiedono all'utente di autenticare la propria identità mediante un sistema di autenticazione a più fattori. Ad esempio, è possibile imporre a un utente di confermare un PIN in un telefono cellulare personale per assicurare un livello aggiuntivo di sicurezza. L'autenticazione MFA protegge le risorse dall'accesso da parte di un utente non autorizzato che ha ottenuto l'accesso al nome utente e alla password di un utente valido.
- **Accesso e rischi per l'utente**: in Azure AD Identity Protection sono disponibili criteri di rischio dell'accesso condizionale, che assicurano una protezione avanzata in base a eventi di rischio e attività di accesso anomale.
 

## Accesso condizionale basato su dispositivo 

**Dispositivi registrati**: a livello di dispositivo è possibile configurare criteri per concedere l'accesso ai soli dispositivi noti o con gestione di dispositivi mobili registrati. Per verificare che il dispositivo sia registrato e conforme viene utilizzato Microsoft Intune. L'accesso condizionale a livello di dispositivo assicura che solo i dispositivi conformi ai criteri definiti, ad esempio l'applicazione della crittografia dei file in un dispositivo, siano autorizzati ad accedere. L'uso di soluzioni MDM consente anche di assicurare che i dati aziendali in un dispositivo perso/rubato possano essere cancellati in modalità remota.
  

Il livello di accesso configurabile mediante questi criteri può essere applicato a risorse sul cloud o locali. Le risorse sul cloud possono essere app come Office 365 e app SaaS di terze parti. Possono anche essere app line-of-business ospitate sul cloud.
  
## Accesso condizionale - Mappa dei contenuti  
La mappa dei contenuti seguente elenca i documenti a cui è necessario fare riferimento per ottenere altre informazioni sull'abilitazione dell'accesso condizionale nella distribuzione corrente.


| Scenario | Articoli |
|------------------------------------------------------|----------|
| Proteggere le risorse in base all'utente o al livello di autenticazione |[Guida introduttiva all'accesso condizionale con app connesse di Azure AD in base al gruppo, alla posizione e alla sensibilità dell'applicazione](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Tipi di applicazione supportati](active-directory-conditional-access-supported-apps.md)|
| Protezione dei dati aziendali su dispositivi persi/rubati |[Proteggere i dati con la cancellazione completa o selettiva tramite Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|Protezione delle risorse in base ai rischi di accesso |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| Informazioni aggiuntive |[Domande frequenti sull'accesso condizionale](active-directory-conditional-faqs.md)<br><br>[Documentazione tecnica](active-directory-conditional-access-technical-reference.md) |

<!---HONumber=AcomDC_0629_2016-->