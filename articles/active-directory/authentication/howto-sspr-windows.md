---
title: Self-service password reset for Windows - Azure Active Directory
description: How to enable self-service password reset using forgot password at the Windows login screen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381238"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Enable password reset from the Windows login screen

For machines running Windows 7, 8, 8.1, and 10 you can enable users to reset their password at the Windows login screen. Users no longer have to find a device with a web browser to access the [SSPR portal](https://aka.ms/sspr).

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitazioni generali

- Password reset is not currently supported from a Remote Desktop or from Hyper-V enhanced sessions.
- Questa funzionalità non funziona per le reti con autenticazione di rete 802.1x distribuita e l'opzione "Esegui immediatamente prima dell'accesso utente". Per le reti con autenticazione di rete 802.1x distribuita, è consigliabile usare l'autenticazione di computer per abilitare questa funzionalità.
- Hybrid Azure AD joined machines must have network connectivity line of sight to a domain controller to use the new password and update cached credentials.
- If using an image, prior to running sysprep ensure that the web cache is cleared for the built-in Administrator prior to performing the CopyProfile step. More information about this step can be found in the support article [Performance poor when using custom default user profile](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- The following settings are known to interfere with the ability to use and reset passwords on Windows 10 devices
    - Se i criteri richiedono la combinazione Ctrl + Alt + Canc nelle versioni di Windows 10 precedenti alla v1809, **Reimposta password** non funziona.
    - Se le notifiche della schermata di blocco sono disattivate **Reimposta password** non funziona.
    - HideFastUserSwitching è impostato su 1, ovvero è abilitato
    - DontDisplayLastUserName è impostato su 1, ovvero è abilitato
    - NoLockScreen è impostato su 1, ovvero è abilitato
    - EnableLostMode è impostato sul dispositivo
    - Explorer.exe è sostituito con una shell personalizzata
- The combination of the following specific three settings can cause this feature to not work.
    - Interactive logon: Do not require CTRL+ALT+DEL = Disabled
    - DisableLockScreenAppNotifications = 1 or Enabled
    - IsContentDeliveryPolicyEnforced = 1 or True

## <a name="windows-10-password-reset"></a>Windows 10 password reset

### <a name="windows-10-prerequisites"></a>Windows 10 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 10 devices 
       - Port 443 to `passwordreset.microsoftonline.com` and `ajax.aspnetcdn.com`
       - Windows 10 devices only support machine-level proxy configuration
- Run at least Windows 10, version April 2018 Update (v1803), and the devices must be either:
    - Aggiunta ad Azure AD
    - Aggiunta a Azure AD ibrido

### <a name="enable-for-windows-10-using-intune"></a>Enable for Windows 10 using Intune

Distribuire la modifica della configurazione per abilitare la reimpostazione della password dalla schermata di accesso con Intune è il metodo più flessibile. Intune consente di distribuire la modifica della configurazione a uno specifico gruppo di computer definito. Questo metodo richiede la registrazione del dispositivo a Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Creare criteri di configurazione dei dispositivi in Intune

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Intune**.
1. Creare un nuovo profilo di configurazione dei dispositivi passando a **Configurazione del dispositivo** > **Profili** > **Crea profilo**
   - Immettere un nome significativo per il profilo
   - Specificare eventualmente una descrizione significativa per il profilo
   - Piattaforma **Windows 10 e versioni successive**
   - Tipo profilo **Personalizzato**
1. Configurare le **impostazioni**
   - **Aggiungere** le impostazioni URI OMA seguenti per abilitare il collegamento di reimpostazione della password
      - Specificare un nome significativo per spiegare l'effetto dell'impostazione
      - Specificare eventualmente una descrizione significativa per l'impostazione
      - Impostare **URI OMA** su `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Impostare **Tipo di dati** su **Integer**
      - Impostare **Valore** su **1**
      - Fare clic su **OK**.
   - Fare clic su **OK**.
1. Fare clic su **Crea**
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Enable for Windows 10 using the Registry

1. Accedere al PC Windows con credenziali amministrative
1. Eseguire **regedit** come amministratore
1. Impostare la chiave del Registro di sistema seguente
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Troubleshooting Windows 10 password reset

Il log di controllo di Azure AD includerà informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

When users reset their password from the login screen of a Windows 10 device, a low-privilege temporary account called `defaultuser1` is created. Questo account viene usato per proteggere il processo di reimpostazione della password. Lo stesso account ha una password generata casualmente, non viene visualizzato per l'accesso al dispositivo e verrà rimosso automaticamente dopo che l'utente avrà reimpostato la password. Multiple `defaultuser` profiles may exist but can be safely ignored.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8, and 8.1 password reset

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8, and 8.1 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 7, 8, and 8.1 devices
       - Port 443 to `passwordreset.microsoftonline.com`
- Sistema operativo Windows 7 con patch o Windows 8.1.
- Protocollo TLS 1.2 abilitato seguendo le indicazioni riportate in [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) (Impostazioni del Registro di sistema di Transport Layer Security - TLS).
- If more than one 3rd party credential provider is enabled on your machine, users will see more than one user profile on the login screen.

> [!WARNING]
> TLS 1.2 must be enabled, not just set to auto negotiate

### <a name="install"></a>Installazione

1. Scaricare il programma di installazione appropriato per la versione di Windows che si vuole abilitare.
   - Il software è disponibile nell'Area download Microsoft all'indirizzo [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Accedere al computer in cui si vuole effettuare l'installazione ed eseguire il programma di installazione.
1. Al termine dell'installazione è consigliabile riavviare il computer.
1. After the reboot, at the login screen choose a user and click "Forgot password?" per avviare il flusso di lavoro di reimpostazione della password.
1. Completare il flusso di lavoro seguendo i passaggi visualizzati sullo schermo per reimpostare la password.

![Esempio dell'opzione "Password dimenticata?" selezionata in Windows 7 SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installazione invisibile all'utente

- Per l'installazione invisibile all'utente, usare il comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Per la disinstallazione invisibile all'utente, usare il comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Troubleshooting Windows 7, 8, and 8.1 password reset

Gli eventi verranno registrati sia nel computer sia in Azure AD. Gli eventi di Azure AD includeranno informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se è necessaria una registrazione aggiuntiva, è possibile modificare una chiave del Registro di sistema per abilitare la registrazione dettagliata. Abilitare la registrazione dettagliata solo per scopi di risoluzione dei problemi.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- To enable verbose logging, create a `REG_DWORD: “EnableLogging”`, and set it to 1.
- To disable verbose logging, change the `REG_DWORD: “EnableLogging”` to 0.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Now that you have configured password reset for your Windows devices, what changes for the user? Come capisce che può reimpostare la password nella schermata di accesso?

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

When users attempt to sign in, they now see a **Reset password** or **Forgot password** link that opens the self-service password reset experience at the login screen. Questa funzionalità consente agli utenti di reimpostare la password senza dover usare un altro dispositivo per accedere a un Web browser.

Gli utenti troveranno indicazioni per l'uso di questa funzionalità in [Reimpostare la password per un account aziendale o dell'istituto di istruzione](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Passaggi successivi

[Plan authentication methods to allow](concept-authentication-methods.md)

[Configure Windows 10](https://docs.microsoft.com/windows/configuration/)
