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
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497528"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory

## <a name="general-faq"></a>Domande frequenti generali

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>D: Di recente è stato registrato un dispositivo. Perché non è possibile visualizzare il dispositivo sotto le informazioni utente nel portale di Azure? Oppure perché il proprietario del dispositivo è contrassegnato come N/D per i dispositivi ibridi aggiunti ad Azure Active Directory (Azure AD)?

**A:** I dispositivi Windows 10 aggiunti ad Azure AD ad Azure AD non vengono visualizzati nei **dispositivi USER.**
Usare la visualizzazione **Tutti i dispositivi** nel portale di Azure. In alternativa, è possibile usare un cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Sotto **Dispositivi utente** vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido. 
- Tutti i dispositivi non Windows 10 o Windows Server 2016.
- Tutti i dispositivi non Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>D: Come è possibile conoscere lo stato di registrazione del dispositivo client?

**A:** Nel portale di Azure passare a **Tutti i dispositivi**. Cercare il dispositivo usando l'ID dispositivo. Controllare il valore nella colonna Tipo di join. In alcuni casi, è possibile che il dispositivo sia stato reimpostato o che ne sia stata ricreata l'immagine. È quindi importante controllare anche lo stato di registrazione del dispositivo all'interno del dispositivo:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire `dsregcmd.exe /status`.
- Per le versioni del sistema operativo di livello inferiore, eseguire `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** Per informazioni sulla risoluzione dei problemi, vedere questi articoli:For troubleshooting information, see these articles:
- [Risoluzione dei problemi relativi ai dispositivi utilizzando il comando dsregcmdTroubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>D: Il record del dispositivo viene visualizzato sotto le informazioni USER nel portale di Azure.D: I see the device record under the USER info in the Azure portal. E lo stato viene visualizzato come registrato nel dispositivo. È impostato correttamente per l'utilizzo dell'accesso condizionale?

**A:** Lo stato di aggiunta al dispositivo, indicato da **deviceID,** deve corrispondere allo stato di Azure AD e soddisfare i criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Richiedere dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>D: Perché gli utenti visualizzano un messaggio di errore che indica che l'organizzazione ha eliminato il dispositivo o "L'organizzazione ha disabilitato il dispositivo" nei dispositivi Windows 10?

**A:** Nei dispositivi Windows 10 aggiunti o registrati con Azure AD, agli utenti viene rilasciato un token di [aggiornamento primario (PRT)](concept-primary-refresh-token.md) che consente l'accesso Single Sign-On. La validità del PRT si basa sulla validità del dispositivo stesso. Gli utenti visualizzano questo messaggio se il dispositivo viene eliminato o disabilitato in Azure AD senza avviare l'azione dal dispositivo stesso. Un dispositivo può essere eliminato o disabilitato in Azure AD uno degli scenari seguenti:A device can be deleted or disabled in Azure AD one of the following scenarios: 

- L'utente disabilita il dispositivo dal portale App personali. 
- Un amministratore (o utente) elimina o disabilita il dispositivo nel portale di Azure o tramite PowerShell
- Solo aggiunta ad Azure AD ibrido: un amministratore rimuove l'unità organizzativa dei dispositivi dall'ambito di sincronizzazione, causando l'eliminazione dei dispositivi da Azure ADHybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Aggiornamento di Azure AD connettersi alla versione 1.4.xx.x.Upgrading Azure AD connect to the version 1.4.xx.x. [Informazioni su Azure AD Connect 1.4.xx.x e la scomparsa](/azure/active-directory/hybrid/reference-connect-device-disappearance)del dispositivo .


Di seguito viene illustrato come correggere queste azioni.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>D: Ho disabilitato o eliminato il dispositivo nel portale di Azure o tramite Windows PowerShell.D: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. Ma lo stato locale nel dispositivo risulta ancora registrato. Cosa devo fare?

**A:** Questa operazione è in base alla progettazione. In questo caso, il dispositivo non ha accesso alle risorse nel cloud. Gli amministratori possono eseguire questa azione per i dispositivi obsoleti, persi o rubati per impedire l'accesso non autorizzato. Se questa azione è stata eseguita involontariamente, sarà necessario riattivare o registrare nuovamente il dispositivo come descritto di seguito

- Se il dispositivo è stato disabilitato in Azure AD, un amministratore con privilegi sufficienti può abilitarlo dal portale di Azure ADIf the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > Se si sincronizzano i dispositivi con Azure AD Connect, i dispositivi aggiunti ad Azure AD ibridi verranno riabilitati automaticamente durante il ciclo di sincronizzazione successivo. Pertanto, se è necessario disabilitare un dispositivo ibrido aggiunto ad Azure AD, è necessario disabilitarlo da Active Directory locale

 - Se il dispositivo viene eliminato in Azure AD, è necessario registrare nuovamente il dispositivo. Per registrarsi nuovamente, è necessario eseguire un'azione manuale sul dispositivo. Vedere di seguito per istruzioni per la nuova registrazione in base allo stato del dispositivo. 

      Per registrare nuovamente i dispositivi ibridi di Azure AD aggiunti a Windows 10 e Windows Server 2016/2019, eseguire la procedura seguente:To re-register azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `dsregcmd.exe /debug /leave`.
      1. Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

      Per le versioni del sistema operativo Windows di livello inferiore aggiunte ad Azure AD ibrido, eseguire la procedura seguente:For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Per i dispositivi aggiunti ad Azure AD dispositivi Windows 10, eseguire la procedura seguente:For Azure AD joined devices Windows 10 devices, take the following steps:

      1. Aprire il prompt dei comandi come amministratore
      1. Invio `dsregcmd /forcerecovery` (Nota: è necessario essere un amministratore per eseguire questa azione).
      1. Fare clic su "Accedi" nella finestra di dialogo che si apre e continuare con il processo di accesso.
      1. Disconnettersi e accedere di nuovo al dispositivo per completare il ripristino.

      Per i dispositivi Windows 10 registrati in Azure AD, eseguire la procedura seguente:For Azure AD registered Windows 10 devices, take the following steps:

      1. Passare a **Impostazioni** > **account** > **Accesso al lavoro o all'istituto di istruzione**. 
      1. Selezionare l'account e scegliere **Disconnetti**.
      1. Fare clic su "Connessione" e registrare nuovamente il dispositivo passando attraverso il processo di accesso.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>D: Perché nel portale di Azure vengono visualizzate voci di dispositivo duplicate?

**Un:**

- Per i dispositivi Windows 10 e Windows Server 2016, i tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo potrebbero dare luogo a elementi duplicati. 
- Ciascun utente Windows che usa l'opzione **Aggiungi account aziendale o dell'istituto di istruzione** creerà un nuovo record di dispositivo con lo stesso nome.
- Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio Azure Directory locale, la registrazione automatica crea un nuovo record di dispositivo con lo stesso nome per ogni utente del dominio che accede al dispositivo. 
- Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome viene visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>D: La registrazione dei dispositivi Windows 10 in Azure AD supporta i TPM in modalità FIPS?

**A:** La registrazione del dispositivo Windows 10 è supportata solo per TPM 2.0 conforme a FIPS e non per TPM 1.2. Se i dispositivi dispongono di TPM 1.2 conforme allo standard FIPS, è necessario disabilitarli prima di procedere con l'aggiunta ad Azure AD o l'aggiunta ad Azure AD ibrido. Microsoft non fornisce alcuno strumento per disabilitare la modalità FIPS per i TPM in quanto dipende dal produttore del TPM. Contattare l'OEM hardware per assistenza. 

---

**D: Perché un utente può ancora accedere alle risorse da un dispositivo disabilitato nel portale di Azure?**

**A:** L'applicazione di una revoca richiede fino a un'ora dal momento in cui il dispositivo Azure AD viene contrassegnato come disabilitato.

>[!NOTE] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per ulteriori informazioni, vedere [Informazioni sulla gestione dei dispositivi](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>D: Perché sono presenti dispositivi contrassegnati come "In sospeso" nella colonna REGISTERED del portale di Azure?

**R**: In sospeso indica che il dispositivo non è registrato. Questo stato indica che un dispositivo è stato sincronizzato usando la connessione di Azure AD da un ad esempio locale ed è pronto per la registrazione del dispositivo. Questi dispositivi hanno il TIPO JOIN impostato su "Aggiunta ad Azure AD ibrido". Altre informazioni su [come pianificare l'implementazione ibrida](hybrid-azuread-join-plan.md)di join di Azure Active Directory.

>[!NOTE]
>Un dispositivo può anche passare da uno stato registrato a "In sospeso"A device can also change from having a registered state to "Pending"
>* Se un dispositivo viene eliminato prima da Azure AD e risincronizzato da un'area di Active Directory locale.
>* Se un dispositivo viene rimosso da un ambito di sincronizzazione in Azure AD Connect e aggiunto di nuovo.
>
>In entrambi i casi, è necessario registrare nuovamente il dispositivo manualmente su ciascuno di questi dispositivi. Per verificare se il dispositivo è stato registrato in precedenza, è possibile [risolvere i problemi relativi ai dispositivi utilizzando il comando dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>D: Come si annulla l'aggiunta di un dispositivo aggiunto ad Azure AD in locale nel dispositivo?

**A:** Per i dispositivi aggiunti ad Azure AD puri, assicurarsi di disporre di un account amministratore locale offline o di crearne uno. Non è possibile accedere con le credenziali di un utente Azure AD. Passare quindi a **Impostazioni** > **Account** > **Accesso al lavoro o all'istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>D: Gli utenti possono accedere ai dispositivi aggiunti ad Azure AD eliminati o disabilitati in Azure AD?

**A:** Sì. Windows dispone di un nome utente memorizzato nella cache e della funzionalità di password che consente agli utenti che hanno effettuato l'accesso in precedenza di accedere al desktop rapidamente anche senza la connettività di rete. 

Quando un dispositivo viene eliminato o disabilitato in Azure AD, ciò non è noto al dispositivo Windows. Pertanto, gli utenti registrati in precedenza continuano ad accedere al desktop con il nome utente memorizzato nella cache e la password. Tuttavia, poiché il dispositivo viene eliminato o disabilitato, gli utenti non possono accedere alle risorse protette dall'accesso condizionale basato sul dispositivo. 

Gli utenti che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>D: Un utente disabilitato o eliminato può accedere a un dispositivo aggiunto ad Azure AD

**A:** Sì, ma solo per un periodo limitato. Quando un utente viene eliminato o disabilitato in Azure AD, ciò non è noto immediatamente al dispositivo Windows. Pertanto, gli utenti registrati in precedenza possono accedere al desktop con il nome utente memorizzato nella cache e la password. 

In genere, il dispositivo è a conoscenza dello stato utente in meno di quattro ore. Quindi Windows blocca l'accesso degli utenti sul desktop. Quando l'utente viene eliminato o disabilitato in Azure AD, tutti i relativi token vengono revocati. Pertanto, non può accedere alle risorse. 

Gli utenti eliminati o disabilitati che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>D: Perché gli utenti hanno problemi nei dispositivi aggiunti ad Azure AD dopo la modifica dell'UPN?

**A:** Attualmente, le modifiche UPN non sono completamente supportate nei dispositivi aggiunti ad Azure AD. Dopo la modifica dell'UPN, quindi, l'autenticazione degli utenti ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. Per risolvere il problema, gli utenti devono accedere a Windows tramite il riquadro "Altro utente" specificando il nuovo UPN. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>D: Gli utenti non possono cercare stampanti da dispositivi aggiunti ad Azure AD. In che modo è possibile abilitare la stampa da tali dispositivi?

**A:** Per distribuire stampanti per i dispositivi aggiunti ad Azure AD, vedere [Distribuire Windows Server Hybrid Cloud Print con preautenticazione.](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>D: Come si connette a un dispositivo aggiunto ad Azure AD remoto?

**A:** Vedere [Connettersi a un PC remoto aggiunto ad Azure Active Directory](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>D: Perché gli utenti vedono *Non è possibile arrivarci da qui?*

**A:** Sono state configurate alcune regole di accesso condizionale per richiedere uno stato specifico del dispositivo? Se il dispositivo non soddisfa i criteri, gli utenti vengono bloccati e viene visualizzato tale messaggio. Valutare le regole dei criteri di accesso condizionale. Assicurarsi che il dispositivo soddisfi i criteri in modo da evitare il messaggio.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>D: Perché alcuni utenti non ricevono richieste di Azure Multi-Factor Authentication nei dispositivi aggiunti ad Azure AD?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. Il dispositivo stesso diventa quindi un secondo fattore attendibile per tale utente. Ogni volta che lo stesso utente esegue l'accesso al dispositivo e accede a un'applicazione, Azure AD considera tale dispositivo come secondo fattore. Ciò consente di accedere facilmente alle applicazioni senza altri messaggi prompt da parte di Multi-Factor Authentication. 

Questo comportamento:

- È applicabile a tutti i dispositivi registrati in Azure AD e ai dispositivi aggiunti ad Azure AD, ma non ai dispositivi aggiunti ad Azure AD ibrido.
- Non è applicabile ad altri utenti che accedono al dispositivo. Pertanto, tutti gli altri utenti che accedono a tale dispositivo ricevono una richiesta di verifica di Multi-Factor Authentication. A questo punto è possibile accedere le applicazioni che richiedono l'autenticazione a più fattori.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>D: Perché viene visualizzato un messaggio *di nome utente o password non corretto* per un dispositivo appena aggiunto ad Azure AD?

**A:** I motivi più comuni per questo scenario sono i seguenti:Common reasons for this scenario are as follows:

- Le credenziali dell'utente non sono più valide.
- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.
- Gli accessi federati richiedono un server federativo con supporto per endpoint WS-Trust abilitati e accessibili. 
- Autenticazione pass-through abilitata. Pertanto, la password temporanea deve essere modificata quando si accede.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>D: Perché vedo le *Oops... si è verificato un errore!* quando si tenta di aggiungere Azure AD al PC?

**A:** Questo errore si verifica quando si configura la registrazione di Azure Active Directory con Intune.This error happens when you set up Azure Active Directory enrollment with Intune. Verificare che all'utente che cerca di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altre informazioni, vedere [Set up enrollment for Windows devices (Configurare la registrazione in blocco per i dispositivi Windows)](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>D: Perché il tentativo di aggiungere Azure AD a un PC non è riuscito, anche se non sono state fornite informazioni sull'errore?

**A:** Una causa probabile è che è stato eseguito l'accesso al dispositivo utilizzando l'account amministratore predefinito locale. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>D: quali sono i certificati MS-Organization-P2P-Access presenti sui dispositivi Windows 10?

**A:** I certificati MS-Organization-P2P-Access vengono emessi da Azure AD a entrambi, ad Azure AD e ai dispositivi aggiunti ad Azure AD ibridi. Tali certificati vengono usati per abilitare l'attendibilità tra i dispositivi nello stesso tenant per scenari di desktop remoti. Un certificato viene rilasciato al dispositivo e un altro viene rilasciato all'utente. Il certificato del dispositivo è presente in `Local Computer\Personal\Certificates` ed è valido per un giorno. Tale certificato viene rinnovato (generando un nuovo certificato) se il dispositivo è ancora attivo in Azure AD. Il certificato utente è presente in `Current User\Personal\Certificates` e anch'esso è valido per un giorno, ma viene rilasciato su richiesta quando un utente tenta di eseguire una sessione di accesso da remoto a un altro dispositivo aggiunto ad Azure AD. Non viene rinnovato alla scadenza. Entrambi questi certificati vengono emessi usando il certificato MS-Organization-P2P-Access presente in `Local Computer\AAD Token Issuer\Certificates`. Tale certificato è emesso da Azure AD durante la registrazione del dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>D: Perché vengono visualizzati più certificati scaduti emessi da MS-Organization-P2P-Access nei dispositivi Windows 10? Come è possibile eliminarli?

**A:** Si è verificato un problema identificato in Windows 10 versione 1709 e inferiore in cui i certificati MS-Organization-P2P-Access scaduti hanno continuato a esistere nell'archivio computer a causa di problemi di crittografia. Gli utenti potrebbero affrontare problemi con la connettività di rete, se si utilizzano client VPN (ad esempio, Cisco AnyConnect) che non è in grado di gestire il numero elevato di certificati scaduti. Questo problema è stato risolto nella versione 1803 di Windows 10 nella quale vengono eliminati automaticamente i certificati MS-Organization-P2P-Access scaduti. È possibile risolvere questo problema aggiornando i dispositivi alla versione 1803 di Windows 10. Se non si è in grado di aggiornare, è possibile eliminare questi certificati senza effetti negativi.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD ibrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>D: Come si annulla l'unjoin di un dispositivo aggiunto ad Azure AD ibrido in locale nel dispositivo?

**A:** Per i dispositivi aggiunti ad Azure AD ibridi, assicurarsi di disattivare la registrazione automatica. In questo modo l'attività pianificata non registra nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e immettere `dsregcmd.exe /debug /leave`. O eseguire questo comando come uno script in più dispositivi per separare in blocco.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>D: Dove è possibile trovare informazioni sulla risoluzione dei problemi per diagnosticare errori di join di Azure AD ibridi?

**A:** Per informazioni sulla risoluzione dei problemi, vedere questi articoli:For troubleshooting information, see these articles:

- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>D: Perché viene visualizzato un record registrato di Azure AD duplicato per il dispositivo aggiunto ad Azure AD ibrido di Windows 10 nell'elenco dei dispositivi di Azure AD?

**A:** Quando gli utenti aggiungono i propri account alle app in un dispositivo aggiunto a un dominio, è possibile che venga richiesto **di aggiungere un account a Windows?** Se si immette **Sì** quando viene richiesto, si registra il dispositivo in Azure AD. Il tipo di attendibilità è contrassegnato come registrato in Azure AD. Quando si abilita l'aggiunta ad Azure AD ibrido nell'organizzazione, il dispositivo viene aggiunto anche ad Azure AD ibrido. Vengono visualizzati due stati dei dispositivi per lo stesso dispositivo. 

Lo stato di aggiunto ad Azure AD ibrido ha la precedenza rispetto allo stato di registrato in Azure AD. Il dispositivo viene quindi considerato ibrido di Azure AD aggiunto per qualsiasi autenticazione e valutazione dell'accesso condizionale. È possibile eliminare in modo sicuro il record di dispositivo registrato in Azure AD dal portale di Azure AD. Informazioni su come [evitare o eliminare il doppio stato nel computer Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>D: Perché gli utenti hanno problemi nei dispositivi aggiunti ad Azure AD ibridi di Windows 10 dopo la modifica dell'UPN?

**A:** Attualmente le modifiche UPN non sono completamente supportate con i dispositivi aggiunti ad Azure AD ibridi. Dopo aver modificato il proprio UPN, gli utenti potranno accedere al dispositivo e alle applicazioni locali, ma l'autenticazione ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. A questo punto, è necessario rimuovere l'unjoin del dispositivo da Azure AD (eseguire "dsregcmd /leave" con privilegi elevati) e riconnettersi (accade automaticamente) per risolvere il problema. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>D: I dispositivi aggiunti a Azure AD ibridi di Windows 10 richiedono la linea di vista al controller di dominio per ottenere l'accesso alle risorse cloud?

**A:** No, tranne quando viene modificata la password dell'utente. Al termine dell'aggiunta ad Azure AD ibrido di Windows 10 e dopo che l'utente ha eseguito l'accesso almeno una volta, il dispositivo non richiede la linea di vista del controller di dominio per accedere alle risorse cloud. Windows 10 può ottenere l'accesso Single Sign-On alle applicazioni di Azure AD da qualsiasi luogo con una connessione Internet, tranne quando viene modificata una password. Gli utenti che accedono con Windows Hello for Business continuano a ottenere l'accesso Single Sign-On alle applicazioni Azure AD anche dopo una modifica della password, anche se non hanno linea di vista sul controller di dominio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>D: Cosa succede se un utente modifica la password e tenta di accedere al proprio dispositivo aggiunto ad Azure AD ibrido di Windows 10 all'esterno della rete aziendale?

**A:** Se una password viene modificata all'esterno della rete aziendale , ad esempio usando Azure AD SSPR, l'utente accede con la nuova password avrà esito negativo. Per i dispositivi aggiunti ad Azure AD ibridi, Active Directory locale è l'autorità principale. Quando un dispositivo non ha linea di vista al controller di dominio, non è in grado di convalidare la nuova password. Pertanto, l'utente deve stabilire una connessione con il controller di dominio (tramite VPN o nella rete aziendale) prima di poter accedere al dispositivo con la nuova password. In caso contrario, possono accedere solo con la vecchia password a causa della funzionalità di accesso memorizzata nella cache in Windows.Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. Tuttavia, la vecchia password viene invalidata da Azure AD durante le richieste di token e, di conseguenza, impedisce l'accesso Single Sign-On e non riesce tutti i criteri di accesso condizionale basati su dispositivo. Questo problema non si verifica se si utilizza Windows Hello per aziende. 

---

## <a name="azure-ad-register-faq"></a>Domande frequenti sulla registrazione in Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>D: Come si rimuove uno stato registrato di Azure AD per un dispositivo in locale?

**Un:** 
- Per i dispositivi registrati in Windows 10 Azure AD, vai a **Impostazioni** > **account** > **Access Work or School**. Selezionare l'account e quindi **Disconnetti**. La registrazione del dispositivo è per profilo utente in Windows 10.Device registration is per user profile on Windows 10.
- Per iOS e Android, è possibile utilizzare l'applicazione Microsoft Authenticator **Impostazioni** > **Registrazione dispositivo** e selezionare Annulla registrazione **dispositivo**.
- Per macOS, è possibile usare l'applicazione Portale aziendale di Microsoft Intune per annullare la registrazione del dispositivo dalla gestione e rimuovere qualsiasi registrazione. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>D: In che modo è possibile impedire agli utenti di aggiungere altri account di lavoro (è registrato in Azure AD) nei dispositivi Windows 10 aziendali?

**A:** Abilitare il Registro di sistema seguente per impedire agli utenti di aggiungere altri account di lavoro ai dispositivi Windows 10 aggiunti al dominio aziendale, ad Azure AD o aibridi di Azure AD. Questi criteri possono essere usati anche per impedire ai computer aggiunti al dominio di ottenere inavvertitamente Azure AD registrato con lo stesso account utente. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>D: È possibile registrare dispositivi Android o iOS in modalità BYOD?

**A:** Sì, ma solo con il servizio di registrazione dei dispositivi di Azure e per i clienti ibridi. Non è supportato con il servizio di registrazione dei dispositivi locali in Active Directory Federation Services (ADFS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>D: Come si registra un dispositivo macOS?

**A:** Attenersi alla seguente procedura:

1.    [Creare i criteri di conformità del dispositivo](/intune/compliance-policy-create-mac-os)
1.    [Definire criteri di accesso condizionale per i dispositivi macOSDefine a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**Note:**

- Gli utenti inclusi nei criteri di accesso condizionale necessitano di una [versione supportata di Office per macOS](../conditional-access/concept-conditional-access-conditions.md) per accedere alle risorse. 
- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---
## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sui [dispositivi registrati in Azure AD](concept-azure-ad-register.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD](concept-azure-ad-join.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD in modalità ibrida](concept-azure-ad-join-hybrid.md)
