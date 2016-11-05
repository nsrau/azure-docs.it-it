---
title: Accesso condizionale di Azure Active Directory | Microsoft Docs
description: Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2016
ms.author: markvi

---
# Accesso condizionale di Azure Active Directory
La protezione dell'accesso alle risorse aziendali è importante per ogni organizzazione. Con l'avvento dei servizi cloud e dei dispositivi mobili, le modalità in cui gli utenti accedono alle risorse aziendali sono cambiate significativamente. La proliferazione di dispositivi personali e aziendali richiede un nuovo approccio all'accesso alle risorse aziendali e alla sicurezza.

## Vantaggi dell'accesso condizionale
Le funzionalità di controllo di accesso condizionale in Azure Active Directory consentono alle società di proteggere con facilità le proprie risorse sul cloud e locali. I criteri di accesso condizionale offrono una protezione contro il rischio di furto e phishing delle credenziali con l'autenticazione a più fattori. È inoltre possibile applicare criteri di accesso condizionale per proteggere i dati aziendali, in modo che solo i dispositivi registrati in un sistema di gestione di dispositivi mobili, come Microsoft Intune, abbiano accesso a servizi riservati.

## Prerequisiti
L'accesso condizionale di Azure Active Directory è una funzionalità di [Azure AD Premium](http://www.microsoft.com/identity). Tutti gli utenti che accedono a un'applicazione a cui sono applicati i criteri di accesso condizionale devono avere una licenza di Azure AD Premium. Per altre informazioni sull'utilizzo, vedere [Unlicensed User report](https://aka.ms/utc5ix) (Report Utente senza licenza).

## Applicazione del controllo di accesso condizionale
Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Una volta soddisfatti questi requisiti di accesso, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.

