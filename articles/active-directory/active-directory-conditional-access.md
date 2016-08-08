<properties
	pageTitle="Accesso condizionale di Azure Active Directory | Microsoft Azure"  
    description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."  
    services="active-directory" 
	keywords="accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale" 
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
	ms.date="07/26/2016"
	ms.author="markvi"/>


# Accesso condizionale di Azure Active Directory   
  
La protezione dell'accesso alle risorse aziendali è importante per ogni organizzazione. Con l'avvento dei servizi cloud e dei dispositivi mobili, le modalità in cui gli utenti accedono alle risorse aziendali sono cambiate significativamente. È quindi necessario un nuovo approccio alla sicurezza.
  
## Vantaggi dell'accesso condizionale  

Le funzionalità di controllo dell'accesso condizionale in Azure Active Directory consentono alle società di proteggere con facilità le proprie risorse sul cloud e locali. I criteri di accesso condizionale possono essere usati per contribuire alla protezione dal rischio di credenziali rubate e sottoposte a phishing, in quanto richiedono l'autenticazione a più fattori, e contribuiscono alla protezione dei dati aziendali richiedendo un dispositivo gestito di Intune che concede l'accesso ai servizi riservati.



## Requisiti per le licenze

L'accesso condizionale è una funzionalità di [Azure AD Premium](http://www.microsoft.com/identity). Tutti gli utenti che accedono a un'applicazione a cui sono applicati i criteri di accesso condizionale devono avere una licenza di Azure AD Premium. Per altre informazioni sull'utilizzo, vedere [Unlicensed User report](https://aka.ms/utc5ix) (Report Utente senza licenza).





## Applicazione del controllo di accesso condizionale  

Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Una volta soddisfatti questi requisiti di accesso, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condizioni
  
- **Appartenenza a gruppi**: è possibile controllare il livello di accesso consentito a un utente in base alla rispettiva appartenenza a un gruppo.

- **Posizione**: è possibile usare la posizione dell'utente per attivare MFA e i controlli di blocco quando un utente non si trova in un rete attendibile.

- **Piattaforma del dispositivo**: è possibile usare il tipo di dispositivo della piattaforma, ad esempio iOS, Android, Windows Mobile e Windows, come condizioni per l'applicazione dei criteri.

- **Dispositivo abilitato**: lo stato di abilitazione/disabilitazione del dispositivo viene convalidato durante la valutazione dei criteri del dispositivo. Se si disabilita un dispositivo perso o rubato nella directory, non sarà più possibile usarlo per rispettare i requisiti dei criteri.

- **Accesso e rischi per l'utente**: in Azure AD Identity Protection sono disponibili criteri di rischio dell'accesso condizionale, che assicurano una protezione avanzata in base a eventi di rischio e attività di accesso anomale.


## Controlli
   
- **Multi-Factor Authentication (MFA)**: è possibile richiedere l'autenticazione avanzata con MFA. Il servizio MFA può essere fornito da Azure MFA o da un provider di MFA locale tramite AD FS. L'autenticazione MFA contribuisce alla protezione delle risorse dall'accesso da parte di un utente non autorizzato che ha ottenuto l'accesso al nome utente e alla password di un utente valido.

- **Blocco**: il blocco dell'accesso può essere applicato in base a condizioni quali la posizione dell'utente. È ad esempio possibile bloccare l'accesso quando un utente non si trova in una rete attendibile.

- **Dispositivi registrati/conformi**: a livello di dispositivo è possibile configurare criteri per concedere l'accesso ai soli dispositivi registrati e conformi a Gestione di dispositivi mobili. Per verificare che il dispositivo sia registrato e conforme viene utilizzato Microsoft Intune. L'accesso condizionale a livello di dispositivo assicura quindi che solo i dispositivi conformi ai criteri di Gestione di dispositivi mobili siano autorizzati ad accedere.
 

## Applicazioni

- Il livello di accesso configurabile mediante questi criteri può essere applicato ad applicazioni e servizi sul cloud o locali. I criteri vengono applicati direttamente al sito Web o al servizio. I criteri vengono quindi applicati per l'accesso al browser, oltre che alle applicazioni che accedono al servizio. L'elenco di criteri del servizio applicabili è disponibile qui.


  
## Accesso condizionale - Mappa dei contenuti  
La mappa dei contenuti seguente elenca i documenti a cui è necessario fare riferimento per ottenere altre informazioni sull'abilitazione dell'accesso condizionale nella distribuzione corrente.


### MFA e criteri relativi alla posizione

- [Guida introduttiva all'accesso condizionale ad Azure AD](active-directory-conditional-access-azuread-connected-apps.md) per app connesse in base a gruppo, posizione e criteri MFA
- [Tipi di applicazione supportati](active-directory-conditional-access-supported-apps.md)


### Criteri relativi ai dispositivi

[Contribuire alla protezione dei dati richiedendo Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### Protezione delle risorse in base ai rischi di accesso

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informazioni aggiuntive

- [Domande frequenti sull'accesso condizionale](active-directory-conditional-faqs.md)
- [Riferimento tecnico](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->