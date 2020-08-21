---
title: Reimpostazione della password self-service per i dispositivi Windows-Azure Active Directory
description: Informazioni su come abilitare la reimpostazione della password self-service Azure Active Directory nella schermata di accesso di Windows.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51d8c45f652173e5b2b0731d64a8e6f14ee46c7
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717354"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Abilitare la reimpostazione della password self-service Azure Active Directory nella schermata di accesso di Windows

La reimpostazione della password self-service (SSPR) consente agli utenti di Azure Active Directory (Azure AD) di modificare o reimpostare la password, senza alcun intervento da parte di amministratore o di help desk. In genere, gli utenti aprono un Web browser in un altro dispositivo per accedere al [portale di SSPR](https://aka.ms/sspr). Per migliorare l'esperienza nei computer che eseguono Windows 7, 8, 8,1 e 10, è possibile consentire agli utenti di reimpostare la password nella schermata di accesso di Windows.

![Esempi di schermate di accesso di Windows 7 e 10 con collegamento alla reimpostazione della password self-service visualizzato](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> Questa esercitazione mostra un amministratore come abilitare SSPR per i dispositivi Windows in un'organizzazione.
>
> Se il team IT non ha consentito di usare SSPR dal dispositivo Windows o si verificano problemi durante l'accesso, rivolgersi al supporto tecnico per assistenza aggiuntiva.

## <a name="general-limitations"></a>Limitazioni generali

Le limitazioni seguenti si applicano all'uso di SSPR dalla schermata di accesso di Windows:

- La reimpostazione della password non è attualmente supportata da un Desktop remoto o da sessioni avanzate di Hyper-V.
- Alcuni provider di credenziali di terze parti sono noti per causare problemi con questa funzionalità.
- La disabilitazione del controllo dell'account utente tramite la modifica della [chiave del registro di sistema EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) è nota per causare problemi.
- Questa funzionalità non funziona per le reti con autenticazione di rete 802.1 x distribuita e l'opzione "Esegui immediatamente prima dell'accesso utente". Per le reti con autenticazione di rete 802.1 x distribuita, è consigliabile usare l'autenticazione del computer per abilitare questa funzionalità.
- Per usare la nuova password e aggiornare le credenziali memorizzate nella cache, è necessario che i computer aggiunti ad Azure AD ibrido abbiano la connettività di rete associata a un controller di dominio. Ciò significa che i dispositivi devono trovarsi nella rete interna dell'organizzazione o in una VPN con accesso di rete a un controller di dominio locale.
- Se si usa un'immagine, assicurarsi che la cache Web sia stata cancellata per l'amministratore predefinito prima di eseguire il passaggio CopyProfile di Sysprep. Per altre informazioni su questo passaggio, vedere l'articolo di supporto [Prestazioni insufficienti quando si usa il profilo utente predefinito personalizzato](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Le impostazioni seguenti sono note per interferire con la possibilità di usare e reimpostare le password nei dispositivi Windows 10:
    - Se la combinazione di tasti CTRL + ALT + CANC è necessaria per i criteri nelle versioni di Windows 10 prima di v1809, **reimpostare la password** non funzionerà.
    - Se le notifiche della schermata di blocco sono spente, **reimpostare la password** non funzionerà.
    - *HideFastUserSwitching* è impostato su Enabled o su 1
    - *DontDisplayLastUserName* è impostato su Enabled o su 1
    - *Nolockscreen* è impostato su abilitato o su 1
    - *EnableLostMode* è impostato sul dispositivo
    - Explorer.exe è sostituito con una shell personalizzata
- La combinazione delle tre impostazioni specifiche seguenti può causare un funzionamento non corretto di questa funzionalità.
    - Accesso interattivo: Non richiede CTRL+ALT+CANC = Disattivato
    - *DisableLockScreenAppNotifications* = 1 o abilitato
    - Lo SKU di Windows non è l'edizione Home o Professional

## <a name="windows-10-password-reset"></a>Reimpostazione della password di Windows 10

Per configurare un dispositivo Windows 10 per SSPR nella schermata di accesso, esaminare i prerequisiti e i passaggi di configurazione seguenti.

### <a name="windows-10-prerequisites"></a>Prerequisiti di Windows 10

- Un amministratore [deve abilitare Azure ad la reimpostazione della password self-service dall'portale di Azure](tutorial-enable-sspr.md).
- Prima di usare questa funzionalità, gli utenti devono registrarsi a SSPR [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Non univoco per l'uso di SSPR dalla schermata di accesso di Windows, tutti gli utenti devono fornire le informazioni di contatto per l'autenticazione prima di poter reimpostare la password.
- Requisiti del proxy di rete:
    - Porta 443 per `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
    - I dispositivi Windows 10 supportano solo la configurazione del proxy a livello di computer.
- Eseguire almeno Windows 10, versione aggiornamento di aprile 2018 (v1803) e i dispositivi devono essere:
    - Aggiunta ad Azure AD
    - Aggiunta a Azure AD ibrido

### <a name="enable-for-windows-10-using-intune"></a>Abilitare per Windows 10 con Intune

La distribuzione della modifica della configurazione per abilitare SSPR dalla schermata di accesso con Intune è il metodo più flessibile. Intune consente di distribuire la modifica della configurazione a uno specifico gruppo di computer definito. Questo metodo richiede la registrazione del dispositivo a Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Creare criteri di configurazione dei dispositivi in Intune

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Intune**.
1. Creare un nuovo profilo di configurazione del dispositivo passando a profili di **configurazione del dispositivo**  >  **Profiles**, quindi selezionare **+ Crea profilo**
   - Per la **piattaforma** scegliere *Windows 10 e versioni successive*
   - Per **tipo di profilo**scegliere *personalizzato* .
1. Selezionare **Crea**e quindi specificare un nome significativo per il profilo, ad esempio la *schermata di accesso di Windows 10 SSPR*

    Facoltativamente, fornire una descrizione significativa del profilo, quindi selezionare **Avanti**.
1. In *impostazioni di configurazione*selezionare **Aggiungi** e specificare l'impostazione URI OMA seguente per abilitare il collegamento Reimposta password:
      - Fornire un nome significativo per spiegare il funzionamento dell'impostazione, ad esempio *Aggiungi collegamento SSPR*.
      - Facoltativamente, fornire una descrizione significativa dell'impostazione.
      - Impostare **URI OMA** su `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Impostare **Tipo di dati** su **Integer**
      - Impostare **Valore** su **1**

    Selezionare **Aggiungi**e quindi **Avanti**.
1. Il criterio può essere assegnato a utenti, dispositivi o gruppi specifici. Assegnare il profilo come desiderato per l'ambiente in uso, idealmente a un gruppo di dispositivi di test, quindi fare clic su **Avanti**.

    Per altre informazioni, vedere [assegnare profili utente e dispositivo in Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Configurare le regole di applicabilità desiderate per l'ambiente in uso, ad esempio per *assegnare il profilo se l'edizione del sistema operativo è Windows 10 Enterprise*, quindi selezionare **Avanti**.
1. Esaminare il profilo, quindi selezionare **Crea**.

### <a name="enable-for-windows-10-using-the-registry"></a>Abilitare per Windows 10 con il registro

Per abilitare SSPR nella schermata di accesso con una chiave del registro di sistema, completare i passaggi seguenti:

1. Accedere al PC Windows con credenziali amministrative.
1. Premere **Windows**  +  **R** per aprire la finestra di dialogo *Esegui* , quindi eseguire **Regedit** come amministratore
1. Impostare la chiave del Registro di sistema seguente:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Risoluzione dei problemi relativi alla reimpostazione della password di Windows 10

In caso di problemi con l'uso di SSPR dalla schermata di accesso di Windows, il Azure AD log di controllo include informazioni sull'indirizzo IP e *TipoClient* in cui si è verificata la reimpostazione della password, come illustrato nell'output di esempio seguente:

![Esempio di reimpostazione della password di Windows 7 nel log di controllo di Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando gli utenti reimpostano la password dalla schermata di accesso di un dispositivo Windows 10, viene creato un account temporaneo con privilegi limitati `defaultuser1` . Questo account viene usato per proteggere il processo di reimpostazione della password.

L'account stesso ha una password generata in modo casuale, non viene visualizzato per l'accesso del dispositivo e viene rimosso automaticamente dopo la reimpostazione della password da parte dell'utente. Possono esistere più profili `defaultuser`, ma è possibile tranquillamente ignorarli.

## <a name="windows-7-8-and-81-password-reset"></a>Reimpostazione della password di Windows 7, 8 e 8.1

Per configurare un dispositivo Windows 7, 8 o 8,1 per SSPR nella schermata di accesso, esaminare i prerequisiti e i passaggi di configurazione seguenti.

### <a name="windows-7-8-and-81-prerequisites"></a>Prerequisiti di Windows 7, 8 e 8.1

- Un amministratore [deve abilitare Azure ad la reimpostazione della password self-service dall'portale di Azure](tutorial-enable-sspr.md).
- Prima di usare questa funzionalità, gli utenti devono registrarsi a SSPR [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Non univoco per l'uso di SSPR dalla schermata di accesso di Windows, tutti gli utenti devono fornire le informazioni di contatto per l'autenticazione prima di poter reimpostare la password.
- Requisiti del proxy di rete:
    - Porta 443 per `passwordreset.microsoftonline.com`
- Sistema operativo Windows 7 con patch o Windows 8.1.
- Protocollo TLS 1.2 abilitato seguendo le indicazioni riportate in [Transport Layer Security (TLS) registry settings](/windows-server/security/tls/tls-registry-settings#tls-12) (Impostazioni del Registro di sistema di Transport Layer Security - TLS).
- Se nel computer è abilitato più di un provider di credenziali di terze parti, gli utenti visualizzeranno più di un profilo utente nella schermata di accesso.

> [!WARNING]
> Il protocollo TLS 1.2 deve essere abilitato, non semplicemente impostato sulla negoziazione automatica.

### <a name="install"></a>Installazione

Per Windows 7, 8 e 8,1, è necessario che nel computer sia installato un piccolo componente per abilitare SSPR nella schermata di accesso. Per installare questo componente SSPR, completare i passaggi seguenti:

1. Scaricare il programma di installazione appropriato per la versione di Windows che si vuole abilitare.

    Il programma di installazione software è disponibile nell'area download Microsoft all'indirizzo [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Accedere al computer in cui si vuole effettuare l'installazione ed eseguire il programma di installazione.
1. Al termine dell'installazione è consigliabile riavviare il computer.
1. Dopo il riavvio, nella schermata di accesso scegliere un utente e selezionare "password dimenticata?". per avviare il flusso di lavoro di reimpostazione della password.
1. Completare il flusso di lavoro seguendo i passaggi visualizzati sullo schermo per reimpostare la password.

![Esempio dell'opzione "Password dimenticata?" selezionata in Windows 7 Flusso della reimpostazione della password self-service](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installazione invisibile all'utente

Il componente SSPR può essere installato o disinstallato senza richiedere l'uso dei comandi seguenti:

- Per l'installazione invisibile all'utente, usare il comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Per la disinstallazione invisibile all'utente, usare il comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Risoluzione dei problemi relativi alla reimpostazione della password di Windows 7, 8 e 8.1

Se si verificano problemi con l'uso di SSPR dalla schermata di accesso di Windows, gli eventi vengono registrati sia nel computer che in Azure AD. Gli eventi Azure AD includono le informazioni sull'indirizzo IP e TipoClient in cui si è verificata la reimpostazione della password, come illustrato nell'output di esempio seguente:

![Esempio di reimpostazione della password di Windows 7 nel log di controllo di Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se è necessaria una registrazione aggiuntiva, è possibile modificare una chiave del Registro di sistema per abilitare la registrazione dettagliata. Abilitare la registrazione dettagliata per la risoluzione dei problemi solo usando il valore della chiave del registro di sistema seguente:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Per abilitare la registrazione dettagliata, creare un `REG_DWORD: "EnableLogging"` e impostarlo su 1.
- Per disabilitare la registrazione dettagliata, modificare il `REG_DWORD: "EnableLogging"` su 0.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Con SSPR configurato per i dispositivi Windows, quali modifiche sono state apportate all'utente? Come capisce che può reimpostare la password nella schermata di accesso? Le schermate di esempio seguenti illustrano le opzioni aggiuntive che consentono a un utente di reimpostare la password usando SSPR:

![Esempi di schermate di accesso di Windows 7 e 10 con collegamento alla reimpostazione della password self-service visualizzato](./media/howto-sspr-windows/windows-reset-password.png)

Quando gli utenti tentano di eseguire l'accesso, visualizzano un collegamento **Reimposta password** o **password dimenticata** che apre l'esperienza di reimpostazione della password self-service nella schermata di accesso. Questa funzionalità consente agli utenti di reimpostare la password senza dover usare un altro dispositivo per accedere a un Web browser.

Altre informazioni per gli utenti che usano questa funzionalità sono disponibili in [reimpostare la password aziendale o dell'Istituto di istruzione](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Passaggi successivi

Per semplificare l'esperienza di registrazione dell'utente, è possibile [pre-popolare le informazioni di contatto per l'autenticazione utente per SSPR](howto-sspr-authenticationdata.md).