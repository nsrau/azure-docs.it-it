---
title: Distribuire workstation gestite da Azure-Azure Active Directory
description: Informazioni su come distribuire workstation sicure gestite da Azure per ridurre il rischio di violazione a causa di una configurazione errata o un compromesso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d713dd968956f5bcc93e7b53ed2d7801e5d7bec2
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561941"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuire una workstation protetta gestita da Azure

Ora che si [conoscono le workstation sicure](concept-azure-managed-workstation.md), è il momento di iniziare il processo di distribuzione. Con queste linee guida, si usano i profili definiti per creare una workstation più sicura dall'inizio.

![Distribuzione di una workstation sicura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selezionare un profilo prima di distribuire la soluzione. È possibile utilizzare più profili contemporaneamente in una distribuzione e assegnarli con tag o gruppi.

> [!NOTE]
> Applicare i profili in base alle esigenze. È possibile passare a un altro profilo assegnando il Microsoft Intune.

| Profilo | Basse | Avanzato | Alte | Specializzata | Assicurato | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utente in Azure AD | SÌ | SÌ | SÌ | SÌ | SÌ | SÌ |
| Gestione di Intune | SÌ | SÌ | SÌ | SÌ | SÌ | SÌ |
| Azure AD dispositivo registrato | SÌ |  |  |  |  | |   |
| Dispositivo-Azure AD aggiunto |   | SÌ | SÌ | SÌ | SÌ | SÌ |
| Baseline della sicurezza di Intune applicata |   | SÌ <br> Enhanced | SÌ <br> (HighSecurity) | SÌ <br> NCSC | SÌ <br> Assicurato | ND |
| L'hardware soddisfa gli standard di Windows 10 protetti |   | SÌ | SÌ | SÌ | SÌ | SÌ |
| Microsoft Defender ATP abilitato |   | SÌ  | SÌ | SÌ | SÌ | SÌ |
| Rimozione dei diritti di amministratore |   |   | SÌ  | SÌ | SÌ | SÌ |
| Distribuzione con Microsoft Autopilot |   |   | SÌ  | SÌ | SÌ | SÌ |
| App installate solo da Intune |   |   |   | SÌ | SÌ |SÌ |
| URL limitati all'elenco approvato |   |   |   | SÌ | SÌ |SÌ |
| Internet bloccato (in ingresso/in uscita) |   |   |   |  |  |SÌ |

> [!NOTE]
> Nei **dispositivi** delle linee guida per workstation sicure verranno assegnati i profili e i criteri. Agli utenti non verrà applicato alcun criterio direttamente, consentendo la condivisione dei dispositivi (dispositivi condivisi). Se una workstation protetta non è condivisa nella distribuzione o se sono necessari criteri utente singoli, l'assegnazione dei profili criteri utente può essere assegnata all'utente e al dispositivo. 

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I concetti trattati in questa guida presuppongono che sia Microsoft 365 Enterprise E5 o uno SKU equivalente. Alcuni consigli in questa guida possono essere implementati con SKU inferiori. Per ulteriori informazioni, vedere [Microsoft 365 Enterprise Licensing](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Per automatizzare il provisioning delle licenze, prendere in considerazione le [licenze basate sui gruppi](../users-groups-roles/licensing-groups-assign.md) per gli utenti.

## <a name="azure-active-directory-configuration"></a>Configurazione di Azure Active Directory

Azure Active Directory (Azure AD) gestisce utenti, gruppi e dispositivi per le workstation di amministratore. Abilitare i servizi di identità e le funzionalità con un [account amministratore](../users-groups-roles/directory-assign-admin-roles.md).

Quando si crea l'account amministratore della workstation protetto, questo viene esposto alla workstation corrente. Assicurarsi di usare un dispositivo sicuro noto per eseguire questa configurazione iniziale e tutta la configurazione globale. Per ridurre l'esposizione agli attacchi per la prima volta, è consigliabile seguire le [indicazioni per evitare infezioni da malware](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Richiedere l'autenticazione a più fattori, almeno per gli amministratori. Vedere Distribuire l'autenticazione a più fattori [basata sul cloud](../authentication/howto-mfa-getstarted.md) per le linee guida di implementazione.

### <a name="azure-ad-users-and-groups"></a>Utenti e gruppi Azure AD

1. Dalla portale di Azure individuare **Azure Active Directory** > **utenti** > **nuovo utente**.
1. Creare l'amministratore del dispositivo attenendosi alla procedura descritta nell' [esercitazione creare un utente](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Digitare:

   * **Nome** : amministratore workstation sicuro
   * **Nome utente** - `secure-ws-admin@identityitpro.com`
   * **Ruolo della Directory** - **amministratore con limitazioni** e selezionare il ruolo di **amministratore di Intune** .

1. Selezionare **Create** (Crea).

Si creeranno quindi due gruppi: utenti workstation e dispositivi workstation.

Dalla portale di Azure individuare **Azure Active Directory** > **gruppi** > **nuovo gruppo**.

1. Per il gruppo utenti workstation, potrebbe essere necessario configurare le [licenze basate sui gruppi](../users-groups-roles/licensing-groups-assign.md) per automatizzare il provisioning delle licenze agli utenti.
1. Per il gruppo utenti workstation, immettere:

   * **Tipo di gruppo** -sicurezza
   * **Nome gruppo** -utenti workstation protetti
   * **Tipo di appartenenza** : assegnato

1. Aggiungere l'utente amministratore della workstation protetta: `secure-ws-admin@identityitpro.com`
1. È possibile aggiungere altri utenti che gestiranno le workstation protette.
1. Selezionare **Create** (Crea).
1. Per il gruppo dispositivi workstation, immettere:

   * **Tipo di gruppo** -sicurezza
   * **Nome gruppo** -workstation sicure
   * **Tipo di appartenenza** : assegnato

1. Selezionare **Create** (Crea).

### <a name="azure-ad-device-configuration"></a>Configurazione del dispositivo Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Specificare gli utenti che possono aggiungere dispositivi a Azure AD

Configurare l'impostazione dei dispositivi in Active Directory per consentire al gruppo di sicurezza amministrativo di aggiungere dispositivi al dominio. Per configurare questa impostazione dalla portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni del dispositivo**.
1. Scegliere **selezionato** in **utenti può aggiungere dispositivi a Azure ad**e quindi selezionare il gruppo "utenti workstation protetti".

#### <a name="removal-of-local-admin-rights"></a>Rimozione dei diritti di amministratore locale

Questo metodo richiede che gli utenti delle workstation VIP, DevOps e a livello di sicurezza non dispongano dei diritti di amministratore per i computer. Per configurare questa impostazione dalla portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni del dispositivo**.
1. Selezionare **nessuno** in **Administrators locale aggiuntivo nei dispositivi Azure ad aggiunti**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Richiedere l'autenticazione a più fattori per aggiungere dispositivi

Per rafforzare ulteriormente il processo di aggiunta dei dispositivi a Azure AD:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni del dispositivo**.
1. Selezionare **Sì** in **Richiedi autenticazione a più fattori per aggiungere dispositivi**.
1. Selezionare **Salva**.

#### <a name="configure-mobile-device-management"></a>Configurare la gestione dei dispositivi mobili

Nel portale di Azure:

1. Passare a **Azure Active Directory** > **Mobility (MDM e MAM)**  > **Microsoft Intune**.
1. Modificare l'impostazione dell' **ambito utente MDM** su **tutti**.
1. Selezionare **Salva**.

Questi passaggi consentono di gestire qualsiasi dispositivo con Intune. Per altre informazioni, vedere [Guida introduttiva di Intune: configurare la registrazione automatica per i dispositivi Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). I criteri di conformità e configurazione di Intune vengono creati in un passaggio successivo.

#### <a name="azure-ad-conditional-access"></a>Accesso condizionale di Azure AD

Azure AD l'accesso condizionale può contribuire a limitare le attività amministrative con privilegi ai dispositivi conformi. Per eseguire l'autenticazione a più fattori per l'accesso alle applicazioni cloud sono necessari membri predefiniti del gruppo **utenti workstation protetti** . Una procedura consigliata consiste nell'escludere gli account di accesso di emergenza dal criterio. Per altre informazioni, vedere [gestire gli account di accesso di emergenza in Azure ad](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Configurazione di Intune

### <a name="configure-enrollment-status"></a>Configurare lo stato di registrazione

È importante assicurarsi che la workstation protetta sia un dispositivo pulito attendibile. Quando si acquistano nuovi dispositivi, è possibile insistere sul fatto che il Factory è impostato su [Windows 10 Pro in modalità S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), che limita l'esposizione alle vulnerabilità durante la gestione della supply chain. Dopo aver ricevuto un dispositivo dal fornitore, è possibile utilizzare Autopilot per modificarlo dalla modalità S. Le linee guida seguenti forniscono informazioni dettagliate sull'applicazione del processo di trasformazione.

Per assicurarsi che i dispositivi siano completamente configurati prima dell'uso, Intune fornisce un mezzo per **bloccare l'uso del dispositivo fino a quando non vengono installate tutte le app e i profili**.

Dal **portale di Azure**:

1. Passare a **Microsoft Intune** > **registrazione del dispositivo** > registrazione di **Windows** > **pagina stato della registrazione** > **Impostazioni** **predefinite** di > .
1. Impostare **Mostra stato installazione profilo applicazione** su **Sì**.
1. Impostare l' **uso del dispositivo a blocchi finché tutte le app e i profili non vengono installati** su **Sì**.

### <a name="create-an-autopilot-deployment-profile"></a>Creare un profilo di distribuzione di Autopilot

Dopo aver creato un gruppo di dispositivi, è necessario creare un profilo di distribuzione per configurare i dispositivi Autopilot.

In Intune nel portale di Azure:

1. Selezionare **registrazione del dispositivo** > **registrazione Windows** > **profili di distribuzione** > **Crea profilo**.
1. Digitare:

   * Nome: **profilo di distribuzione della workstation sicuro**.
   * Descrizione: **distribuzione di workstation protette**.
   * Impostare **Converti tutti i dispositivi di destinazione su AUTOPILOT** su **Sì**. Questa impostazione garantisce che tutti i dispositivi nell'elenco vengano registrati con il servizio di distribuzione Autopilot. Consenti l'elaborazione della registrazione per 48 ore.

1. Selezionare **Avanti**.

   * Per la **modalità di distribuzione**, scegliere **distribuzione automatica (anteprima)** . I dispositivi con questo profilo sono associati all'utente che esegue la registrazione del dispositivo. Per registrare il dispositivo sono necessarie le credenziali utente. È essenziale tenere presente che la distribuzione di un dispositivo nella modalità di **distribuzione automatica** consentirà di distribuire computer portatili in un modello condiviso. Non verrà eseguita alcuna assegnazione utente fino a quando il dispositivo non viene assegnato a un utente per la prima volta. Di conseguenza, tutti i criteri utente, ad esempio BitLocker, non verranno abilitati finché non viene completata l'assegnazione di un utente. Per ulteriori informazioni su come accedere a un dispositivo protetto, vedere [profili selezionati](https://docs.microsoft.com/intune/device-profile-assign).
   * La casella **Aggiungi a Azure ad come** dovrebbe mostrare **Azure ad Unite** ed essere disabilitata.
   * Selezionare la lingua (regione), tipo di account utente **standard**. 

1. Selezionare **Avanti**.

   * Selezionare un tag di ambito se ne è stato preconfigurato uno.

1. Selezionare **Avanti**.
1. Scegliere **assegnazioni** > **assegna a** > **gruppi selezionati**. In **Seleziona gruppi da includere**scegliere **workstation sicure**.
1. Selezionare **Avanti**.
1. Selezionare **Crea** per creare il profilo. Il profilo di distribuzione Autopilot è ora disponibile per l'assegnazione ai dispositivi.

La registrazione dei dispositivi in Autopilot offre un'esperienza utente diversa in base al tipo di dispositivo e al ruolo. Nell'esempio di distribuzione viene illustrato un modello in cui i dispositivi protetti vengono distribuiti in blocco e possono essere condivisi, ma quando vengono usati per la prima volta, il dispositivo viene assegnato a un utente. Per ulteriori informazioni, vedere la pagina relativa alla [registrazione dei dispositivi con Autopilot di Intune](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configurare Windows Update

Mantenere aggiornato Windows 10 è uno degli elementi più importanti. Per mantenere Windows in uno stato sicuro, si distribuisce un anello di aggiornamento per gestire la velocità con cui gli aggiornamenti vengono applicati alle workstation. 

Questa guida consiglia di creare un nuovo anello di aggiornamento e modificare le impostazioni predefinite seguenti:

Nel portale di Azure:

1. Passare a **Microsoft Intune** > **aggiornamenti software > gli** **anelli di aggiornamento di Windows 10**.
1. Digitare:

   * Nome- **aggiornamenti della workstation gestita da Azure**
   * Canale di manutenzione- **Windows Insider-Fast**
   * Differimento dell'aggiornamento qualitativo (giorni)- **3**
   * Periodo di differimento dell'aggiornamento delle funzionalità (giorni)- **3**
   * Comportamento aggiornamento automatico- **installazione automatica e riavvio senza controllo utente finale**
   * Blocca l'utente dalla sospensione degli aggiornamenti di Windows- **blocco**
   * Richiedi l'approvazione dell'utente per il riavvio fuori orario di lavoro- **obbligatorio**
   * Consenti all'utente di riavviare (riavvio attivato)- **obbligatorio**
   * Transizione degli utenti al riavvio eseguito dopo un riavvio automatico (giorni)- **3**
   * Promemoria riavvio attivato per Snooze (giorni)- **3**
   * Imposta la scadenza per i riavvii in sospeso (giorni)- **3**

1. Selezionare **Create** (Crea).
1. Nella scheda **assegnazioni** aggiungere il gruppo **workstation sicure** .

Per ulteriori informazioni sui criteri di Windows Update, vedere [policy CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrazione di Windows Defender ATP Intune

Windows Defender ATP e Microsoft Intune interagiscono per prevenire le violazioni della sicurezza. Possono anche limitare l'effetto delle violazioni. Le funzionalità ATP forniscono funzionalità di rilevamento delle minacce in tempo reale e consentono il controllo e la registrazione estensivi dei dispositivi endpoint.

Per configurare l'integrazione di Windows Defender ATP e Intune, passare alla portale di Azure.

1. Passare a **Microsoft Intune** > **conformità del dispositivo** > **Windows Defender ATP**.
1. Nel passaggio 1, in **Configuring Windows Defender ATP**, selezionare **Connect Windows defender ATP to Microsoft Intune in Windows Defender Security Center**.
1. In Windows Defender Security Center:

   1. Selezionare **impostazioni** > **funzionalità avanzate**.
   1. Per **Microsoft Intune connessione**, scegliere **on**.
   1. Selezionare **Salva preferenze**.

1. Una volta stabilita la connessione, tornare a Intune e selezionare **Aggiorna** nella parte superiore.
1. Impostare **Connetti dispositivi Windows versione 10.0.15063 e successive a Windows Defender ATP** **su on**.
1. Selezionare **Salva**.

Per altre informazioni, vedere [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Completa protezione avanzata profili workstation

Per completare correttamente la protezione avanzata della soluzione, scaricare ed eseguire lo script appropriato. Trovare i collegamenti di download per il **livello di profilo**desiderato:

| Profilo | Percorso di download | Nome file |
| --- | --- | --- |
| Sicurezza bassa | N/D | N/D |
| Sicurezza avanzata | https://aka.ms/securedworkstationgit | Enhanced-workstation-Windows10-(1809). ps1 |
| Sicurezza elevata | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Specializzata | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Conformità specializzata * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Assicurato | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline. ps1 |

\* conformità specializzata è uno script che impone la configurazione specializzata fornita in NCSC Windows10 SecurityBaseline.

Una volta eseguito correttamente lo script, è possibile effettuare aggiornamenti a profili e criteri in Intune. Gli script per i profili avanzati e protetti creano i criteri e i profili per l'utente, ma è necessario assegnare il criterio al gruppo di dispositivi per le **workstation sicure** .

* Qui è possibile trovare i profili di configurazione dei dispositivi di Intune creati dagli script: **portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **profili**.
* Qui è possibile trovare i criteri di conformità dei dispositivi di Intune creati dagli script: **portale di Azure** > **Microsoft Intune** > **criteri**di **conformità del dispositivo** > .

Per esaminare le modifiche apportate dagli script, è possibile esportare i profili. In questo modo è possibile determinare ulteriori finalizzazione che potrebbero essere richieste come indicato nella documentazione di [Seccon](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Eseguire lo script di esportazione dei dati di Intune `DeviceConfiguration_Export.ps1` dal [repository DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) per esportare tutti i profili Intune correnti.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurazioni aggiuntive e protezione avanzata da considerare

Seguendo le istruzioni riportate in questo articolo, è stata distribuita una workstation protetta. È tuttavia necessario considerare anche controlli aggiuntivi. ad esempio:

* limitare l'accesso ai browser alternativi
* Consenti HTTP in uscita
* Blocca selezione siti Web
* impostare le autorizzazioni per l'esecuzione di script di PowerShell personalizzati

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Impostare le regole nel provider del servizio di configurazione firewall (CSP)

È possibile apportare ulteriori modifiche alla gestione delle regole in ingresso e in uscita in base alle esigenze per gli endpoint consentiti e bloccati. Quando si continua a rafforzare la workstation protetta, è possibile allentare la restrizione che nega tutto il traffico in ingresso e in uscita. È possibile aggiungere siti in uscita consentiti per includere siti Web comuni e attendibili. Per altre informazioni, vedere [servizio di configurazione del firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

La gestione del traffico URL restrittiva include:

* Nega tutto il traffico in ingresso impostato nello script del profilo workstation protetto.
* Nega tutte le connessioni in uscita, ad eccezione di Azure e dei servizi Microsoft, inclusi Azure Cloud Shell e la possibilità di consentire la reimpostazione della password di Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Se si vuole fornire una maggiore granularità alle regole di blocco, è possibile fare riferimento al progetto Spamhaus che gestisce [l'elenco di blocchi di dominio (doppia)](https://www.spamhaus.org/dbl/): una risorsa efficace da usare come set avanzato di regole da implementare per bloccare i siti.

### <a name="manage-local-applications"></a>Gestire le applicazioni locali

La workstation protetta passa a uno stato effettivamente finalizzato quando vengono rimosse le applicazioni locali, incluse le applicazioni di produttività. Qui si aggiunge Chrome come browser predefinito e si usa Intune per limitare la capacità di un utente di modificare il browser e i relativi plug-in.

#### <a name="deploy-applications-using-intune"></a>Distribuire applicazioni con Intune

In alcune situazioni, le applicazioni come il browser Google Chrome sono necessarie nella workstation protetta. Nell'esempio seguente vengono fornite le istruzioni per installare Chrome nei dispositivi del gruppo di sicurezza **workstation protette**.

1. Scaricare il bundle Chrome del programma di installazione offline [per Windows 64 bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Estrarre i file e prendere nota del percorso del file di `GoogleChromeStandaloneEnterprise64.msi`.
1. Nella **portale di Azure** passare a **Microsoft Intune** > **app client** > **app** > **Aggiungi**.
1. In **tipo di app**scegliere **line-of-business**.
1. In **file pacchetto app**selezionare il file `GoogleChromeStandaloneEnterprise64.msi` dal percorso estratto e selezionare **OK**.
1. In **informazioni sull'app**specificare una descrizione e un server di pubblicazione. Selezionare **OK**.
1. Selezionare **Aggiungi**.
1. Nella scheda **assegnazioni** selezionare **disponibile per i dispositivi registrati** in **tipo di assegnazione**.
1. In **gruppi inclusi**aggiungere il gruppo **workstation sicure** .
1. Selezionare **OK** e quindi **Salva**.

Per altre informazioni sulla configurazione delle impostazioni di Chrome, vedere [gestire il browser Chrome con Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurazione del portale aziendale per le app personalizzate

In modalità protetta, l'installazione dell'applicazione è limitata al portale aziendale di Intune. Tuttavia, l'installazione del portale richiede l'accesso a Microsoft Store. Nella soluzione protetta è possibile rendere disponibile il portale aziendale per tutti i dispositivi tramite una modalità offline.

Una copia gestita da Intune del [portale aziendale](https://docs.microsoft.com/Intune/store-apps-company-portal-app) offre accesso su richiesta a strumenti aggiuntivi che è possibile eseguire il push agli utenti delle workstation protette.

Potrebbe essere necessario installare app di Windows a 32 bit o altre app la cui distribuzione richiede speciali preparazioni. In questi casi, lo [strumento Microsoft Win32 content Prep](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) può fornire un file di formato `.intunewin` pronto per l'installazione.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Accesso condizionale che consente solo alla workstation protetta di accedere portale di Azure

Azure AD offre la possibilità di gestire e limitare, chi e cosa può accedere al portale di gestione cloud di Azure. Con l'abilitazione dell' [accesso condizionale](../conditional-access/overview.md) si garantisce che solo la workstation protetta possa gestire o modificare le risorse. È essenziale che durante la distribuzione di questa funzionalità si prenda in considerazione la possibilità di usare la funzionalità di [accesso di emergenza](../users-groups-roles/directory-emergency-access.md) solo per casi estremi e l'account gestito tramite criteri.

> [!NOTE]
> Sarà necessario creare un gruppo di utenti e includere l'utente di emergenza in grado di ignorare i criteri di accesso condizionale. Per questo esempio è presente un gruppo di sicurezza denominato **Emergency BreakGlass**

1. Passare alla **portale di Azure** > **Microsoft Intune** > **criteri di accesso condizionale** > **nuovo criterio**.
1. Consente di specificare un **nome** per il criterio.
1. Seleziona utenti e **gruppi** > **Seleziona utenti e gruppi** 
1. Selezionare **includi** > **ruoli della directory** > scegliere i ruoli > amministratore globale, amministratore del ruolo con privilegi, amministratore di autenticazione con privilegi, amministratore della sicurezza, amministratore della conformità, amministratore dell'accesso condizionale, amministratore dell'applicazione, amministratore di applicazioni cloud, amministratore del servizio Intune
1. Selezionare **escludi** > scegliere **utenti e gruppi** > selezionare **Seleziona utenti esclusi** > selezionare il gruppo di **BreakGlass di emergenza** .
1. Selezionare le **app Cloud o le azioni** > selezionare **tutte le app Cloud**
1. Selezionare **le condizioni** > **selezionare piattaforme del dispositivo** > scegliere Configura **Sì** > selezionare **Seleziona piattaforme del dispositivo** scegliere **Windows**
1. Selezionare **i controlli di accesso** > selezionare **concedi accesso** **Sì** > scegliere Richiedi che **il dispositivo sia contrassegnato come conforme**. 
1. Selezionare **abilita** > criteri **in**
 
Questo set di criteri garantisce che gli amministratori debbano usare un dispositivo Windows conforme, impostato da Intune e WDATP. 

Le organizzazioni devono inoltre prendere in considerazione la possibilità di bloccare i protocolli di autenticazione legacy negli ambienti. Sono disponibili diversi modi per eseguire questa attività. per altre informazioni sul blocco di protocolli di autenticazione legacy, vedere l'articolo [procedura: bloccare l'autenticazione legacy per Azure ad con accesso condizionale](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Usare PowerShell per creare impostazioni personalizzate

È anche possibile usare PowerShell per estendere le funzionalità di gestione degli host. Questo script di esempio imposta uno sfondo predefinito nell'host. Si tratta di una funzionalità che è disponibile anche tramite il portale di Azure e i profili. Lo script di esempio serve solo per illustrare la funzionalità di PowerShell.

Potrebbe essere necessario configurare alcuni controlli e impostazioni personalizzati nelle workstation protette. Questo esempio Mostra come modificare lo sfondo della workstation usando la funzionalità di PowerShell per identificare facilmente il dispositivo come una workstation sicura e pronta all'uso.

Lo script [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) di Microsoft Scripting Center consente a Windows di caricare l' [immagine di sfondo generica gratuita](https://i.imgur.com/OAJ28zO.png) all'avvio.

1. Scaricare lo script in un dispositivo locale.
1. Aggiornare customerXXXX e il percorso di download dell'immagine di sfondo. In questo esempio si sostituisce customerXXXX in backgrounds.
1. Passare alla **portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **script di PowerShell** > **Aggiungi**.
1. Fornire un **nome** per lo script e specificare il **percorso dello script**.
1. Selezionare **Configura**.
   1. Impostare **Esegui questo script utilizzando le credenziali di accesso su** **Sì**.
   1. Selezionare **OK**.
1. Selezionare **Create** (Crea).
1. Selezionare **assegnazioni** > **selezionare gruppi**.
   1. Aggiungere le **workstation protette**del gruppo di sicurezza.
   1. Selezionare **Salva**.

## <a name="enroll-and-validate-your-first-device"></a>Registrare e convalidare il primo dispositivo

1. Per registrare il dispositivo, sono necessarie le informazioni seguenti:
   * **Numero di serie** : disponibile nello chassis del dispositivo.
   * **ID prodotto Windows** -disponibile in **System** > **About** dal menu impostazioni di Windows.
   * È possibile eseguire [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) per ottenere un file hash CSV con tutte le informazioni necessarie per la registrazione del dispositivo.
   
     Eseguire `Get-WindowsAutoPilotInfo – outputfile device1.csv` per restituire le informazioni come file CSV che è possibile importare in Intune.

     > [!NOTE]
     > Lo script richiede diritti elevati. Viene eseguito come signed remoto. Il comando `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` consente l'esecuzione corretta dello script.

   * È possibile raccogliere queste informazioni eseguendo l'accesso a un dispositivo Windows 10 versione 1809 o superiore. Anche il rivenditore hardware può fornire queste informazioni.
1. Nella **portale di Azure**passare a **Microsoft Intune** > registrazione del **dispositivo** > **registrazione di Windows** **dispositivi > -gestire i dispositivi Windows Autopilot**.
1. Selezionare **Importa** e scegliere il file CSV.
1. Aggiungere il dispositivo al gruppo di sicurezza **workstation sicure** .
1. Nel dispositivo Windows 10 che si vuole configurare passare a impostazioni di **windows** > **aggiorna & sicurezza** > **ripristino**.
   1. Scegliere **Introduzione** in **Reimposta il PC**.
   1. Seguire le istruzioni per reimpostare e riconfigurare il dispositivo con il profilo e i criteri di conformità configurati.

Dopo aver configurato il dispositivo, completare una verifica e verificare la configurazione. Verificare che il primo dispositivo sia configurato correttamente prima di continuare la distribuzione.

## <a name="assign-devices"></a>Assegnare i dispositivi

Per assegnare dispositivi e utenti, è necessario eseguire il mapping dei [profili selezionati](https://docs.microsoft.com/intune/device-profile-assign) al gruppo di sicurezza. Tutti i nuovi utenti che richiedono le autorizzazioni per il servizio devono essere aggiunti anche al gruppo di sicurezza.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Uso di Sentinel e Windows Defender ATP per monitorare e rispondere agli eventi imprevisti della sicurezza

Il monitoraggio della distribuzione della workstation protetta può essere eseguito abilitando [Sentinel] e utilizzando la [gestione delle minacce e delle vulnerabilità](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) , le linee guida non forniscono una ricerca completa delle minacce, ma offrono un buon senso comune per monitorare e rispondere a potenziali incidenti di sicurezza.

**Azure Sentinel** viene usato per: 

* Raccogliere dati da Intune, portale di Azure e Azure AD per il monitoraggio di utenti e dispositivi
* Informazioni sulle attività sospette di configurazione di utenti e dispositivi
* E la possibilità di esplorare le indagini di sicurezza con WDATP

Il monitoraggio sentinella richiede la configurazione di connettori per le origini dati, ad esempio Azure AD.

1. Nella **portale di Azure**passare ad **Azure Sentinel (anteprima)** > selezionare **Aggiungi**
1. Nella pagina **scegliere un'area di lavoro da aggiungere ad Azure Sentinel** selezionare **Crea una nuova area di lavoro**
1. Digitare:
   * **Area di lavoro di log Analytics** -"monitoraggio della workstation sicura"
   * **Sottoscrizione** : selezionare la sottoscrizione attiva
   * **Gruppo di risorse** : selezionare la * * creazione di un nuovo * * > protezione RG della workstation > **OK**
   * **Località** : selezionare la località geograficamente più adatta per la distribuzione
   * Piano **tariffario** -seleziona **Per GB (2018)**
1. Selezionare **OK**.

Successivamente, si connetteranno le origini dati della workstation protetta disponibili al monitoraggio.

1. Nel **portale di Azure**passare all'area di **lavoro di Azure Sentinel** > selezionare **sicurezza workstation monitoraggio** area di lavoro
1. Selezionare **i connettori dati**
1. Scegliere **Azure Active Directory** > pagina apri connettore > dopo aver esaminato il prerequisito. Passare a Configuration (configurazione) e selezionare **Connect (Connetti** ) per i log di accesso Azure ad, nonché Azure ad log di controllo.
1. Scegliere **attività di Azure** > > pagina Apri connettore dopo aver esaminato il prerequisito. Passare a configurare i log attività di Azure > selezionare la sottoscrizione > selezionare **Connetti**

Man mano che i dati vengono raccolti da Sentinel, sarà possibile osservare l'attività selezionando **portale di Azure**, passare ad **Azure Sentinel Overview** 

**Microsoft userà Windows Defender ATP (WDATP)** per:

* Osservare e monitorare continuamente le vulnerabilità e le configurazioni errate
* USA WDATP per definire la priorità delle minacce dinamiche in modo naturale
* Correlazione delle vulnerabilità con gli avvisi di rilevamento e risposta degli endpoint
* Usare il dashboard per identificare la vulnerabilità a livello di computer durante le indagini
* Eseguire il push delle correzioni in Intune

Configurare il [dashboard di Defender ATP](https://securitycenter.windows.com/machines). Uso delle linee guida per le [minacce & panoramica del dashboard di gestione delle vulnerabilità](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitoraggio dell'attività delle applicazioni mediante Microsoft Monitoring Agent (MMA)
A partire dalla workstation specializzata, app Locker è abilitato per il monitoraggio delle attività dell'applicazione in una workstation. Affinché il monitoraggio venga integrato nell'area di lavoro di Log Analytics, è necessario seguire un agente e una configurazione MMA. 

> [!NOTE]
> Il profilo workstation specializzato contiene i criteri di monitoraggio di AppLocker. La distribuzione dei criteri è necessaria per il monitoraggio delle attività dell'applicazione in un client

Distribuire l'agente MMA con lo script di PowerShell per Intune

1. Scaricare lo [script di installazione in un dispositivo locale](https://aka.ms/securedworkstationgit).
1. Aggiornare i parametri, **$WorkSpaceID** e **$WorkSpaceKey**
1. Passare alla **portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **script di PowerShell** > **Aggiungi**.
1. Fornire un **nome** per lo script e specificare il **percorso dello script**.
1. Selezionare **Configura**.
   1. Impostare **Esegui questo script utilizzando le credenziali di accesso su** **Sì**.
   1. Selezionare **OK**.
1. Selezionare **Create** (Crea).
1. Selezionare **assegnazioni** > **selezionare gruppi**.
   1. Aggiungere le **workstation protette**del gruppo di sicurezza.
   1. Selezionare **Salva**.

Successivamente, è necessario configurare Log Analytics per ricevere i nuovi log
1. Nella **portale di Azure**passare all'area di **lavoro log Analytics** > selezionare-"monitoraggio workstation sicuro".
1. Selezionare **Impostazioni avanzate** > **dati** > **registri eventi di Windows**
1. In **Raccogli eventi dai log eventi seguenti** 
1. Digitare:
   * ' Microsoft-Windows-AppLocker/EXE e DLL ' > deselezionare **le informazioni**
   * ' Microsoft-Windows-AppLocker/MSI e script ' > deselezionare **le informazioni**
   * ' Microsoft-Windows-AppLocker/Packaged App-Deployment ' > deselezionare **le informazioni**
   * ' Microsoft-Windows-AppLocker/app in pacchetto-esecuzione ' > deselezionare **le informazioni**
1. Selezionare **Salva**

La registrazione dell'applicazione sarà disponibile nell'area di lavoro Log Analytics selezionata.

## <a name="monitoring"></a>Monitorare

* Informazioni su come [rilevare le minacce con Azure Sentinel](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)
* [Esaminare gli eventi imprevisti con Sentinel di Azure](https://docs.microsoft.com/azure/sentinel/tutorial-investigate-cases)
* [Configurare le risposte automatiche alle minacce in Sentinel di Azure](https://docs.microsoft.com/azure/sentinel/tutorial-respond-threats-playbook)
* Informazioni su come verificare il [Punteggio di esposizione](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Verifica [raccomandazione sulla sicurezza](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Gestire le [correzioni](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) di sicurezza
* Gestire il [rilevamento e la risposta degli endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitorare i profili con [il monitoraggio](https://docs.microsoft.com/intune/device-profile-monitor)dei profili di Intune.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Informazioni [Azure ad](../index.yml).
* Usare [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Scopri [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