![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condizioni
* **Appartenenza ai gruppi**: è possibile controllare il livello di accesso consentito a un utente in base alla rispettiva appartenenza a un gruppo.
* **Posizione**: la posizione dell'utente può essere usata per attivare l'autenticazione a più fattori (MFA) e bloccare i controlli quando un utente non si trova su una rete attendibile.
* **Piattaforma del dispositivo**: è possibile usare il tipo di piattaforma del dispositivo, ad esempio iOS, Android, Windows Mobile e Windows, come condizioni per l'applicazione dei criteri.
* **Dispositivo abilitato**: lo stato di abilitazione/disabilitazione del dispositivo viene convalidato durante la valutazione dei criteri del dispositivo. Se si disabilita un dispositivo perso o rubato nella directory, non sarà più possibile usarlo per rispettare i requisiti dei criteri.
* **Accesso e rischi per l'utente**: in [Azure AD Identity Protection](active-directory-identityprotection.md) sono disponibili criteri di rischio per l'accesso condizionale che assicurano una protezione avanzata in base a eventi di rischio e attività di accesso anomale.

## Controlli
* **Multi-Factor Authentication (MFA)**: è possibile richiedere l'autenticazione avanzata con MFA. Il servizio MFA può essere fornito da Azure MFA o da un provider di MFA locale tramite Active Directory Federation Server (AD FS). MFA contribuisce alla protezione delle risorse dall'accesso da parte di un utente non autorizzato che ha ottenuto l'accesso alle credenziali di un utente valido.
* **Blocco**: condizioni come la posizione dell'utente possono essere applicate per bloccare l'accesso dell'utente. È ad esempio possibile bloccare l'accesso quando un utente non si trova in una rete attendibile.
* **Dispositivi conformi**: a livello di dispositivo, è possibile impostare criteri che applicano condizioni quali consentire l'accesso solo ai computer aggiunti a un dominio o ai dispositivi mobili conformi e registrati in un'applicazione di gestione di dispositivi mobili (MDM). Ad esempio, Microsoft Intune può essere usato per controllare la conformità dei dispositivi e segnalarla ad Azure Active Directory affinché ne tenga conto durante l'accesso delle applicazioni. Per istruzioni dettagliate su come usare Microsoft Intune per proteggere le applicazioni e i dati, vedere [Proteggere le applicazioni e i dati con Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). È inoltre possibile applicare la protezione dei dati per i dispositivi smarriti o rubati tramite Microsoft Intune. Per altre informazioni, vedere [Proteggere i dati con la cancellazione completa o selettiva tramite Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## Applicazioni
* Il livello di accesso configurabile mediante questi criteri può essere applicato ad applicazioni e servizi sul cloud o locali. I criteri vengono applicati direttamente al sito Web o al servizio. I criteri vengono quindi applicati per l'accesso al browser, oltre che alle applicazioni che accedono al servizio. L'elenco di criteri del servizio applicabili è disponibile qui.

## Accesso condizionale basato su dispositivo
È inoltre possibile limitare l'accesso alle applicazioni di dispositivi registrati con Azure AD conformi a determinate condizioni. L'accesso condizionale basato sul dispositivo protegge le risorse aziendali dal rischio che gli utenti vi accedano da:

* Dispositivi sconosciuti/non gestiti
* Dispositivi che non soddisfano i criteri di sicurezza definiti dall'organizzazione.

I criteri possono essere impostati in base ai requisiti seguenti:

* **Dispositivi aggiunti a un dominio**: è possibile impostare criteri per limitare l'accesso ai soli dispositivi aggiunti a un dominio di Active Directory locale e registrati con Azure AD. Questo criterio si applica ai computer desktop, portatili o tablet aziendali Windows che appartengono a un dominio di Active Directory locale registrati con Azure AD. Per altre informazioni su come configurare la registrazione automatica dei dispositivi aggiunti a un dominio con Azure AD, vedere l'articolo [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivi conformi**: è possibile impostare criteri per limitare l'accesso ai dispositivi contrassegnati come **conformi** nella directory tramite il sistema di gestione. Con questo criterio, l'accesso viene limitato ai soli dispositivi conformi ai criteri di sicurezza, ad esempio l'applicazione della crittografia dei file. Questo criterio può essere utilizzato per limitare l'accesso dai dispositivi seguenti:
  
  * **Dispositivi aggiunti a un dominio di Windows** gestiti tramite System Center Configuration Manager Current Branch in una configurazione ibrida.
  * **Dispositivi Windows 10 mobili o personali** gestiti tramite Microsoft Intune o un sistema di Gestione dei dispositivi mobili (MDM) di terze parti supportato.
  * **Dispositivi iOS e Android** gestiti tramite Microsoft Intune.

Gli utenti che intendono accedere ad applicazioni protette da criteri di accesso condizionale basato sul dispositivo devono farlo da dispositivi conformi a questo criterio. Se si tenta di accedere da un dispositivo che non soddisfa i criteri richiesti, l'accesso viene negato.

Per informazioni su come configurare i criteri CA in base al dispositivo in Azure AD, vedere l'articolo su [come configurare i criteri di accesso condizionale basato sul dispositivo per il controllo di accesso delle applicazioni connesse ad Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## Indice notizie per l'accesso condizionale di Azure Active Directory
La mappa dei contenuti seguente elenca i documenti a cui è necessario fare riferimento per ottenere altre informazioni sull'abilitazione dell'accesso condizionale nella distribuzione corrente.

### MFA e criteri relativi alla posizione
* [Guida introduttiva all'accesso condizionale ad Azure AD per app connesse in base a gruppo, posizione e criteri MFA](active-directory-conditional-access-azuread-connected-apps.md)
* [Tipi di applicazione supportati](active-directory-conditional-access-supported-apps.md)

### Accesso condizionale basato su dispositivo
* [Come impostare criteri di accesso condizionale in base al dispositivo per controllare gli accessi delle applicazioni connesse ad Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Correzione a disposizione dell'utente per accedere ad applicazioni protette con accesso condizionale basato su dispositivo di Azure AD](active-directory-conditional-access-device-remediation.md)
* [Proteggere i dati sui dispositivi smarriti o rubati tramite Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### Protezione delle risorse in base ai rischi di accesso
[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informazioni aggiuntive
* [Domande frequenti sull'accesso condizionale](active-directory-conditional-faqs.md)
* [Riferimento tecnico](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0928_2016-->