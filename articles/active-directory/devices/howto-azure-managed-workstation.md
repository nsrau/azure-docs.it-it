---
title: Distribuire workstation gestite da Azure-Azure Active Directory
description: Informazioni su come distribuire workstation sicure gestite da Azure per ridurre il rischio di violazione a causa di una configurazione errata o un compromesso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377484"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuire una workstation protetta gestita da Azure

Ora che si [conoscono le workstation sicure](concept-azure-managed-workstation.md), è il momento di iniziare il processo di distribuzione. Con queste linee guida, si usano i profili definiti per creare una workstation più sicura dall'inizio.

![Distribuzione di una workstation sicura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Prima di poter distribuire la soluzione, è necessario selezionare un profilo. È possibile utilizzare più profili contemporaneamente in una distribuzione e assegnarli con tag o gruppi.
> [!NOTE]
> Applicare i profili in base alle esigenze. È possibile passare a un altro profilo mediante l'assegnazione in Intune.

| Profilo | Basso | Avanzato | High | Specializzato | Protetti | Isolato |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utente in Azure AD | Yes | Sì | Sì | Sì | Sì | Sì |
| Gestione di Intune | Sì | Sì | Sì | Sì | Sì | Sì |
| Azure AD dispositivo registrato | Sì |  |  |  |  | |   |
| Dispositivo-Azure AD aggiunto |   | Sì | Sì | Sì | Sì | Yes |
| Baseline della sicurezza di Intune applicata |   | Sì <br> Enhanced | Sì <br> (HighSecurity) | Sì <br> NCSC | Yes <br> Assicurato |  ND |
| L'hardware soddisfa gli standard di Windows 10 protetti |   | Sì | Sì | Sì | Sì | Sì |
| Microsoft Defender ATP abilitato |   | Sì  | Sì | Sì | Sì | Sì |
| Rimozione dei diritti di amministratore |   |   | Sì  | Sì | Sì | Sì |
| Distribuzione con Microsoft Autopilot |   |   | Sì  | Sì | Sì | Yes |
| App installate solo da Intune |   |   |   | Sì | Sì |Sì |
| URL limitati all'elenco approvato |   |   |   | Sì | Sì |Yes |
| Internet bloccato (in ingresso/in uscita) |   |   |   |  |  |Sì |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I concetti trattati in questa guida presuppongono che sia Microsoft 365 Enterprise E5 o uno SKU equivalente. Alcuni consigli in questa guida possono essere implementati con SKU inferiori. Per ulteriori informazioni, vedere [Microsoft 365 Enterprise Licensing](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Per automatizzare il provisioning delle licenze, prendere in considerazione le [licenze basate sui gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) per gli utenti.

## <a name="azure-active-directory-configuration"></a>Configurazione di Azure Active Directory

Azure Active Directory (Azure AD) gestisce utenti, gruppi e dispositivi per le workstation di amministratore. È necessario abilitare i servizi di identità e le funzionalità con un [account amministratore](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando si crea l'account amministratore della workstation protetto, questo viene esposto alla workstation corrente. Assicurarsi di usare un dispositivo sicuro noto per eseguire questa configurazione iniziale e tutta la configurazione globale. Per ridurre l'esposizione agli attacchi per la prima volta, è consigliabile seguire le [indicazioni per evitare infezioni da malware](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

È anche necessario richiedere l'autenticazione a più fattori, almeno per gli amministratori. Vedere Distribuire l'autenticazione a più fattori [basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) per le linee guida di implementazione.

### <a name="azure-ad-users-and-groups"></a>Utenti e gruppi Azure AD

1. Dalla portale di Azure individuare **Azure Active Directory** > **utenti** > **nuovo utente**.
1. Creare l'amministratore del dispositivo attenendosi alla procedura descritta nell' [esercitazione creare un utente](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Digitare:
   * **Nome** : amministratore workstation sicuro
   * **Nome utente** - `secure-ws-admin@identityitpro.com`
   * Ruolo - della directory con**privilegi di amministratore limitato** e selezionare il ruolo di **amministratore di Intune** .
1. Selezionare **Create**.

Si creeranno quindi due gruppi: utenti workstation e dispositivi workstation.

Dalla portale di Azure individuare **Azure Active Directory** > **gruppi** > **nuovo gruppo**.

1. Per il gruppo utenti workstation, potrebbe essere necessario configurare le [licenze basate sui gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) per automatizzare il provisioning delle licenze agli utenti.
1. Per il gruppo utenti workstation, immettere:
   * **Tipo di gruppo** -sicurezza
   * **Nome gruppo** -utenti workstation protetti
   * **Tipo di appartenenza** : assegnato
1. Aggiungere l'utente amministratore della workstation protetta:`secure-ws-admin@identityitpro.com`
1. È possibile aggiungere altri utenti che gestiranno le workstation protette.
1. Selezionare **Create**.

1. Per il gruppo dispositivi workstation, immettere:
   * **Tipo di gruppo** -sicurezza
   * **Nome gruppo** -workstation sicure
   * **Tipo di appartenenza** : assegnato
1. Selezionare **Create**.

### <a name="azure-ad-device-configuration"></a>Configurazione del dispositivo Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Specificare gli utenti che possono aggiungere dispositivi a Azure AD

Configurare l'impostazione dei dispositivi in Active Directory per consentire al gruppo di sicurezza amministrativo di aggiungere dispositivi al dominio. Per configurare questa impostazione dalla portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **Impostazioni dispositivo**.
1. Scegliere **selezionato** in **utenti può aggiungere dispositivi a Azure ad**e quindi selezionare il gruppo "utenti workstation protetti".

#### <a name="removal-of-local-admin-rights"></a>Rimozione dei diritti di amministratore locale

Questo metodo richiede che gli utenti delle workstation VIP, DevOps e a livello di sicurezza non dispongano dei diritti di amministratore per i computer. Per configurare questa impostazione dalla portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **Impostazioni dispositivo**.
1. Selezionare **nessuno** in **Administrators locale aggiuntivo nei dispositivi Azure ad aggiunti**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Richiedere l'autenticazione a più fattori per aggiungere dispositivi

Per rafforzare ulteriormente il processo di aggiunta dei dispositivi a Azure AD:

1. Passare a **Azure Active Directory** > **dispositivi** > **Impostazioni dispositivo**.
1. Selezionare **Sì** in **Richiedi autenticazione a più fattori per aggiungere dispositivi**.
1. Selezionare **Salva**.

#### <a name="configure-mdm"></a>Configurare MDM

Nel portale di Azure:

1. Passare a **Azure Active Directory** > **Mobility (MDM e MAM)**  > **Microsoft Intune**.
1. Modificare l'impostazione dell' **ambito utente MDM** su **tutti**.
1. Selezionare **Salva**.

Questi passaggi consentono di gestire qualsiasi dispositivo con Intune. Per altre informazioni, vedere [Guida introduttiva a Intune: Configurare la registrazione automatica per i dispositivi](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)Windows 10. I criteri di conformità e configurazione di Intune vengono creati in un passaggio successivo.

#### <a name="azure-ad-conditional-access"></a>Accesso condizionale di Azure AD

Azure AD l'accesso condizionale può contribuire a limitare le attività amministrative con privilegi ai dispositivi conformi. Per eseguire l'autenticazione a più fattori per l'accesso alle applicazioni cloud sono necessari membri predefiniti del gruppo **utenti workstation protetti** . Una procedura consigliata consiste nell'escludere gli account di accesso di emergenza dal criterio. Per altre informazioni, vedere [gestire gli account di accesso di emergenza in Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Per configurare l'accesso condizionale dal portale di Azure:

1. Passare a **Azure Active Directory** > i**nuovi criteri**di**accesso** > condizionale.
1. Digitare:
   * **Nome** -criterio obbligatorio dispositivo sicuro
   * Assegnazioni
     * **Utenti e gruppi**
       * Includi- **utenti e gruppi** -selezionare il gruppo **utenti workstation protetti** creato in precedenza.
       * Escludi **utenti e gruppi** -selezionare gli account di accesso di emergenza dell'organizzazione.
     * **App Cloud** : Includi **tutte le app Cloud**.
    * Controlli di accesso
      * **Concedi** : selezionare il pulsante di opzione **Concedi accesso** .
        * **Richiedere l'autenticazione a più fattori**.
        * **Richiedi che il dispositivo sia contrassegnato come conforme**.
        * Per più controlli: **richiede tutti i controlli selezionati**.
    * Abilitare i criteri- **on**.

È possibile creare criteri che bloccano i paesi in cui gli utenti non accedono alle risorse aziendali. Per ulteriori informazioni sui criteri di accesso condizionale basato sulla posizione IP, vedere [la condizione location in Azure Active Directory accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Configurazione di Intune

### <a name="configure-enrollment-status"></a>Configurare lo stato di registrazione

È importante assicurarsi che la workstation protetta sia un dispositivo pulito attendibile. Quando si acquistano nuovi dispositivi, è possibile insistere sul fatto che il Factory è impostato su [Windows 10 Pro in modalità S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), che limita l'esposizione alle vulnerabilità durante la gestione della supply chain. Dopo aver ricevuto un dispositivo dal fornitore, è possibile utilizzare Autopilot per modificarlo dalla modalità S. Le linee guida seguenti forniscono informazioni dettagliate sull'applicazione del processo di trasformazione.

Per assicurarsi che i dispositivi siano completamente configurati prima dell'uso, Intune fornisce un mezzo per **bloccare l'uso del dispositivo fino a quando non vengono installate tutte le app e i profili**.

Dal **portale di Azure**:
1. Passare a **Microsoft Intune** > registrazione del**dispositivo** > registrazione di**Windows** > **pagina** > stato impostazioni**predefinite** > .
1. Impostare **Mostra stato installazione profilo applicazione** su **Sì**.
1. Impostare l' **uso del dispositivo a blocchi finché tutte le app e i profili non vengono installati** su **Sì**.

### <a name="create-an-autopilot-deployment-profile"></a>Creare un profilo di distribuzione di Autopilot

Dopo aver creato un gruppo di dispositivi, è necessario creare un profilo di distribuzione per configurare i dispositivi Autopilot.

In Intune nel portale di Azure:

1. Selezionare **registrazione del dispositivo registrazione** > **Windows** > profili > di distribuzione**Crea profilo**.
1. Digitare:
   * Nome: **profilo di distribuzione della workstation sicuro**.
   * Descrizione: **distribuzione di workstation protette**.
   * Impostare **Converti tutti i dispositivi di destinazione su AUTOPILOT** su **Sì**. Questa impostazione garantisce che tutti i dispositivi nell'elenco vengano registrati con il servizio di distribuzione Autopilot. Consenti l'elaborazione della registrazione per 48 ore.
1. Selezionare **Avanti**.
   * Per la **modalità di distribuzione**, scegliere **distribuzione automatica (anteprima)** . I dispositivi con questo profilo sono associati all'utente che esegue la registrazione del dispositivo. Per registrare il dispositivo sono necessarie le credenziali utente. È essenziale tenere presente che la distribuzione di un dispositivo nella modalità di **distribuzione automatica** consentirà di distribuire computer portatili in un modello condiviso. Non verrà eseguita alcuna assegnazione utente fino a quando il dispositivo non viene assegnato a un utente per la prima volta. Di conseguenza, tutti i criteri utente, ad esempio BitLocker, non verranno abilitati finché non viene completata l'assegnazione di un utente. Per ulteriori informazioni su come accedere a un dispositivo protetto, vedere [profili selezionati](https://docs.microsoft.com/intune/device-profile-assign).
   * La casella **Aggiungi a Azure ad come** dovrebbe mostrare **Azure ad Unite** ed essere disabilitata.
   * Selezionare il Langugage (Region), il tipo di account utente **standard**. 
1. Selezionare **Avanti**.
   * Selezionare un tag di ambito se ne è stato preconfigurato uno.
1. Selezionare **Avanti**.
1. Scegliere **assegnazioni** > **assegna a** > **gruppi selezionati**. In **Seleziona gruppi da includere**scegliere **protezione utenti workstation**.
1. Selezionare **Avanti**.
1. Selezionare **Crea** per creare il profilo. Il profilo di distribuzione Autopilot è ora disponibile per l'assegnazione ai dispositivi.

La registrazione dei dispositivi in Autopilot offre un'esperienza utente diversa in base al tipo di dispositivo e al ruolo. Nell'esempio di distribuzione viene illustrato un modello in cui i dispositivi protetti vengono distribuiti in blocco e possono essere condivisi, ma quando vengono usati per la prima volta, il dispositivo viene assegnato a un utente. Per ulteriori informazioni, vedere la pagina relativa alla [registrazione dei dispositivi](https://docs.microsoft.com/intune/device-enrollment)con Autopilot di Intune.

### <a name="configure-windows-update"></a>Configurare Windows Update

Mantenere aggiornato Windows 10 è uno degli elementi più importanti. Per mantenere Windows in uno stato sicuro, si distribuisce un anello di aggiornamento per gestire la velocità con cui gli aggiornamenti vengono applicati alle workstation. 

Questa guida consiglia di creare un nuovo anello di aggiornamento e modificare le impostazioni predefinite seguenti:

Nel portale di Azure:

1. Passare a **Microsoft Intune** > **aggiornamenti** > software**anelli di aggiornamento di Windows 10**.
1. Digitare:
   * Nome- **aggiornamenti della workstation gestita di Azure**
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

1. Selezionare **Create**.
1. Nella scheda **assegnazioni** aggiungere il gruppo **workstation sicure** .

Per ulteriori informazioni sui criteri di Windows Update, vedere [policy CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrazione di Windows Defender ATP Intune

Windows Defender ATP e Microsoft Intune interagiscono per prevenire le violazioni della sicurezza. Possono anche limitare l'effetto delle violazioni. Le funzionalità ATP forniscono funzionalità di rilevamento delle minacce in tempo reale e consentono il controllo e la registrazione estensivi dei dispositivi endpoint.

Per configurare l'integrazione di Windows Defender ATP e Intune, passare alla portale di Azure.

1. Passare a **Microsoft Intune** > conformità > del dispositivo**Windows Defender ATP**.
1. Nel passaggio 1, in Configuring **Windows Defender ATP**, selezionare **Connect Windows defender ATP to Microsoft Intune in Windows Defender Security Center**.
1. In Windows Defender Security Center:
   1. Selezionare **Impostazioni** > **funzionalità avanzate**.
   1. Per **Microsoft Intune connessione**, scegliere **on**.
   1. Selezionare **Salva preferenze**.
1. Una volta stabilita la connessione, tornare a Intune e selezionare **Aggiorna** nella parte superiore.
1. Impostare **Connetti dispositivi Windows versione 10.0.15063 e successive a Windows Defender ATP** **su on**.
1. Selezionare **Salva**.

Per altre informazioni, vedere [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Completa protezione avanzata profili workstation

Per completare correttamente la protezione avanzata della soluzione, scaricare ed eseguire lo script appropriato. Trovare i collegamenti di download per il **livello di profilo**desiderato:

| Profilo | Percorso download | Nome file |
| --- | --- | --- |
| Sicurezza bassa | N/D |  N/D |
| Sicurezza avanzata | https://aka.ms/securedworkstationgit | Enhanced-workstation-Windows10-(1809). ps1 |
| Sicurezza elevata  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializzato | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Conformità specializzata * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protetti | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*La conformità specializzata è uno script che impone la configurazione specializzata fornita in NCSC Windows10 SecurityBaseline.

Una volta eseguito correttamente lo script, è possibile effettuare aggiornamenti a profili e criteri in Intune. Gli script per i profili avanzati e protetti creano i criteri e i profili per l'utente, ma è necessario assegnare il criterio al gruppo di **workstation sicure** .

* Qui è possibile trovare i profili di configurazione dei dispositivi di Intune creati dagli script: **Portale di Azure**Microsoft Intune i profili diconfigurazione > dei dispositivi. >  > 
* Qui è possibile trovare i criteri di conformità dei dispositivi di Intune creati dagli script: **Portale di Azure**Microsoft Intune i criteri diconformità > del dispositivo. >  > 

Per esaminare le modifiche apportate dagli script, è possibile esportare i profili. In questo modo è possibile determinare ulteriori finalizzazione che potrebbero essere richieste come indicato nella documentazione di [Seccon](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Eseguire lo script `DeviceConfiguration_Export.ps1` di esportazione dei dati di Intune dal [repository DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) per esportare tutti i profili Intune correnti.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurazioni aggiuntive e protezione avanzata da considerare

Seguendo le istruzioni riportate in questo articolo, è stata distribuita una workstation protetta. È tuttavia necessario considerare anche controlli aggiuntivi. Ad esempio:

* limitare l'accesso ai browser alternativi
* Consenti HTTP in uscita
* Blocca selezione siti Web
* impostare le autorizzazioni per l'esecuzione di script di PowerShell personalizzati

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Impostare le regole nel provider del servizio di configurazione firewall (CSP)

È possibile apportare ulteriori modifiche alla gestione delle regole in ingresso e in uscita in base alle esigenze per gli endpoint consentiti e bloccati. Quando si continua a rafforzare la workstation protetta, è possibile allentare la restrizione che nega tutto il traffico in ingresso e in uscita. È possibile aggiungere siti in uscita consentiti per includere siti Web comuni e attendibili. Per altre informazioni, vedere [servizio di configurazione del firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Le raccomandazioni con restrizioni predefinite sono:

* Nega tutto in ingresso
* Nega tutto in uscita

Il progetto Spamhaus gestisce [l'elenco di blocchi di dominio (doppia)](https://www.spamhaus.org/dbl/): una risorsa efficace da usare come punto di partenza per bloccare i siti.

### <a name="manage-local-applications"></a>Gestire le applicazioni locali

La workstation protetta passa a uno stato effettivamente finalizzato quando vengono rimosse le applicazioni locali, incluse le applicazioni di produttività. Qui si aggiunge Chrome come browser predefinito e si usa Intune per limitare la capacità di un utente di modificare il browser e i relativi plug-in.

#### <a name="deploy-applications-using-intune"></a>Distribuire applicazioni con Intune

In alcune situazioni, le applicazioni come il browser Google Chrome sono necessarie nella workstation protetta. Nell'esempio seguente vengono fornite le istruzioni per installare Chrome nei dispositivi del gruppo di sicurezza **workstation protette**.

1. Scaricare il bundle Chrome del programma di installazione offline [per Windows 64 bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Estrarre i file e prendere nota del percorso del `GoogleChromeStandaloneEnterprise64.msi` file.
1. Nella **portale di Azure** passarea **Microsoft Intune** > app**client** > app > **Aggiungi**.
1. In **tipo di app**scegliere **line-of-business**.
1. In **file pacchetto app**selezionare il `GoogleChromeStandaloneEnterprise64.msi` file dal percorso estratto e selezionare **OK**.
1. In **informazioni sull'app**specificare una descrizione e un server di pubblicazione. Selezionare **OK**.
1. Selezionare **Aggiungi**.
1. Nella scheda **assegnazioni** selezionare **disponibile per i dispositivi registrati** in **tipo di assegnazione**.
1. In **gruppi inclusi**aggiungere il gruppo **workstation sicure** .
1. Selezionare **OK** e quindi **Salva**.

Per altre informazioni sulla configurazione delle impostazioni di Chrome, vedere [gestire il browser Chrome con Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurazione del portale aziendale per le app personalizzate

In modalità protetta, l'installazione dell'applicazione è limitata al portale aziendale di Intune. Tuttavia, l'installazione del portale richiede l'accesso a Microsoft Store. Nella soluzione protetta è possibile rendere disponibile il portale aziendale per tutti i dispositivi tramite una modalità offline.

Una copia gestita da Intune del [portale aziendale](https://docs.microsoft.com/Intune/store-apps-company-portal-app) offre accesso su richiesta a strumenti aggiuntivi che è possibile eseguire il push agli utenti delle workstation protette.

Potrebbe essere necessario installare app di Windows a 32 bit o altre app la cui distribuzione richiede speciali preparazioni. In questi casi, lo [strumento Microsoft Win32 content Prep](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) può fornire un file di formato pronto per `.intunewin` l'uso per l'installazione.

### <a name="use-powershell-to-create-custom-settings"></a>Usare PowerShell per creare impostazioni personalizzate

È anche possibile usare PowerShell per estendere le funzionalità di gestione degli host. Questo script di esempio imposta uno sfondo predefinito nell'host. Si tratta di una funzionalità che è disponibile anche tramite il portale di Azure e i profili. Lo script di esempio serve solo per illustrare la funzionalità di PowerShell.

Potrebbe essere necessario configurare alcuni controlli e impostazioni personalizzati nelle workstation protette. Questo esempio Mostra come modificare lo sfondo della workstation usando la funzionalità di PowerShell per identificare facilmente il dispositivo come una workstation sicura e pronta all'uso.

Lo script [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) di Microsoft Scripting Center consente a Windows di caricare l' [immagine di sfondo generica gratuita](https://i.imgur.com/OAJ28zO.png) all'avvio.

1. Scaricare lo script in un dispositivo locale.
1. Aggiornare customerXXXX e il percorso di download dell'immagine di sfondo. In questo esempio si sostituisce customerXXXX in backgrounds.  
1. Passare alla **portale di Azure** > **Microsoft Intune**configurazione del dispositivoscript > di PowerShell**Aggiungi**. >  > 
1. Fornire un **nome** per lo script e specificare il **percorso dello script**.
1. Selezionare **Configura**.
   1. Impostare **Esegui questo script utilizzando le credenziali di accesso su** **Sì**.
   1. Selezionare **OK**.
1. Selezionare **Create**.
1. Selezionare **assegnazioni** > **selezionare gruppi**.
   1. Aggiungere le **workstation protette**del gruppo di sicurezza.
   1. Selezionare **Salva**.

## <a name="enroll-and-validate-your-first-device"></a>Registrare e convalidare il primo dispositivo

1. Per registrare il dispositivo, sono necessarie le informazioni seguenti:
   * **Numero di serie** : disponibile nello chassis del dispositivo.
   * **ID prodotto Windows** : disponibile in **System** > **About** dal menu impostazioni di Windows.
   * È possibile eseguire [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) per ottenere un file hash CSV con tutte le informazioni necessarie per la registrazione del dispositivo.
   
     Eseguire `Get-WindowsAutoPilotInfo – outputfile device1.csv` per restituire le informazioni come file CSV che è possibile importare in Intune.

     > [!NOTE]
     > Lo script richiede diritti elevati. Viene eseguito come signed remoto. Il `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando consente di eseguire correttamente lo script.

   * È possibile raccogliere queste informazioni eseguendo l'accesso a un dispositivo Windows 10 versione 1809 o superiore. Anche il rivenditore hardware può fornire queste informazioni.
1. Nella **portale di Azure**passare a **Microsoft Intune** >  >  > Registrazione dispositivi dispositivi di**registrazione Windows** **-Gestisci dispositivi Windows Autopilot**.
1. Selezionare **Importa** e scegliere il file CSV.
1. Aggiungere il dispositivo al gruppo di sicurezza **workstation sicure** .
1. Sul dispositivo Windows 10 che si desidera configurare, passare a **Impostazioni** > di Windows**Aggiorna &**  > **ripristino**di sicurezza.
   1. Scegliere **Introduzione** in **Reimposta il PC**.
   1. Seguire le istruzioni per reimpostare e riconfigurare il dispositivo con il profilo e i criteri di conformità configurati.

Dopo aver configurato il dispositivo, completare una verifica e verificare la configurazione. Verificare che il primo dispositivo sia configurato correttamente prima di continuare la distribuzione.

## <a name="assign-and-monitor"></a>Assegnazione e monitoraggio

Per assegnare dispositivi e utenti, è necessario eseguire il mapping dei [profili selezionati](https://docs.microsoft.com/intune/device-profile-assign) al gruppo di sicurezza. Tutti i nuovi utenti che richiedono le autorizzazioni per il servizio devono essere aggiunti anche al gruppo di sicurezza.

È possibile monitorare i profili con [il monitoraggio](https://docs.microsoft.com/intune/device-profile-monitor)dei profili di Intune.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Informazioni [Azure ad](https://docs.microsoft.com/azure/active-directory/index).
