---
title: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory | Microsoft Docs
description: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57bc2ca38b5166cfba39fb20254e169ce016ea12
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706322"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory

## <a name="general-faq"></a>Domande frequenti generali

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>D: Di recente è stato registrato un dispositivo. Perché non viene visualizzato nelle informazioni dell'utente all'interno del portale di Azure? O perché il proprietario del dispositivo è contrassegnato come N/d per i dispositivi aggiunti a Azure Active Directory ibrido (Azure AD)?

**R:** I dispositivi ibridi di Windows 10 aggiunti ad Azure AD non vengono visualizzati tra i **Dispositivi utente**.
Usare la visualizzazione **Tutti i dispositivi** nel portale di Azure. In alternativa, è possibile usare un cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Sotto **Dispositivi utente** vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido. 
- Tutti i dispositivi non Windows 10 o Windows Server 2016.
- Tutti i dispositivi non Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>D: Ricerca per categorie conoscere lo stato di registrazione del dispositivo del client?

**R:** Passare a **Tutti i dispositivi** nel portale di Azure. Cercare il dispositivo usando l'ID dispositivo. Controllare il valore nella colonna Tipo di join. In alcuni casi, è possibile che il dispositivo sia stato reimpostato o che ne sia stata ricreata l'immagine. È quindi importante controllare anche lo stato di registrazione del dispositivo all'interno del dispositivo:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire `dsregcmd.exe /status`.
- Per le versioni del sistema operativo di livello inferiore, eseguire `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**R:** Per informazioni sulla risoluzione dei problemi, vedere gli articoli seguenti:
- [Risoluzione dei problemi relativi ai dispositivi tramite il comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>D: Viene visualizzata la registrazione del dispositivo nelle informazioni dell'utente all'interno del portale di Azure. E lo stato viene visualizzato come registrato nel dispositivo. Sono configurati correttamente per l'uso dell'accesso condizionale?

**R:** Lo stato di join del dispositivo, illustrato da **DeviceID**, deve corrispondere allo stato in Azure ad e soddisfare i criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Richiedi i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>D: Perché gli utenti visualizzano un messaggio di errore che informa che l'organizzazione ha eliminato il dispositivo o che l'organizzazione ha disabilitato il dispositivo nei dispositivi Windows 10?

**R:** Nei dispositivi Windows 10 aggiunti o registrati con Azure AD, viene emesso un [token di aggiornamento primario (PRT)](concept-primary-refresh-token.md) che abilita Single Sign-on. La validità del PRT è basata sull'validaity del dispositivo stesso. Questo messaggio viene visualizzato dagli utenti se il dispositivo viene eliminato o disabilitato in Azure AD senza avviare l'azione dal dispositivo stesso. Un dispositivo può essere eliminato o disabilitato in Azure AD uno degli scenari seguenti: 

- L'utente disabilita il dispositivo dal portale app personali. 
- Un amministratore (o utente) Elimina o Disabilita il dispositivo nella portale di Azure o tramite PowerShell
- Solo Azure AD ibrido aggiunto: Un amministratore rimuove la OU dei dispositivi dall'ambito di sincronizzazione risultante dall'eliminazione dei dispositivi da Azure AD

Vedere di seguito il modo in cui è possibile rettificare queste azioni.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>D: Il dispositivo è stato disabilitato o eliminato nel portale di Azure o tramite Windows PowerShell. Ma lo stato locale sul dispositivo indica che è ancora registrato. Cosa devo fare?

**R:** Questa operazione viene eseguita per prassi. In questo caso, il dispositivo non ha accesso alle risorse nel cloud. Gli amministratori possono eseguire questa azione per i dispositivi obsoleti, smarriti o rubati per impedire accessi non autorizzati. Se questa azione è stata eseguita involontariamente, sarà necessario riabilitare o registrare nuovamente il dispositivo come descritto di seguito.

- Se il dispositivo è stato disabilitato in Azure AD, un amministratore con privilegi sufficienti può abilitarlo dal portale di Azure AD  
  > [!NOTE]
  > Se si sincronizzano i dispositivi con Azure AD Connect, i dispositivi ibridi Azure AD aggiunti verranno riabilitati automaticamente durante il ciclo di sincronizzazione successivo. Quindi, se è necessario disabilitare un dispositivo ibrido Azure AD aggiunto, è necessario disabilitarlo dall'istanza locale di AD

 - Se il dispositivo viene eliminato in Azure AD, è necessario registrare di nuovo il dispositivo. Per ripetere la registrazione, è necessario eseguire un'azione manuale sul dispositivo. Per istruzioni sulla ripetizione della registrazione in base allo stato del dispositivo, vedere di seguito. 

      Per ripetere la registrazione dei dispositivi Windows 10 e Windows Server 2016/2019 aggiunti a Azure AD ibrido, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `dsregcmd.exe /debug /leave`.
      1. Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

      Per le versioni di sistemi operativi Windows di livello inferiore ibride Azure AD Unite, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Per i dispositivi Azure AD aggiunti ai dispositivi Windows 10, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore
      1. Immettere `dsregcmd /forcerecovery` (Nota: Per eseguire questa azione, è necessario essere un amministratore.
      1. Fare clic su "Accedi" nella finestra di dialogo visualizzata e continuare con il processo di accesso.
      1. Disconnettersi e accedere di nuovo al dispositivo per completare il ripristino.

      Per Azure AD dispositivi Windows 10 registrati, seguire questa procedura:

      1. Passare a **Impostazioni** > **account** > **Accedi all'ufficio o all'Istituto di istruzione**. 
      1. Selezionare l'account e selezionare **Disconnetti**.
      1. Fare clic su "+ Connetti" e registrare di nuovo il dispositivo eseguendo il processo di accesso.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>D: Perché vengono visualizzate voci di dispositivo duplicate nel portale di Azure?

**R:**

- Per i dispositivi Windows 10 e Windows Server 2016, i tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo potrebbero dare luogo a elementi duplicati. 
- Ciascun utente Windows che usa l'opzione **Aggiungi account aziendale o dell'istituto di istruzione** creerà un nuovo record di dispositivo con lo stesso nome.
- Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio Azure Directory locale, la registrazione automatica crea un nuovo record di dispositivo con lo stesso nome per ogni utente del dominio che accede al dispositivo. 
- Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome viene visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>D: La registrazione dei dispositivi Windows 10 in Azure AD supporta TPMs in modalità FIPS?

**R:** No, attualmente la registrazione del dispositivo in Windows 10 per tutti gli Stati dei dispositivi, Azure AD ibrido join, Azure AD join e Azure AD registrato, non supporta TPMs in modalità FIPS. Per eseguire correttamente il join o la registrazione a Azure AD, è necessario disattivare la modalità FIPS per il TPMs su tali dispositivi

---

**D: Perché un utente può comunque accedere alle risorse da un dispositivo disattivato nel portale di Azure?**

**R:** È necessaria un'ora per applicare una revoca dal momento in cui il dispositivo Azure AD è contrassegnato come disabilitato.

>[!NOTE] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per ulteriori informazioni, vedere [Informazioni sulla gestione dei dispositivi](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>D: Perché i dispositivi sono contrassegnati come "in sospeso" nella colonna registrata della portale di Azure?

**R**:  In sospeso indica che il dispositivo non è registrato. Questo stato indica che un dispositivo è stato sincronizzato con Azure AD connettersi da AD locale ed è pronto per la registrazione del dispositivo. Il tipo di JOIN del dispositivo è impostato su "Azure AD ibrido aggiunto". Scopri di più su [come pianificare l'implementazione del join di Azure Active Directory ibrido](hybrid-azuread-join-plan.md).

>[!NOTE]
>Un dispositivo può anche passare da uno stato registrato a "in sospeso"
>* Se un dispositivo viene eliminato e da Azure AD prima e nuovamente sincronizzato da AD locale.
>* Se un dispositivo viene rimosso da un ambito di sincronizzazione su Azure AD Connect e aggiunto di nuovo.
>
>In entrambi i casi, è necessario registrare nuovamente il dispositivo manualmente in ognuno di questi dispositivi. Per verificare se il dispositivo è stato registrato in precedenza, è possibile [risolvere i problemi relativi ai dispositivi usando il comando dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>D: Ricerca per categorie separare un dispositivo Azure AD aggiunto localmente nel dispositivo?

**R:** Per i dispositivi puri aggiunti ad Azure AD, assicurarsi di avere un account amministratore locale offline o crearne uno. Non è possibile accedere con le credenziali di un utente Azure AD. A questo punto, passare a **Impostazioni** > **Account** > **Accedi all'azienda o all'istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>D: Gli utenti possono accedere ai dispositivi Azure AD aggiunti che vengono eliminati o disabilitati in Azure AD?

**R:** Sì. Windows dispone di un nome utente memorizzato nella cache e della funzionalità di password che consente agli utenti che hanno effettuato l'accesso in precedenza di accedere al desktop rapidamente anche senza la connettività di rete. 

Quando un dispositivo viene eliminato o disabilitato in Azure AD, ciò non è noto al dispositivo Windows. Pertanto, gli utenti registrati in precedenza continuano ad accedere al desktop con il nome utente memorizzato nella cache e la password. Tuttavia, quando il dispositivo viene eliminato o disabilitato, gli utenti non possono accedere alle risorse protette dall'accesso condizionale basato su dispositivo. 

Gli utenti che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>D: Un utente disabilitato o eliminato può accedere a un Azure AD dispositivi aggiunti

**R:** Sì, ma solo per un periodo di tempo limitato. Quando un utente viene eliminato o disabilitato in Azure AD, ciò non è noto immediatamente al dispositivo Windows. Pertanto, gli utenti registrati in precedenza possono accedere al desktop con il nome utente memorizzato nella cache e la password. 

In genere, il dispositivo è a conoscenza dello stato utente in meno di quattro ore. Quindi Windows blocca l'accesso degli utenti sul desktop. Quando l'utente viene eliminato o disabilitato in Azure AD, tutti i relativi token vengono revocati. Pertanto, non può accedere alle risorse. 

Gli utenti eliminati o disabilitati che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>D: Perché gli utenti hanno problemi nei dispositivi Azure AD aggiunti dopo la modifica dell'UPN?

**R:** Attualmente, non viene ancora offerto il supporto completo delle modifiche degli UPN sui dispositivi aggiunti ad Azure AD. Dopo la modifica dell'UPN, quindi, l'autenticazione degli utenti ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. Per risolvere il problema, gli utenti devono accedere a Windows tramite il riquadro "Altro utente" specificando il nuovo UPN. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>D: Gli utenti non possono eseguire la ricerca delle stampanti da dispositivi aggiunti ad Azure AD. Come è possibile abilitare la stampa da tali dispositivi?

**R:** Per la distribuzione delle stampanti per i dispositivi aggiunti ad Azure AD, vedere [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication (Distribuire stampa di cloud ibrido su server Windows con autenticazione preliminare)](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>D: Ricerca per categorie connettersi a un dispositivo remoto Azure AD aggiunto?

**R:** Vedere [(Connect to remote Azure Active Directory-joined PC) Connetti al computer remoto aggiunto ad Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>D: Perché gli utenti non *riescono a farlo da qui*?

**R:** Sono state configurate alcune regole di accesso condizionale per richiedere uno specifico stato del dispositivo? Se il dispositivo non soddisfa i criteri, gli utenti vengono bloccati e viene visualizzato tale messaggio. Valutare le regole dei criteri di accesso condizionale. Assicurarsi che il dispositivo soddisfi i criteri in modo da evitare il messaggio.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>D: Perché alcuni utenti non ottengono richieste di autenticazione a più fattori di Azure sui dispositivi Azure AD aggiunti?

**R:** Un utente può aggiungere o registrare un dispositivo con Azure AD avvalendosi di Multi-Factor Authentication. Il dispositivo stesso diventa quindi un secondo fattore attendibile per tale utente. Ogni volta che lo stesso utente esegue l'accesso al dispositivo e accede a un'applicazione, Azure AD considera tale dispositivo come secondo fattore. Ciò consente di accedere facilmente alle applicazioni senza altri messaggi prompt da parte di Multi-Factor Authentication. 

Questo comportamento:

- È applicabile a tutti i dispositivi registrati in Azure AD e ai dispositivi aggiunti ad Azure AD, ma non ai dispositivi aggiunti ad Azure AD ibrido.
- Non è applicabile ad altri utenti che accedono al dispositivo. Pertanto, tutti gli altri utenti che accedono a tale dispositivo ricevono una richiesta di verifica di Multi-Factor Authentication. A questo punto è possibile accedere le applicazioni che richiedono l'autenticazione a più fattori.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>D: Perché ricevo un *nome utente o una password non è* un messaggio corretto per un dispositivo appena aggiunto al Azure ad?

**R:** I motivi più comuni per questo scenario sono i seguenti:

- Le credenziali dell'utente non sono più valide.
- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.
- Gli accessi federati richiedono un server federativo con supporto per endpoint WS-Trust abilitati e accessibili. 
- Autenticazione pass-through abilitata. Pertanto, la password temporanea deve essere modificata quando si accede.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>D: Perché vengono visualizzati gli *Oops... si è verificato un errore.* Quando si tenta di Azure AD aggiungere il PC?

**R:** La finestra di errore viene visualizzata quando si registra Azure Active Directory con Intune. Verificare che all'utente che cerca di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altre informazioni, vedere [Set up enrollment for Windows devices (Configurare la registrazione in blocco per i dispositivi Windows)](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>D: Perché il tentativo di Azure AD aggiunta a un PC ha avuto esito negativo, anche se non sono state apportate informazioni sugli errori?

**R:** È probabile che l'utente abbia effettuato l'accesso al dispositivo tramite l'account amministratore predefinito locale. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:qual sono i certificati MS-Organization-P2P-Access presenti nei dispositivi Windows 10?

**R:** I certificati MS-Organization-P2P-Access vengono rilasciati da Azure AD sia sui dispositivi aggiunti ad Azure AD che su quelli aggiunti ad Azure AD ibrido. Tali certificati vengono usati per abilitare l'attendibilità tra i dispositivi nello stesso tenant per scenari di desktop remoti. Un certificato viene rilasciato al dispositivo e un altro viene rilasciato all'utente. Il certificato del dispositivo è presente in `Local Computer\Personal\Certificates` ed è valido per un giorno. Tale certificato viene rinnovato (generando un nuovo certificato) se il dispositivo è ancora attivo in Azure AD. Il certificato utente è presente in `Current User\Personal\Certificates` e anch'esso è valido per un giorno, ma viene rilasciato su richiesta quando un utente tenta di eseguire una sessione di accesso da remoto a un altro dispositivo aggiunto ad Azure AD. Non viene rinnovato alla scadenza. Entrambi questi certificati vengono emessi usando il certificato MS-Organization-P2P-Access presente in `Local Computer\AAD Token Issuer\Certificates`. Tale certificato è emesso da Azure AD durante la registrazione del dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>D: perché vengono visualizzati più certificati scaduti rilasciati da MS-Organization-P2P-Access nei dispositivi Windows 10? Come è possibile eliminarli?

**R:** Si è verificato un problema in Windows 10, versione 1709 e precedenti, in cui i certificati MS-Organization-P2P-Access scaduti sono rimasti presenti nell'archivio del computer a causa di problemi di crittografia. Gli utenti potrebbero affrontare problemi con la connettività di rete, se si usano qualsiasi client VPN (ad esempio, Cisco AnyConnect) che non è in grado di gestire il numero elevato di certificati scaduti. Questo problema è stato risolto nella versione 1803 di Windows 10 nella quale vengono eliminati automaticamente i certificati MS-Organization-P2P-Access scaduti. È possibile risolvere questo problema aggiornando i dispositivi alla versione 1803 di Windows 10. Se non si è in grado di aggiornare, è possibile eliminare questi certificati senza effetti negativi.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD ibrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>D: Ricerca per categorie separare un dispositivo Azure AD ibrido aggiunto localmente nel dispositivo?

**R:** Per la gestione ibrida dei dispositivi aggiunti ad Azure AD, assicurarsi di disattivare la registrazione automatica. In questo modo l'attività pianificata non registra nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e immettere `dsregcmd.exe /debug /leave`. O eseguire questo comando come uno script in più dispositivi per separare in blocco.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>D: Dove è possibile trovare informazioni sulla risoluzione dei problemi per diagnosticare gli errori di join di Azure AD ibrido?

**R:** Per informazioni sulla risoluzione dei problemi, vedere gli articoli seguenti:

- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>D: Perché viene visualizzato un record registrato Azure AD duplicato per il dispositivo Windows 10 Hybrid Azure AD aggiunto nell'elenco Azure AD Devices?

**R:** Quando gli utenti aggiungono i propri account alle app in un dispositivo aggiunto al dominio, potrebbe essere visualizzata la richiesta **Aggiungi account a Windows?** Se si immette **Sì** quando viene richiesto, si registra il dispositivo in Azure AD. Il tipo di attendibilità è contrassegnato come registrato in Azure AD. Quando si abilita l'aggiunta ad Azure AD ibrido nell'organizzazione, il dispositivo viene aggiunto anche ad Azure AD ibrido. Vengono visualizzati due stati dei dispositivi per lo stesso dispositivo. 

Lo stato di aggiunto ad Azure AD ibrido ha la precedenza rispetto allo stato di registrato in Azure AD. Quindi, il dispositivo viene considerato ibrido Azure AD Unito per la valutazione dell'autenticazione e dell'accesso condizionale. È possibile eliminare in modo sicuro il record di dispositivo registrato in Azure AD dal portale di Azure AD. Informazioni su come [evitare o eliminare il doppio stato nel computer Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>D: Perché gli utenti hanno problemi nei dispositivi Windows 10 Hybrid Azure AD aggiunti dopo la modifica dell'UPN?

**R:** Attualmente, non viene ancora offerto il supporto completo delle modifiche degli UPN sui dispositivi aggiunti ad Azure AD ibrido. Dopo aver modificato il proprio UPN, gli utenti potranno accedere al dispositivo e alle applicazioni locali, ma l'autenticazione ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. A questo punto, è necessario separare il dispositivo dal Azure AD (eseguire "dsregcmd/Leave" con privilegi elevati) e riunirlo (si verifica automaticamente) per risolvere il problema. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>D: I dispositivi Windows 10 Hybrid Azure AD aggiunti richiedono una serie di informazioni sul controller di dominio per ottenere l'accesso alle risorse cloud?

**R:** No, tranne quando viene modificata la password dell'utente. Dopo che Windows 10 Hybrid Azure AD join è stato completato e l'utente ha eseguito l'accesso almeno una volta, il dispositivo non necessita di una linea di visibilità per il controller di dominio per accedere alle risorse cloud. Windows 10 può ottenere l'accesso Single Sign-on per Azure AD applicazioni da qualsiasi posizione con una connessione Internet, tranne quando viene modificata una password. Gli utenti che eseguono l'accesso con Windows Hello for business continuano a ottenere l'accesso Single Sign-on alle applicazioni Azure AD anche dopo una modifica della password, anche se non hanno una linea di controllo per il controller di dominio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>D: Cosa accade se un utente modifica la password e tenta di accedere al dispositivo Windows 10 Hybrid Azure AD aggiunto al di fuori della rete aziendale?

**R:** Se una password viene modificata all'esterno della rete aziendale, ad esempio usando Azure AD SSPR, l'accesso dell'utente con la nuova password avrà esito negativo. Per i dispositivi ibridi Azure AD aggiunti, l'Active Directory locale è l'autorità primaria. Quando un dispositivo non dispone di una linea di visibilità per il controller di dominio, non è in grado di convalidare la nuova password. Quindi, l'utente deve stabilire una connessione con il controller di dominio (tramite VPN o nella rete aziendale) prima di poter accedere al dispositivo con la nuova password. In caso contrario, possono accedere solo con la vecchia password a causa della funzionalità di accesso memorizzato nella cache di Windows. Tuttavia, la vecchia password viene invalidata da Azure AD durante le richieste di token e, di conseguenza, impedisce l'accesso Single Sign-on e non genera alcun criterio di accesso condizionale basato su dispositivo. Questo problema non si verifica se si usa Windows Hello for business. 

---

## <a name="azure-ad-register-faq"></a>Domande frequenti sulla registrazione in Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-device-locally-on-the-device"></a>D: Ricerca per categorie rimuovere un dispositivo registrato Azure AD localmente sul dispositivo?

**R:** 
- Per i dispositivi registrati Azure ad Windows 10, passare a **Impostazioni** > **account** > **Accedi all'ufficio o all'Istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. La registrazione del dispositivo è per profilo utente in Windows 10.
- Per iOS e Android, è possibile usare le **Impostazioni** > dell'applicazione Microsoft Authenticator**registrazione del dispositivo** e selezionare **Annulla registrazione del dispositivo**.
- Per macOS, è possibile usare l'applicazione Portale aziendale Microsoft Intune per annullare la registrazione del dispositivo dalla gestione e rimuovere eventuali registrazioni. 

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>D: È possibile registrare I dispositivi Android o iOS BYOD?

**R:** Sì, ma solo con il servizio di registrazione del dispositivo di Azure e per clienti che usano ambienti ibridi. Non è supportato con il servizio di registrazione dei dispositivi locali in Active Directory Federation Services (ADFS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>D: Come è possibile registrare un dispositivo macOS?

**R:** Eseguire questa procedura:

1.  [Creare i criteri di conformità del dispositivo](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Definire un criterio di accesso condizionale per i dispositivi macOS](../active-directory-conditional-access-azure-portal.md) 

**Osservazioni:**

- Per accedere alle risorse, gli utenti inclusi nei criteri di accesso condizionale necessitano [di una versione supportata di Office per MacOS](../conditional-access/technical-reference.md#client-apps-condition) . 
- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---
## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sui [dispositivi registrati in Azure AD](concept-azure-ad-register.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD](concept-azure-ad-join.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD in modalità ibrida](concept-azure-ad-join-hybrid.md)
