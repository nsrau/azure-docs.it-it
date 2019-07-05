---
title: Distribuire workstation gestite di Azure - Azure Active Directory
description: Informazioni su come distribuire le workstation sicure, gestita da Azure per ridurre il rischio di violazione a causa di una configurazione errata o compromissione.
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
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550476"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuire una workstation sicura, gestita da Azure

Dopo aver [comprendere workstation sicure](concept-azure-managed-workstation.md), è possibile iniziare il processo di distribuzione. Con questa Guida, userai i profili definiti per creare una workstation in cui è più sicura dall'inizio.

![Distribuzione di una workstation sicura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Prima di poter distribuire la soluzione, è necessario selezionare un profilo. È possibile utilizzare contemporaneamente più profili in una distribuzione e assegnare i tag o i gruppi.
> [!NOTE]
> Applicare uno dei profili in base alle esigenze per i requisiti. È possibile spostare in un altro profilo tramite l'assegnazione in Intune.

| Profilo | Basso | Avanzato | Alto | Specializzata | Protetto | Isolato |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utente di Azure AD | Yes | Sì | Sì | Sì | Sì | Yes |
| Gestito da Intune | Yes | Sì | Sì | Sì | Sì | Yes |
| Dispositivo - registrati in Azure AD | Yes |  |  |  |  | |   |
| Dispositivo - aggiunti ad Azure AD |   | Yes | Sì | Sì | Sì | Yes |
| Baseline della sicurezza Intune applicato |   | Yes <br> (Avanzato) | Yes <br> (HighSecurity) | Yes <br> (NCSC) | Yes <br> (Protetto) |  NA |
| L'hardware soddisfi gli standard di sicuro Windows 10 |   | Yes | Sì | Sì | Sì | Yes |
| Microsoft Defender ATP abilitata |   | Yes  | Sì | Sì | Sì | Yes |
| Rimozione dei diritti di amministratore |   |   | Yes  | Sì | Sì | Yes |
| Distribuzione mediante Microsoft Autopilot |   |   | Yes  | Sì | Sì | Yes |
| App installate solo da Intune |   |   |   | Yes | Sì |Yes |
| URL con restrizioni all'elenco approvato |   |   |   | Yes | Sì |Yes |
| Internet bloccato (in ingresso/uscita) |   |   |   |  |  |Yes |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I concetti trattati in questa guida si presuppongono un equivalente SKU o Microsoft 365 Enterprise E5. Alcuni suggerimenti in questa guida può essere implementato con SKU inferiori. Per altre informazioni, vedere [licenza di Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Per automatizzare il provisioning di licenza, prendere in considerazione [licenze basate su gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) per gli utenti.

## <a name="azure-active-directory-configuration"></a>Configurazione di Azure Active Directory

Azure Active Directory (Azure AD) consente di gestire utenti, gruppi e dispositivi per le workstation di amministratore. È necessario abilitare Servizi di identità e le funzionalità con un' [account di amministratore](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando si crea l'account di amministratore di workstation protetti, si espone l'account nella workstation corrente. Assicurarsi di che usare un dispositivo noto sicuro per eseguire questa configurazione iniziale e tutte le configurazioni globali. Per ridurre il rischio di attacco per un'esperienza primo, prendere in considerazione seguente il [materiale sussidiario per impedire le infezioni da malware](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

È anche consigliabile richiedere l'autenticazione a più fattori, almeno per gli amministratori. Visualizzare [distribuire MFA basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) per indicazioni sull'implementazione.

### <a name="azure-ad-users-and-groups"></a>Utenti e gruppi azure AD

1. Dal portale di Azure, passare a **Azure Active Directory** > **utenti** > **nuovo utente**.
1. Creare l'amministratore del dispositivo seguendo i passaggi descritti nel [esercitazione sull'utente di creare](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Digitare:
   * **Nome** -amministratore Workstation protette
   * **Nome utente** - `secure-ws-admin@identityitpro.com`
   * **Ruolo della directory** - **amministratore con limitazioni** e selezionare il **amministratore di Intune** ruolo.
1. Selezionare **Create**.

Creare quindi due gruppi: gli utenti delle workstation e dispositivi workstation.

Dal portale di Azure, passare a **Azure Active Directory** > **gruppi** > **nuovo gruppo**.

1. Per il gruppo di utenti di workstation, si potrebbe voler configurare [licenze basate su gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) per automatizzare il provisioning delle licenze agli utenti.
1. Per il gruppo di utenti di workstation, immettere:
   * **Tipo di gruppo** -sicurezza
   * **Nome del gruppo** -proteggere gli utenti di Workstation
   * **Tipo di appartenenza** : assegnato
1. Aggiungere l'utente amministratore di workstation sicura: `secure-ws-admin@identityitpro.com`
1. È possibile aggiungere qualsiasi altro utente che gestiranno workstation sicure.
1. Selezionare **Create**.

1. Per il gruppo di dispositivi workstation, immettere:
   * **Tipo di gruppo** -sicurezza
   * **Nome del gruppo** -Secure workstation
   * **Tipo di appartenenza** : assegnato
1. Selezionare **Create**.

### <a name="azure-ad-device-configuration"></a>Configurazione del dispositivo AD Azure

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Specificare chi può aggiungere dispositivi ad Azure AD

Configurare i dispositivi di configurazione in Active Directory per consentire il gruppo di protezione amministrativa aggiungere i dispositivi al dominio. Per configurare questa impostazione dal portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo**.
1. Scegli **Selected** sotto **gli utenti possono aggiungere dispositivi ad Azure AD**e quindi selezionare il gruppo "Utenti di proteggere Workstation".

#### <a name="removal-of-local-admin-rights"></a>Rimozione dei diritti di amministratore locale

Questo metodo richiede che gli utenti del VIP, DevOps e le workstation a livello di protezione non hanno diritti di amministratore nelle proprie macchine. Per configurare questa impostazione dal portale di Azure:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo**.
1. Selezionare **None** sotto **amministratori locali aggiuntivi su Azure AD i dispositivi aggiunti**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Richiedere l'autenticazione a più fattori per aggiungere i dispositivi

Rafforzare ulteriormente il processo di aggiunta dei dispositivi ad Azure AD:

1. Passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo**.
1. Selezionare **Yes** sotto **Richiedi multi-Factor Auth per aggiungere dispositivi**.
1. Selezionare **Salva**.

#### <a name="configure-mdm"></a>Configurare MDM

Nel portale di Azure:

1. Passare a **Azure Active Directory** > **servizi Mobility (MDM e MAM)**  > **Microsoft Intune**.
1. Modifica il **ambito utente MDM** se si imposta su **tutte**.
1. Selezionare **Salva**.

Questi passaggi consentono di gestire tutti i dispositivi con Intune. Per altre informazioni, vedere [Intune Quickstart: Configurare la registrazione automatica per i dispositivi Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). In un passaggio successivo è creare i criteri di configurazione e la conformità di Intune.

#### <a name="azure-ad-conditional-access"></a>Accesso condizionale di Azure AD

Accesso condizionale AD Azure consente di limitare le attività amministrative con privilegi ai dispositivi conformi. Predefined i membri del **proteggere gli utenti delle Workstation** gruppo sono necessari per eseguire l'autenticazione a più fattori durante l'accesso alle applicazioni cloud. Una procedura consigliata è di escludere gli account di accesso di emergenza dai criteri. Per altre informazioni, vedere [gestire gli account di accesso di emergenza in Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Configurare l'accesso condizionale dal portale di Azure:

1. Passare a **Azure Active Directory** > **accesso condizionale** > **nuovo criterio**.
1. Digitare:
   * **Nome** -protetta dei dispositivi necessarie dei criteri
   * Assegnazioni
     * **Utenti e gruppi**
       * Includere - **utenti e gruppi** : selezionare la **proteggere gli utenti di Workstation** gruppo creato in precedenza.
       * Escludere - **utenti e gruppi** -selezionare l'account di accesso di emergenza dell'organizzazione.
     * **App cloud** -includono **tutte le app cloud**.
    * Controlli di accesso
      * **Concessione** : selezionare questa opzione **concedere l'accesso** pulsante di opzione.
        * **Richiedere multi-factor authentication**.
        * **Richiedi che i dispositivi siano contrassegnati come conformi**.
        * Per più controlli - **richiedono tutti i controlli selezionati**.
    * Abilitare i criteri - **su**.

È possibile creare criteri che bloccano i paesi in cui utenti non accedono alle risorse aziendali. Per altre informazioni sui criteri di accesso condizionale basati sulla posizione IP, vedere [la condizione della posizione in Active Directory accesso condizionale di Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Configurazione di Intune

### <a name="configure-enrollment-status"></a>Configurare lo stato di registrazione

È importante garantire che le workstation sicura è un dispositivo pulito attendibile. Quando si acquistano nuovi dispositivi, può affermare che siano factory impostato su [Windows 10 Pro in modalità S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), che limita l'esposizione a vulnerabilità durante la gestione della supply chain. Dopo aver ricevuto un dispositivo al proprio fornitore, è possibile usare Autopilot per modificarlo da modalità S. Il materiale sussidiario seguente fornisce informazioni dettagliate sull'applicazione del processo di trasformazione.

Per garantire che i dispositivi siano completamente configurati prima dell'uso, Intune fornisce un mezzo per **blocca l'uso di dispositivi fino a quando tutte le App e i profili vengono installati**.

Dal **portale di Azure**:
1. Passare a **Microsoft Intune** > **registrazione di dispositivi** > **registrazione Windows** > **lo stato di registrazione Pagina** > **predefinito** > **impostazioni**.
1. Impostare **Mostra lo stato dell'installazione del profilo app** al **Yes**.
1. Impostare **blocca l'uso di dispositivi fino a quando tutti i profili e le app installati** al **Sì**.

### <a name="create-an-autopilot-deployment-profile"></a>Creare un profilo di distribuzione Autopilot

Dopo aver creato un gruppo di dispositivi, è necessario creare un profilo di distribuzione per configurare i dispositivi Autopilot.

In Intune nel portale di Azure:

1. Selezionare **registrazione del dispositivo** > **registrazione Windows** > **profili di distribuzione** > **Crea profilo** .
1. Digitare:
   * Nome: **proteggere il profilo di distribuzione di workstation**.
   * Descrizione - **distribuzione di workstation sicure**.
   * Impostare **convertire destinazione tutti i dispositivi Autopilot** al **Yes**. Questa impostazione garantisce che nell'elenco di tutti i dispositivi registrati con il servizio di distribuzione Autopilot. Consentire a 48 ore per la registrazione per l'elaborazione.
1. Selezionare **Avanti**.
   * Per la **modalità di distribuzione**, scegliere **self-distribuzione (anteprima)** . I dispositivi con questo profilo sono associati all'utente che effettua la registrazione del dispositivo. Le credenziali dell'utente sono necessarie per registrare il dispositivo.
   * Il **Join per Azure AD lo stesso** casella viene visualizzato **aggiunti ad Azure AD** e visualizzata in grigio.
   * Selezionare l'IntelliPoint (regione), tipo di account utente **standard**. 
1. Selezionare **Avanti**.
   * Selezionare un tag di ambito se è stata preconfigurata uno.
1. Selezionare **Avanti**.
1. Scegli **assegnazioni** > **assegnare ai** > **gruppi selezionati**. Nelle **selezionare i gruppi da includere**, scegliere **proteggere gli utenti di Workstation**.
1. Selezionare **Avanti**.
1. Selezionare **Crea** per creare il profilo. Il profilo di distribuzione Autopilot è ora disponibile da assegnare ai dispositivi.

### <a name="configure-windows-update"></a>Configurare Windows Update

Windows 10 stare al passo è uno degli aspetti più importanti che è possibile eseguire. Per gestire Windows in uno stato protetto, si distribuisce un anello di aggiornamento per gestire il ritmo che vengono applicati gli aggiornamenti software nelle workstation. 

È consigliabile creare un nuovo anello di aggiornamento e modificare le impostazioni predefinite seguenti:

Nel portale di Azure:

1. Passare a **Microsoft Intune** > **gli aggiornamenti Software** > **anelli di aggiornamento di Windows 10**.
1. Digitare:
   * Nome - **aggiornamenti workstation gestite di Azure**
   * -Canale di manutenzione **Windows Insider - veloce**
   * Differimento di aggiornamento di qualità (giorni) - **3**
   * Periodo di differimento dell'aggiornamento funzionalità (giorni) - **3**
   * Comportamento di aggiornamento automatico - **automatico installa e riavvia senza controllo dell'utente finale**
   * Impedisce all'utente la sospensione degli aggiornamenti di Windows - **blocco**
   * Richiedi l'approvazione dell'utente di riavviare fuori orario di lavoro - **obbligatorio**
   * Consentire all'utente di riavviare (riavviare coinvolti) - **obbligatorio**
   * Eseguire la transizione agli utenti di coinvolgimento riavvio dopo un riavvio automatico (giorni) - **3**
   * Promemoria di riavvio coinvolti posticipo (giorni) - **3**
   * Impostare scadenze per in sospeso (giorni) - riavvia **3**

1. Selezionare **Create**.
1. Nel **assegnazioni** scheda, aggiungere il **Secure workstation** gruppo.

Per altre informazioni sui criteri di Windows Update, vedere [Policy CSP - Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrazione di Intune di Windows Defender ATP

Windows Defender ATP e Microsoft Intune interagiscono per aiutare a prevenire le violazioni della sicurezza. È anche possibile limitare l'impatto delle violazioni. Le funzionalità di ATP forniscono il rilevamento delle minacce in tempo reale, nonché abilitare il controllo completo e la registrazione dei dispositivi dell'endpoint.

Per configurare l'integrazione di Windows Defender ATP e Intune, passare al portale di Azure.

1. Passare a **Microsoft Intune** > **conformità del dispositivo** > **Windows Defender ATP**.
1. Nel passaggio 1 in **configurazione di Windows Defender ATP**, selezionare **Connect Windows Defender ATP a Microsoft Intune in Windows Defender Security Center**.
1. In Windows Defender Security Center:
   1. Selezionare **le impostazioni** > **funzionalità avanzate**.
   1. Per la **connessione di Microsoft Intune**, scegliere **su**.
   1. Selezionare **salvare le preferenze**.
1. Dopo aver stabilita una connessione, tornare a Intune e selezionare **Aggiorna** nella parte superiore.
1. Impostare **Connect Windows 10.0.15063 versione di dispositivi e versioni successive di Windows Defender ATP** al **su**.
1. Selezionare **Salva**.

Per altre informazioni, vedere [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Completa il profilo di workstation protezione avanzato

Per completare il rafforzamento della soluzione, scaricare ed eseguire lo script appropriato. Trovare i collegamenti di download per la posizione desiderata **a livello di profilo**:

| Profilo | Percorso di download | Nome del file |
| --- | --- | --- |
| Livello di protezione basso | N/D |  N/D |
| Sicurezza avanzata | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Sicurezza elevata  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializzata | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - SecurityBaseline.ps1 Windows10 (1803) |
| Conformità specializzato * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protetto | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Conformità specializzata è uno script che applica la configurazione specializzata fornita nel SecurityBaseline Windows10 NCSC.

Dopo lo script viene eseguito correttamente, è possibile apportare aggiornamenti ai profili e i criteri in Intune. Gli script per i profili avanzato e sicura per creano criteri e profili per l'utente, ma è necessario assegnare i criteri per i **workstation Secure** gruppo.

* Ecco dove è possibile trovare i profili di configurazione di dispositivo di Intune creati dagli script: **Portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **profili**.
* Ecco dove è possibile trovare il dispositivo di Intune creati dagli script di criteri di conformità: **Portale di Azure** > **Microsoft Intune** > **conformità del dispositivo** > **criteri**.

Per esaminare le modifiche apportate dagli script, è possibile esportare i profili. In questo modo è possibile determinare la protezione avanzata aggiuntiva che potrebbe essere necessaria come descritto nella documentazione di SECCON.

Eseguire lo script di esportazione dei dati di Intune `DeviceConfiguration_Export.ps1` dal [DeviceConfiguration GiuHub repository](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) per esportare tutti i profili di Intune correnti.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurazioni aggiuntive e protezione avanzata da considerare

Seguendo le indicazioni fornite in questo caso, è stata distribuita una workstation sicura. Tuttavia, è anche consigliabile controlli aggiuntivi. Ad esempio:

* limitare l'accesso al browser alternativo
* Consenti HTTP in uscita
* Selezionare Blocca i siti Web
* impostare le autorizzazioni per l'esecuzione di script di PowerShell personalizzati

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Impostare le regole nel provider del servizio firewall configurazione (CSP)

È possibile apportare modifiche aggiuntive per la gestione delle regole in ingresso e in uscita in base alle necessità per gli endpoint consentiti e bloccati. Quando si continua a rafforzare la protezione della workstation sicura, vengono concesse indistintamente la restrizione che nega tutto il traffico in ingresso e in uscita. È possibile aggiungere siti in uscita consentiti per includere i siti Web attendibili e comuni. Per altre informazioni, vedere [servizio di configurazione del Firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Raccomandazioni con restrizioni predefinite sono:

* Nega tutto in ingresso
* Nega tutto in uscita

Il progetto Spamhaus mantiene [l'elenco di blocchi di dominio (doppio)](https://www.spamhaus.org/dbl/): un'ottima risorsa da usare come punto di partenza per il blocco di siti.

### <a name="manage-local-applications"></a>Gestire le applicazioni locali

La workstation sicura sposta in uno stato realmente finalizzato quando vengono rimosse le applicazioni locali, tra cui le applicazioni di produttività. In questo caso, si aggiunge Chrome come browser predefinito e usare Intune per limitare possibilità la di modificare il browser e i plug-in.

#### <a name="deploy-applications-using-intune"></a>Distribuire le applicazioni con Intune

In alcune situazioni, applicazioni, ad esempio il browser Google Chrome sono necessari nella workstation protette. Nell'esempio seguente vengono fornite istruzioni per installare Chrome per i dispositivi nel gruppo di sicurezza **workstation Secure**.

1. Scaricare il programma di installazione offline [bundle di Chrome per Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Estrarre i file e prendere nota della posizione del `GoogleChromeStandaloneEnterprise64.msi` file.
1. Nel **portale di Azure** individuare **Microsoft Intune** > **App Client** > **app**  >  **Aggiungere**.
1. Sotto **tipo di App**, scegliere **Line-of-business**.
1. Sotto **file pacchetto dell'App**, selezionare la `GoogleChromeStandaloneEnterprise64.msi` file dal percorso di estrazione e selezionare **OK**.
1. Sotto **le informazioni sull'App**, fornire una descrizione e un server di pubblicazione. Selezionare **OK**.
1. Selezionare **Aggiungi**.
1. Nel **assegnazioni** scheda, seleziona **disponibile per i dispositivi registrati** sotto **tipo di assegnazione**.
1. Sotto **gruppi inclusi**, aggiungere il **Secure workstation** gruppo.
1. Selezionare **OK** e quindi **Salva**.

Per altre indicazioni sulla configurazione delle impostazioni di Chrome, vedere [gestire Browser Chrome con Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurazione del portale aziendale per le app personalizzate

In una modalità protetta, installazione dell'applicazione è limitata a portale aziendale di Intune. Tuttavia, l'installazione del portale richiede l'accesso a Microsoft Store. Nella soluzione protetta, è possibile rendere l'App portale aziendale disponibili per tutti i dispositivi tramite una modalità offline.

Una copia gestita da Intune del [portale aziendale](https://docs.microsoft.com/Intune/store-apps-company-portal-app) ti offre accesso on demand a strumenti aggiuntivi che è possibile eseguire il push down agli utenti delle workstation protetti.

Si potrebbe essere necessario installare le app di Windows 32 bit o ad altre App la cui distribuzione richiedono speciali operazioni di preparazione. In questi casi, il [strumento di preparazione del contenuto di Microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) può fornire un pronti da usare `.intunewin` file di formato per l'installazione.

### <a name="use-powershell-to-create-custom-settings"></a>Usare PowerShell per creare impostazioni personalizzate

È anche possibile usare PowerShell per estendere le funzionalità di gestione di host. Questo script di esempio imposta il colore di sfondo sull'host. È una funzionalità che è disponibile anche tramite il portale di Azure e i profili. Lo script di esempio serve solo per illustrare la funzionalità di PowerShell.

Si potrebbe essere necessario configurare alcune impostazioni e i controlli personalizzati nelle workstation di sicuro. Questo esempio viene modificato lo sfondo della workstation usando che consente Powershell di identificare facilmente i dispositivi come workstation pronti da usare e sicura.

Il [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) script di Microsoft Scripting Center consente a Windows caricare questo [immagine di sfondo gratuita e generico](https://i.imgur.com/OAJ28zO.png) d'avvio.

1. Scaricare lo script in un dispositivo locale.
1. Aggiornare il customerXXXX e il percorso di download dell'immagine di sfondo. In questo esempio, abbiamo sostituire customerXXXX per gli sfondi.  
1. Selezionare il **portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **PowerShell gli script** > **Add**.
1. Fornire una **Name** per lo script e specificare le **percorso dello Script**.
1. Selezionare **Configura**.
   1. Impostare **eseguire questo script usando usato per l'accesso alle credenziali** al **Yes**.
   1. Selezionare **OK**.
1. Selezionare **Create**.
1. Selezionare **assegnazioni** > **Seleziona gruppi**.
   1. Aggiungere il gruppo di sicurezza **workstation Secure**.
   1. Selezionare **Salva**.

## <a name="enroll-and-validate-your-first-device"></a>Registrare e convalidare il primo dispositivo

1. Per registrare il dispositivo, sono necessarie le informazioni seguenti:
   * **Numero di serie** : trovato sullo chassis del dispositivo.
   * **ID del prodotto Windows** : trovato sotto **System** > **sulle** nel menu delle impostazioni di Windows.
   * È possibile eseguire [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) per ottenere un file di hash CSV con tutte le informazioni necessarie per la registrazione del dispositivo.
   
     Eseguire `Get-WindowsAutoPilotInfo – outputfile device1.csv` per restituire le informazioni come file CSV che è possibile importare Intune.

     > [!NOTE]
     > Lo script richiede diritti elevati. È in esecuzione come remoto firmato. Il `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando consente il corretta esecuzione dello script.

   * È possibile raccogliere queste informazioni tramite l'accesso a una versione di Windows 10 1809 o dispositivo superiore. Il rivenditore di hardware può anche fornire queste informazioni.
1. Nel **portale di Azure**, passare a **Microsoft Intune** > **registrazione dispositivo** > **registrazione Windows**  >  **i dispositivi - gestire Windows Autopilot**.
1. Selezionare **importazione** e scegliere il file CSV.
1. Aggiungere il dispositivo per il **workstation Secure** gruppo di sicurezza.
1. Nel dispositivo Windows 10 che si desidera configurare, passare a **delle impostazioni di Windows** > **aggiornamento e sicurezza** > **ripristino**.
   1. Scegli **iniziare** sotto **reimpostare questo PC**.
   1. Seguire le istruzioni per reimpostare e riconfigurare il dispositivo con i criteri di conformità e del profilo configurati.

Dopo aver configurato il dispositivo, completare una verifica e controllare la configurazione. Verificare che il primo dispositivo sia configurato correttamente prima di continuare la distribuzione.

## <a name="assign-and-monitor"></a>Assegnazione e monitoraggio

Per assegnare utenti e dispositivi, è necessario eseguire il mapping di [i profili selezionati](https://docs.microsoft.com/intune/device-profile-assign) al gruppo di sicurezza. Tutti i nuovi utenti che necessitano di autorizzazioni per il servizio devono essere aggiunto al gruppo di sicurezza nonché.

È possibile monitorare i profili con [il monitoraggio del profilo di Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Comprendere [AD Azure](https://docs.microsoft.com/azure/active-directory/index).
