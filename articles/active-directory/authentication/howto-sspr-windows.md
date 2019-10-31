---
title: Azure AD la reimpostazione della password self-service per Windows-Azure Active Directory
description: Come abilitare la reimpostazione self-service della password con password dimenticata nella schermata di accesso di Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519993be873e7864dab4de4f66919c56aebfc379
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171858"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Procedura: abilitare la reimpostazione della password dalla schermata di accesso di Windows

Per i computer che eseguono Windows 7, 8, 8,1 e 10 è possibile consentire agli utenti di reimpostare la password nella schermata di accesso di Windows. Gli utenti non devono più trovare un dispositivo con un Web browser per accedere al [portale di SSPR](https://aka.ms/sspr).

![Esempi di schermate di accesso di Windows 7 e 10 con collegamento SSPR visualizzato](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitazioni generali

- La reimpostazione della password non è attualmente supportata da un Desktop remoto o da sessioni avanzate di Hyper-V.
- Questa funzionalità non funziona per le reti con autenticazione di rete 802.1x distribuita e l'opzione "Esegui immediatamente prima dell'accesso utente". Per le reti con autenticazione di rete 802.1x distribuita, è consigliabile usare l'autenticazione di computer per abilitare questa funzionalità.
- Per usare la nuova password e aggiornare le credenziali memorizzate nella cache, è necessario che i computer Azure AD ibrido Uniti abbiano la connettività di rete a un controller di dominio.
- Se si utilizza un'immagine, prima di eseguire Sysprep assicurarsi che la cache Web venga cancellata per l'amministratore predefinito prima di eseguire il passaggio CopyProfile. Per altre informazioni su questo passaggio, vedere l'articolo relativo al supporto [delle prestazioni scarse quando si usa un profilo utente predefinito personalizzato](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Le impostazioni seguenti sono note per interferire con la possibilità di usare e reimpostare le password nei dispositivi Windows 10
    - Se i criteri richiedono la combinazione Ctrl + Alt + Canc nelle versioni di Windows 10 precedenti alla v1809, **Reimposta password** non funziona.
    - Se le notifiche della schermata di blocco sono disattivate **Reimposta password** non funziona.
    - HideFastUserSwitching è impostato su 1, ovvero è abilitato
    - DontDisplayLastUserName è impostato su 1, ovvero è abilitato
    - NoLockScreen è impostato su 1, ovvero è abilitato
    - EnableLostMode è impostato sul dispositivo
    - Explorer.exe è sostituito con una shell personalizzata
- La combinazione delle tre impostazioni specifiche seguenti può causare un funzionamento non corretto di questa funzionalità.
    - Accesso interattivo: non richiedere CTRL + ALT + CANC = disabilitato
    - DisableLockScreenAppNotifications = 1 o abilitato
    - IsContentDeliveryPolicyEnforced = 1 o true

## <a name="windows-10-password-reset"></a>Reimpostazione della password di Windows 10

### <a name="windows-10-prerequisites"></a>Prerequisiti di Windows 10

- Un amministratore deve abilitare Azure AD la reimpostazione della password self-service dall'portale di Azure.
- **Prima di usare questa funzionalità, gli utenti devono registrarsi per SSPR**
- Requisiti del proxy di rete
   - Dispositivi Windows 10 
       - Porta 443 per `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
       - I dispositivi Windows 10 supportano solo la configurazione del proxy a livello di computer
- Eseguire almeno Windows 10, versione aprile 2018 aggiornamento (v1803) e i dispositivi devono essere:
    - Aggiunta ad Azure AD
    - Aggiunta a Azure AD ibrido

### <a name="enable-for-windows-10-using-intune"></a>Abilitare per Windows 10 con Intune

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
1. Questo criterio può essere assegnato a utenti, dispositivi o gruppi specifici. Altre informazioni sono disponibili nell'articolo [assegnare profili utente e dispositivo in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Abilitare per Windows 10 usando il registro di sistema

1. Accedere al PC Windows con credenziali amministrative
1. Eseguire **regedit** come amministratore
1. Impostare la chiave del Registro di sistema seguente
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Risoluzione dei problemi relativi alla reimpostazione della password di Windows 10

Il log di controllo di Azure AD includerà informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Esempio di reimpostazione della password di Windows 7 nel registro di controllo Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando gli utenti reimpostano la password dalla schermata di accesso di un dispositivo Windows 10, viene creato un account temporaneo con privilegi limitati denominato `defaultuser1`. Questo account viene usato per proteggere il processo di reimpostazione della password. Lo stesso account ha una password generata casualmente, non viene visualizzato per l'accesso al dispositivo e verrà rimosso automaticamente dopo che l'utente avrà reimpostato la password. Possono esistere più profili di `defaultuser` ma possono essere ignorati in modo sicuro.

## <a name="windows-7-8-and-81-password-reset"></a>Reimpostazione della password di Windows 7, 8 e 8,1

### <a name="windows-7-8-and-81-prerequisites"></a>Prerequisiti di Windows 7, 8 e 8,1

- Un amministratore deve abilitare Azure AD la reimpostazione della password self-service dall'portale di Azure.
- **Prima di usare questa funzionalità, gli utenti devono registrarsi per SSPR**
- Requisiti del proxy di rete
   - Dispositivi Windows 7, 8 e 8,1
       - Porta 443 `passwordreset.microsoftonline.com`
- Sistema operativo Windows 7 con patch o Windows 8.1.
- Protocollo TLS 1.2 abilitato seguendo le indicazioni riportate in [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) (Impostazioni del Registro di sistema di Transport Layer Security - TLS).
- Se nel computer è abilitato più di un provider di credenziali di terze parti, gli utenti visualizzeranno più di un profilo utente nella schermata di accesso.

> [!WARNING]
> È necessario abilitare TLS 1,2, non solo impostare la negoziazione automatica

### <a name="install"></a>Installazione

1. Scaricare il programma di installazione appropriato per la versione di Windows che si vuole abilitare.
   - Il software è disponibile nell'Area download Microsoft all'indirizzo [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Accedere al computer in cui si vuole effettuare l'installazione ed eseguire il programma di installazione.
1. Al termine dell'installazione è consigliabile riavviare il computer.
1. Dopo il riavvio, nella schermata di accesso scegliere un utente e fare clic su "password dimenticata?" per avviare il flusso di lavoro di reimpostazione della password.
1. Completare il flusso di lavoro seguendo i passaggi visualizzati sullo schermo per reimpostare la password.

![Esempio dell'opzione "Password dimenticata?" selezionata in Windows 7 Flusso SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installazione invisibile all'utente

- Per l'installazione invisibile all'utente, usare il comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Per la disinstallazione invisibile all'utente, usare il comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Risoluzione dei problemi relativi alla reimpostazione della password di Windows 7, 8 e 8,1

Gli eventi verranno registrati sia nel computer sia in Azure AD. Gli eventi di Azure AD includeranno informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Esempio di reimpostazione della password di Windows 7 nel registro di controllo Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se è necessaria una registrazione aggiuntiva, è possibile modificare una chiave del Registro di sistema per abilitare la registrazione dettagliata. Abilitare la registrazione dettagliata solo per scopi di risoluzione dei problemi.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Per abilitare la registrazione dettagliata, creare un `REG_DWORD: “EnableLogging”`e impostarlo su 1.
- Per disabilitare la registrazione dettagliata, impostare il `REG_DWORD: “EnableLogging”` su 0.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

A questo punto, dopo aver configurato la reimpostazione della password per i dispositivi Windows, quali modifiche sono state apportate all'utente? Come capisce che può reimpostare la password nella schermata di accesso?

![Esempi di schermate di accesso di Windows 7 e 10 con collegamento SSPR visualizzato](./media/howto-sspr-windows/windows-reset-password.png)

Quando gli utenti tentano di eseguire l'accesso, ora visualizzano un collegamento **Reimposta password** o **password dimenticata** che apre l'esperienza di reimpostazione della password self-service nella schermata di accesso. Questa funzionalità consente agli utenti di reimpostare la password senza dover usare un altro dispositivo per accedere a un Web browser.

Gli utenti troveranno indicazioni per l'uso di questa funzionalità in [Reimpostare la password per un account aziendale o dell'istituto di istruzione](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Passaggi successivi

[Pianificare i metodi di autenticazione da consentire](concept-authentication-methods.md)

[Configurare Windows 10](https://docs.microsoft.com/windows/configuration/)
