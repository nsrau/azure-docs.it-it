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
ms.openlocfilehash: a27c9ae1b75b9517bd3af92486df96434c5b34fb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207394"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory

## <a name="general-faq"></a>General FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: I registered the device recently. Perché non viene visualizzato nelle informazioni dell'utente all'interno del portale di Azure? Or why is the device owner marked as N/A for hybrid Azure Active Directory (Azure AD) joined devices?

**A:** Windows 10 devices that are hybrid Azure AD joined don't show up under **USER devices**.
Usare la visualizzazione **Tutti i dispositivi** nel portale di Azure. In alternativa, è possibile usare un cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Sotto **Dispositivi utente** vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido. 
- Tutti i dispositivi non Windows 10 o Windows Server 2016.
- Tutti i dispositivi non Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: How do I know what the device registration state of the client is?

**A:** In the Azure portal, go to **All devices**. Cercare il dispositivo usando l'ID dispositivo. Controllare il valore nella colonna Tipo di join. In alcuni casi, è possibile che il dispositivo sia stato reimpostato o che ne sia stata ricreata l'immagine. È quindi importante controllare anche lo stato di registrazione del dispositivo all'interno del dispositivo:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire `dsregcmd.exe /status`.
- Per le versioni del sistema operativo di livello inferiore, eseguire `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** For troubleshooting information, see these articles:
- [Troubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: I see the device record under the USER info in the Azure portal. E lo stato viene visualizzato come registrato nel dispositivo. Am I set up correctly to use Conditional Access?

**A:** The device join state, shown by **deviceID**, must match the state on Azure AD and meet any evaluation criteria for Conditional Access. For more information, see [Require managed devices for cloud app access with Conditional Access](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Q: Why do my users see an error message saying "Your organization has deleted the device" or "Your organization has disabled the device" on their Windows 10 devices ?

**A:** On Windows 10 devices joined or registered with Azure AD, users are issued a [Primary refresh token (PRT)](concept-primary-refresh-token.md) which enables single sign on. The validity of the PRT is based on the validaity of the device itself. Users see this message if the device is either deleted or disabled in Azure AD without initiating the action from the device itself. A device can be deleted or disabled in Azure AD one of the following scenarios: 

- User disables the device from the My Apps portal. 
- An administrator (or user) deletes or disables the device in the Azure portal or by using PowerShell
- Hybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Upgrading Azure AD connect to the version 1.4.xx.x. [Understanding Azure AD Connect 1.4.xx.x and device disappearance](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


See below on how these actions can be rectified.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. But the local state on the device says it's still registered. Cosa devo fare?

**A:** This operation is by design. In this case, the device doesn't have access to resources in the cloud. Administrators can perform this action for stale, lost or stolen devices to prevent unauthorized access. If this action was performed unintentionally, you'll need to re-enable or re-register the device as described below

- If the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > If you are syncing devices using Azure AD Connect, hybrid Azure AD joined devices will be automatically re-enabled during the next  sync cycle. So, if you need to disable a hybrid Azure AD joined device, you need to disable it from your on-premises AD

 - If the device is deleted in Azure AD, you need to re-register the device. To re-register, you must take a manual action on the device. See below for instructions for re-registration based on the device state. 

      To re-register hybrid Azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `dsregcmd.exe /debug /leave`.
      1. Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

      For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      For Azure AD joined devices Windows 10 devices, take the following steps:

      1. Aprire il prompt dei comandi come amministratore
      1. Enter `dsregcmd /forcerecovery` (Note: You need to be an administrator to perform this action).
      1. Click "Sign in" in the dialog that opens up and continue with the sign in process.
      1. Sign out and sign in back to the device to complete the recovery.

      For Azure AD registered Windows 10 devices, take the following steps:

      1. Go to **Settings** > **Accounts** > **Access Work or School**. 
      1. Select the account and select **Disconnect**.
      1. Click on "+ Connect" and register the device again by going through the sign in process.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Why do I see duplicate device entries in the Azure portal?

**R:**

- Per i dispositivi Windows 10 e Windows Server 2016, i tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo potrebbero dare luogo a elementi duplicati. 
- Ciascun utente Windows che usa l'opzione **Aggiungi account aziendale o dell'istituto di istruzione** creerà un nuovo record di dispositivo con lo stesso nome.
- Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio Azure Directory locale, la registrazione automatica crea un nuovo record di dispositivo con lo stesso nome per ogni utente del dominio che accede al dispositivo. 
- Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome viene visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Does Windows 10 device registration in Azure AD support TPMs in FIPS mode?

**A:** Windows 10 device registration only supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Azure AD join or Hybrid Azure AD join. Note that Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. 

---

**Q: Why can a user still access resources from a device I disabled in the Azure portal?**

**A:** It takes up to an hour for a revoke to be applied from the time the Azure AD device is marked as disabled.

>[!NOTE] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per ulteriori informazioni, vedere [Informazioni sulla gestione dei dispositivi](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Why are there devices marked as "Pending" under the REGISTERED column in the Azure portal?

**A**:  Pending indicates that the device is not registered. This state indicates that a device has been synchronized using Azure AD connect from an on-premises AD and is ready for device registration. These devices have the JOIN TYPE set to "Hybrid Azure AD joined". Learn more on [how to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md).

>[!NOTE]
>A device can also change from having a registered state to "Pending"
>* If a device is deleted from Azure AD first and re-synchronized from an on-premises AD.
>* If a device is removed from a sync scope on Azure AD Connect and added back.
>
>In both cases, you must re-register the device manually on each of these devices. To review whether the device was previously registered, you can [troubleshoot devices using the dsregcmd command](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin an Azure AD joined device locally on the device?

**A:** For pure Azure AD joined devices, make sure you have an offline local administrator account or create one. Non è possibile accedere con le credenziali di un utente Azure AD. A questo punto, passare a **Impostazioni** > **Account** > **Accedi all'azienda o all'istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: Can my users' sign in to Azure AD joined devices that are deleted or disabled in Azure AD?

**R:** Sì. Windows dispone di un nome utente memorizzato nella cache e della funzionalità di password che consente agli utenti che hanno effettuato l'accesso in precedenza di accedere al desktop rapidamente anche senza la connettività di rete. 

Quando un dispositivo viene eliminato o disabilitato in Azure AD, ciò non è noto al dispositivo Windows. Pertanto, gli utenti registrati in precedenza continuano ad accedere al desktop con il nome utente memorizzato nella cache e la password. But as the device is deleted or disabled, users can't access any resources protected by device-based Conditional Access. 

Gli utenti che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: Can a disabled or deleted user sign in to an Azure AD joined devices

**A:** Yes, but only for a limited time. Quando un utente viene eliminato o disabilitato in Azure AD, ciò non è noto immediatamente al dispositivo Windows. Pertanto, gli utenti registrati in precedenza possono accedere al desktop con il nome utente memorizzato nella cache e la password. 

In genere, il dispositivo è a conoscenza dello stato utente in meno di quattro ore. Quindi Windows blocca l'accesso degli utenti sul desktop. Quando l'utente viene eliminato o disabilitato in Azure AD, tutti i relativi token vengono revocati. Pertanto, non può accedere alle risorse. 

Gli utenti eliminati o disabilitati che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Azure AD joined devices after changing their UPN?

**A:** Currently, UPN changes are not fully supported on Azure AD joined devices. Dopo la modifica dell'UPN, quindi, l'autenticazione degli utenti ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. Per risolvere il problema, gli utenti devono accedere a Windows tramite il riquadro "Altro utente" specificando il nuovo UPN. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: My users can't search printers from Azure AD joined devices. How can I enable printing from those devices?

**A:** To deploy printers for Azure AD joined devices, see [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: How do I connect to a remote Azure AD joined device?

**A:** See [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: Why do my users see *You can’t get there from here*?

**A:** Did you configure certain Conditional Access rules to require a specific device state? Se il dispositivo non soddisfa i criteri, gli utenti vengono bloccati e viene visualizzato tale messaggio. Evaluate the Conditional Access policy rules. Assicurarsi che il dispositivo soddisfi i criteri in modo da evitare il messaggio.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: Why don't some of my users get Azure Multi-Factor Authentication prompts on Azure AD joined devices?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. Il dispositivo stesso diventa quindi un secondo fattore attendibile per tale utente. Ogni volta che lo stesso utente esegue l'accesso al dispositivo e accede a un'applicazione, Azure AD considera tale dispositivo come secondo fattore. Ciò consente di accedere facilmente alle applicazioni senza altri messaggi prompt da parte di Multi-Factor Authentication. 

Questo comportamento:

- È applicabile a tutti i dispositivi registrati in Azure AD e ai dispositivi aggiunti ad Azure AD, ma non ai dispositivi aggiunti ad Azure AD ibrido.
- Non è applicabile ad altri utenti che accedono al dispositivo. Pertanto, tutti gli altri utenti che accedono a tale dispositivo ricevono una richiesta di verifica di Multi-Factor Authentication. A questo punto è possibile accedere le applicazioni che richiedono l'autenticazione a più fattori.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Why do I get a *username or password is incorrect* message for a device I just joined to Azure AD?

**A:** Common reasons for this scenario are as follows:

- Le credenziali dell'utente non sono più valide.
- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.
- Gli accessi federati richiedono un server federativo con supporto per endpoint WS-Trust abilitati e accessibili. 
- Autenticazione pass-through abilitata. Pertanto, la password temporanea deve essere modificata quando si accede.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Why do I see the *Oops… an error occurred!* dialog when I try to Azure AD join my PC?

**A:** This error happens when you set up Azure Active Directory enrollment with Intune. Verificare che all'utente che cerca di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altre informazioni, vedere [Set up enrollment for Windows devices (Configurare la registrazione in blocco per i dispositivi Windows)](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: Why did my attempt to Azure AD join a PC fail, although I didn't get any error information?

**A:** A likely cause is that you signed in to the device by using the local built-in administrator account. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What are the MS-Organization-P2P-Access certificates present on our Windows 10 devices?

**A:** The MS-Organization-P2P-Access certificates are issued by Azure AD to both, Azure AD joined and hybrid Azure AD joined devices. Tali certificati vengono usati per abilitare l'attendibilità tra i dispositivi nello stesso tenant per scenari di desktop remoti. Un certificato viene rilasciato al dispositivo e un altro viene rilasciato all'utente. Il certificato del dispositivo è presente in `Local Computer\Personal\Certificates` ed è valido per un giorno. Tale certificato viene rinnovato (generando un nuovo certificato) se il dispositivo è ancora attivo in Azure AD. Il certificato utente è presente in `Current User\Personal\Certificates` e anch'esso è valido per un giorno, ma viene rilasciato su richiesta quando un utente tenta di eseguire una sessione di accesso da remoto a un altro dispositivo aggiunto ad Azure AD. Non viene rinnovato alla scadenza. Entrambi questi certificati vengono emessi usando il certificato MS-Organization-P2P-Access presente in `Local Computer\AAD Token Issuer\Certificates`. Tale certificato è emesso da Azure AD durante la registrazione del dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why do I see multiple expired certificates issued by MS-Organization-P2P-Access on our Windows 10 devices? How can I delete them?

**A:** There was an issue identified on Windows 10 version 1709 and lower where expired MS-Organization-P2P-Access certificates continued to exist on the computer store because of cryptographic issues. Your users could face issues with network connectivity, if you are using any VPN clients (for example, Cisco AnyConnect) that cannot handle the large number of expired certificates. Questo problema è stato risolto nella versione 1803 di Windows 10 nella quale vengono eliminati automaticamente i certificati MS-Organization-P2P-Access scaduti. È possibile risolvere questo problema aggiornando i dispositivi alla versione 1803 di Windows 10. Se non si è in grado di aggiornare, è possibile eliminare questi certificati senza effetti negativi.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD ibrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin a Hybrid Azure AD joined device locally on the device?

**A:** For hybrid Azure AD joined devices, make sure to turn off automatic registration. In questo modo l'attività pianificata non registra nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e immettere `dsregcmd.exe /debug /leave`. O eseguire questo comando come uno script in più dispositivi per separare in blocco.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: Where can I find troubleshooting information to diagnose hybrid Azure AD join failures?

**A:** For troubleshooting information, see these articles:

- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Why do I see a duplicate Azure AD registered record for my Windows 10 hybrid Azure AD joined device in the Azure AD devices list?

**A:** When your users add their accounts to apps on a domain-joined device, they might be prompted with **Add account to Windows?** Se si immette **Sì** quando viene richiesto, si registra il dispositivo in Azure AD. Il tipo di attendibilità è contrassegnato come registrato in Azure AD. Quando si abilita l'aggiunta ad Azure AD ibrido nell'organizzazione, il dispositivo viene aggiunto anche ad Azure AD ibrido. Vengono visualizzati due stati dei dispositivi per lo stesso dispositivo. 

Lo stato di aggiunto ad Azure AD ibrido ha la precedenza rispetto allo stato di registrato in Azure AD. So your device is considered hybrid Azure AD joined for any authentication and Conditional Access evaluation. È possibile eliminare in modo sicuro il record di dispositivo registrato in Azure AD dal portale di Azure AD. Informazioni su come [evitare o eliminare il doppio stato nel computer Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Windows 10 hybrid Azure AD joined devices after changing their UPN?

**A:** Currently UPN changes are not fully supported with hybrid Azure AD joined devices. Dopo aver modificato il proprio UPN, gli utenti potranno accedere al dispositivo e alle applicazioni locali, ma l'autenticazione ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. At this time, you need to unjoin the device from Azure AD (run "dsregcmd /leave" with elevated privileges) and rejoin (happens automatically) to resolve the issue. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: Do Windows 10 hybrid Azure AD joined devices require line of sight to the domain controller to get access to cloud resources?

**A:** No, except when the user's password is changed. After Windows 10 hybrid Azure AD join is complete, and the user has signed in at least once, the device doesn't require line of sight to the domain controller to access cloud resources. Windows 10 can get single sign-on to Azure AD applications from anywhere with an internet connection, except when a password is changed. Users who sign in with Windows Hello for Business continue to get single sign-on to Azure AD applications even after a password change, even if they don't have line of sight to their domain controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: What happens if a user changes their password and tries to login to their Windows 10 hybrid Azure AD joined device outside the corporate network?

**A:** If a password is changed outside the corporate network (for example, by using Azure AD SSPR), then the user sign in with the new password will fail. For hybrid Azure AD joined devices, on-premises Active Directory is the primary authority. When a device does not have line of sight to the domain controller, it is unable to validate the new password. So, user needs to establish connection with the domain controller (either via VPN or being in the corporate network) before they're able to sign in to the device with their new password. Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. However, the old password is invalidated by Azure AD during token requests and hence, prevents single sign-on and fails any device-based Conditional Access policies. This issue doesn't occur if you use Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Domande frequenti sulla registrazione in Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: How do I remove an Azure AD registered state for a device locally?

**R:** 
- For Windows 10 Azure AD registered devices, Go to **Settings** > **Accounts** > **Access Work or School**. Selezionare l'account e quindi **Disconnetti**. Device registration is per user profile on Windows 10.
- For iOS and Android, you can use the Microsoft Authenticator application **Settings** > **Device Registration** and select **Unregister device**.
- For macOS, you can use the Microsoft Intune Company Portal application to un-enroll the device from management and remove any registration. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: How can I block users from adding additional work accounts (Azure AD registered) on my corporate Windows 10 devices?

**A:** Enable the following registry to block your users from adding additional work accounts to your corporate domain joined, Azure AD joined or hybrid Azure AD joined Windows 10 devices. This policy can also be used to block domain joined machines from inadvertently getting Azure AD registered with the same user account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Can I register Android or iOS BYOD devices?

**A:** Yes, but only with the Azure device registration service and for hybrid customers. Non è supportato con il servizio di registrazione dei dispositivi locali in Active Directory Federation Services (ADFS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: How can I register a macOS device?

**A:** Take the following steps:

1.  [Creare i criteri di conformità del dispositivo](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Define a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**Osservazioni:**

- The users included in your Conditional Access policy need a [supported version of Office for macOS](../conditional-access/technical-reference.md#client-apps-condition) to access resources. 
- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---
## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sui [dispositivi registrati in Azure AD](concept-azure-ad-register.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD](concept-azure-ad-join.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD in modalità ibrida](concept-azure-ad-join-hybrid.md)
