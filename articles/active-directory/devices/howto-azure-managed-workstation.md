---
title: Distribuire workstation gestite da Azure - Azure Active DirectoryDeploy Azure-managed workstations - Azure Active Directory
description: Informazioni su come distribuire workstation protette gestite da Azure per ridurre il rischio di violazione a causa di errori di configurazione o compromissione.
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
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672613"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuire una workstation sicura gestita da AzureDeploy a secure, Azure-managed workstation

Dopo aver [compreso le workstation protette,](concept-azure-managed-workstation.md)è il momento di iniziare il processo di distribuzione. Questa guida consente di utilizzare profili definiti per creare una workstation più sicura fin dall'inizio.

![Distribuzione di una workstation sicura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selezionare un profilo prima di distribuire la soluzione. È possibile utilizzare più profili contemporaneamente in una distribuzione e assegnarli con tag o gruppi.

> [!NOTE]
> Applicare uno qualsiasi dei profili in base alle proprie esigenze. È possibile passare a un altro profilo assegnandolo in Microsoft Intune.You can move to another profile by assigning it in Microsoft Intune.

| Profilo | Basso | Avanzato | Alto | Specializzata | Protetto | Isolato |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utente in Azure ADUser in Azure AD | Sì | Sì | Sì | Sì | Sì | Sì |
| Gestito da Intune | Sì | Sì | Sì | Sì | Sì | Sì |
| Dispositivo - Azure AD registratoDevice - Azure AD registered | Sì |  |  |  |  | |   |
| Dispositivo - Aggiunta ad Azure ADDevice - Azure AD joined |   | Sì | Sì | Sì | Sì | Sì |
| Linea di base per la sicurezza di Intune applicata |   | Sì <br> (Migliorato) | Sì <br> (HighSecurity) | Sì <br> (NCSC) | Sì <br> (Protetto) | ND |
| L'hardware soddisfa gli standard sicuri di Windows 10 |   | Sì | Sì | Sì | Sì | Sì |
| ATP di Microsoft Defender abilitato |   | Sì  | Sì | Sì | Sì | Sì |
| Rimozione dei diritti di amministratore |   |   | Sì  | Sì | Sì | Sì |
| Distribuzione con Microsoft Autopilot |   |   | Sì  | Sì | Sì | Sì |
| App installate solo da IntuneApps installed only by Intune |   |   |   | Sì | Sì |Sì |
| URL limitati all'elenco approvato |   |   |   | Sì | Sì |Sì |
| Connessione bloccata (in ingresso/uscita) |   |   |   |  |  |Sì |

> [!NOTE]
> Nei **dispositivi** di guida per workstation protette verranno assegnati profili e criteri. Gli utenti non avranno i criteri applicati direttamente, consentendo la condivisione dei dispositivi (dispositivi condivisi) per essere attivo. Se una workstation protetta non è condivisa nella distribuzione o sono necessari singoli criteri utente, l'assegnazione dei profili dei criteri utente può essere assegnata all'utente e al dispositivo. 

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I concetti trattati in questa guida presuppongono che si disponga di Microsoft 365 Enterprise E5 o di uno SKU equivalente. Alcune delle raccomandazioni contenute in questa guida possono essere implementate con SKU inferiori. Per ulteriori informazioni, vedere [Licenze microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Per automatizzare il provisioning delle [licenze,](../users-groups-roles/licensing-groups-assign.md) prendi in considerazione le licenze basate su gruppo per gli utenti.

## <a name="azure-active-directory-configuration"></a>Configurazione di Azure Active DirectoryAzure Active Directory configuration

Azure Active Directory (Azure AD) gestisce utenti, gruppi e dispositivi per le workstation dell'amministratore. Abilitare i servizi di identità e le funzionalità con un [account amministratore.](../users-groups-roles/directory-assign-admin-roles.md)

Quando si crea l'account amministratore della workstation protetta, si espone l'account alla workstation corrente. Assicurarsi di utilizzare un dispositivo sicuro noto per eseguire questa configurazione iniziale e tutta la configurazione globale. Per ridurre l'esposizione agli attacchi per la prima esperienza, prendere in considerazione la guida [per prevenire le infezioni da malware](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Richiedere l'autenticazione a più fattori, almeno per gli amministratori. Per istruzioni sull'implementazione, vedere Distribuire l'autenticazione a più [fattori basata su cloud.](../authentication/howto-mfa-getstarted.md)

### <a name="azure-ad-users-and-groups"></a>Utenti e gruppi di Azure ADAzure AD users and groups

1. Dal portale di Azure passare a Nuovo utente di **Azure Active Directory** > **Users** > **.**
1. Creare l'amministratore del dispositivo seguendo la procedura descritta [nell'esercitazione sulla creazione dell'utente](/Intune/quickstart-create-user).
1. Digitare:

   * **Nome** - Secure Workstation Administrator
   * **Nome utente** - `secure-ws-admin@identityitpro.com`
   * **Ruolo directory** - Amministratore limitato e selezionare il ruolo **amministratore di Intune.Directory** role**Limited administrator** and select the Intune Administrator role.

1. Selezionare **Crea**.

Successivamente, si creano due gruppi: utenti workstation e dispositivi workstation.

Dal portale di Azure passare al**Groups** > **gruppo Nuovo**gruppi di **Azure Active Directory.** > 

1. Per il gruppo di utenti workstation, è possibile configurare le [licenze basate su gruppo](../users-groups-roles/licensing-groups-assign.md) per automatizzare il provisioning delle licenze per gli utenti.
1. Per il gruppo di utenti workstation, immettere:

   * **Tipo di gruppo** - Sicurezza
   * **Nome gruppo** - Utenti workstation protetti
   * **Tipo di appartenenza** - Assegnato

1. Aggiungere l'utente amministratore della workstation protetta:`secure-ws-admin@identityitpro.com`
1. È possibile aggiungere qualsiasi altro utente che gestirà workstation protette.
1. Selezionare **Crea**.
1. Per il gruppo di dispositivi workstation, immettere:

   * **Tipo di gruppo** - Sicurezza
   * **Nome gruppo** - Workstation protette
   * **Tipo di appartenenza** - Assegnato

1. Selezionare **Crea**.

### <a name="azure-ad-device-configuration"></a>Configurazione del dispositivo Azure ADAzure AD device configuration

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Specificare chi può aggiungere dispositivi ad Azure ADSpecify who can join devices to Azure AD

Configurare l'impostazione dei dispositivi in Active Directory per consentire al gruppo di sicurezza amministrativo di aggiungere dispositivi al dominio. Per configurare questa impostazione dal portale di Azure:To configure this setting from the Azure portal:

1. Passare alle impostazioni dei**dispositivi dispositivi dei dispositivi** > **Device settings** **di Azure Active Directory** > .
1. Scegliere **Selezionato** in **Gli utenti possono aggiungere dispositivi ad Azure AD**e quindi selezionare il gruppo "Utenti workstation protetti".

#### <a name="removal-of-local-admin-rights"></a>Rimozione dei diritti di amministratore locale

Questo metodo richiede che gli utenti delle workstation VIP, DevOps e secure level non dispongano di diritti di amministratore sui propri computer. Per configurare questa impostazione dal portale di Azure:To configure this setting from the Azure portal:

1. Passare alle impostazioni dei**dispositivi dispositivi dei dispositivi** > **Device settings** **di Azure Active Directory** > .
1. Selezionare **Nessuno** in **Amministratori locali aggiuntivi nei dispositivi aggiunti ad Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Richiedere l'autenticazione a più fattori per l'aggiunta di dispositiviRequire multi-factor authentication to join devices

To further strengthen the process of joining devices to Azure AD:

1. Passare alle impostazioni dei**dispositivi dispositivi dei dispositivi** > **Device settings** **di Azure Active Directory** > .
1. Selezionare **Sì** in Richiedi autenticazione a più **fattori per l'aggiunta**di dispositivi .
1. Selezionare **Salva**.

#### <a name="configure-mobile-device-management"></a>Configurare la gestione dei dispositivi mobili

Nel portale di Azure:

1. Passare ad **Azure Active Directory** > **Mobility (MDM e MAM)** > **di Microsoft Intune.**
1. Modificare l'impostazione **dell'ambito utente MDM su** **Tutti**.
1. Selezionare **Salva**.

Questi passaggi consentono di gestire qualsiasi dispositivo con Intune.These steps allow you to manage any device with Intune. Per altre informazioni, vedere Guida introduttiva di Intune: Configurare la registrazione automatica per i dispositivi Windows 10.For more information, see [Intune Quickstart: Set up automatic enrollment for Windows 10 devices](/Intune/quickstart-setup-auto-enrollment). I criteri di configurazione e conformità di Intune vengono creati in un passaggio futuro.

#### <a name="azure-ad-conditional-access"></a>Azure AD Conditional Access

L'accesso condizionale di Azure AD consente di limitare le attività amministrative con privilegi ai dispositivi conformi. I membri predefiniti del gruppo **Secure Workstation Users** devono eseguire l'autenticazione a più fattori durante l'accesso alle applicazioni cloud. Una procedura consigliata consiste nell'escludere gli account di accesso di emergenza dai criteri. Per altre informazioni, vedere Gestire gli account di [accesso di emergenza in Azure AD.](../users-groups-roles/directory-emergency-access.md)

## <a name="intune-configuration"></a>Configurazione di Intune

### <a name="configure-enrollment-status"></a>Configurare lo stato di registrazione

È importante assicurarsi che la workstation sicura sia un dispositivo pulito attendibile. Quando acquisti nuovi dispositivi, puoi insistere sul fatto che sono impostati in fabbrica su [Windows 10 Pro in modalità S,](/Windows/deployment/Windows-10-pro-in-s-mode)il che limita l'esposizione alle vulnerabilità durante la gestione della supply chain. Dopo aver ricevuto un dispositivo dal fornitore, è possibile utilizzare Autopilot per modificarlo dalla modalità S. Le linee guida seguenti forniscono informazioni dettagliate sull'applicazione del processo di trasformazione.

Per assicurarsi che i dispositivi siano completamente configurati prima dell'uso, Intune offre un mezzo per **bloccare l'uso dei dispositivi fino a quando non vengono installate tutte le app e i profili.**

Dal **portale di Azure:**

1. Passare **Microsoft Intune** > a**Impostazioni**predefinite > pagina**Stato** > **Default**registrazione**di registrazione di Windows** > per la**registrazione** > di dispositivi Microsoft Intune .
1. Impostare **Mostra stato installazione profilo app su** **Sì**.
1. Impostare **Blocca utilizzo dispositivo fino a quando tutte le app e i profili non vengono installati** su **Sì**.

### <a name="create-an-autopilot-deployment-profile"></a>Creare un profilo di distribuzione AutoPilot

Dopo aver creato un gruppo di dispositivi, è necessario creare un profilo di distribuzione per configurare i dispositivi Autopilot.

In Intune nel portale di Azure:In Intune in the Azure portal:

1. Selezionare **Registrazione dispositivo** > **Profili** > di**distribuzione** > di Windows**Creazione profilo**.
1. Digitare:

   * Nome - **Profilo di distribuzione sicuro della workstation**.
   * Descrizione - **Distribuzione di workstation protette**.
   * Impostare **Converti tutti i dispositivi interessati in Autopilot** su **Sì**. Questa impostazione garantisce che tutti i dispositivi nell'elenco vengano registrati per il servizio di distribuzione Autopilot. L'elaborazione della registrazione può richiedere fino a 48 ore.

1. Fare clic su **Avanti**.

   * Per **Modalità di distribuzione**, scegliere Distribuzione automatica **(anteprima)**. I dispositivi con questo profilo sono associati all'utente che lo registra. Le credenziali dell'utente sono necessarie per effettuare la registrazione del dispositivo. È essenziale notare che la distribuzione di un dispositivo in modalità **di distribuzione automatica** consentirà di distribuire i computer portatili in un modello condiviso. Non verrà eseguita alcuna assegnazione utente fino a quando il dispositivo non viene assegnato a un utente per la prima volta. Di conseguenza, tutti i criteri utente, ad esempio BitLocker, non verranno abilitati fino al completamento dell'assegnazione di un utente. Per ulteriori informazioni su come accedere a un dispositivo protetto, vedere [profili selezionati](/intune/device-profile-assign).
   * La casella **Aggiungi ad Azure AD come** dovrebbe mostrare Azure AD **unito** ed essere disattivato.
   * Selezionare la lingua (area geografica), il tipo di account utente **standard**. 

1. Fare clic su **Avanti**.

   * Selezionare un tag ambito, se uno è stato preconfigurato.

1. Fare clic su **Avanti**.
1. Scegliere **Assegnazione assegnazione assegnazione assegnazione** > **a** > gruppi**selezionati**. In **Selezionare i gruppi da includere**, scegliere Secure **Workstations**.
1. Fare clic su **Avanti**.
1. Selezionare **Crea** per creare il profilo. Il profilo di distribuzione di AutoPilot è ora disponibile per l'assegnazione ai dispositivi.

La registrazione del dispositivo in Autopilot offre un'esperienza utente diversa in base al tipo e al ruolo del dispositivo. Nell'esempio di distribuzione viene illustrato un modello in cui i dispositivi protetti vengono distribuiti in blocco e possono essere condivisi, ma quando vengono usati per la prima volta, il dispositivo viene assegnato a un utente. Per altre informazioni, vedere [Registrazione dei dispositivi Intune Autopilot](/intune/device-enrollment).For more information, see Intune Autopilot device enrollment .

### <a name="configure-windows-update"></a>Configurare Windows Update

Mantenere aggiornato Windows 10 è una delle cose più importanti che puoi fare. Per mantenere Windows in uno stato sicuro, si distribuisce un anello di aggiornamento per gestire il ritmo di applicazione degli aggiornamenti alle workstation. 

Questa guida consiglia di creare un nuovo anello di aggiornamento e di modificare le seguenti impostazioni predefinite:

Nel portale di Azure:

1. Passare a Aggiornamenti**software** > di **Microsoft Intune** > Windows**10 Update Rings**.
1. Digitare:

   * Nome - **Aggiornamenti delle workstation gestite da AzureName - Azure-managed workstation updates**
   * Canale di manutenzione - **Windows Insider - Veloce**
   * Differimento aggiornamento qualità (giorni) - **3**
   * Periodo di differimento dell'aggiornamento delle funzionalità (giorni) - **3**
   * Comportamento di aggiornamento automatico - **Installazione e riavvio automatici senza controllo dell'utente finale**
   * Impedire all'utente di soglieriare gli aggiornamenti di Windows - **Blocca**
   * Richiedere l'approvazione dell'utente per il riavvio al di fuori dell'orario di lavoro - **ObbligatorioRequire** user's approval to restart outside of work hours - Required
   * Consenti riavvio utente (riavvio impegnato) - **Obbligatorio**
   * Transizione degli utenti al riavvio impegnato dopo un riavvio automatico (giorni) - **3**
   * Posticipa impegnato promemoria di riavvio (giorni) - **3**
   * Impostare la scadenza per i riavvii in sospeso (giorni) - **3**

1. Selezionare **Crea**.
1. Nella scheda **Assegnazioni** aggiungere il gruppo **Secure Workstations.**

Per ulteriori informazioni sui criteri di Windows Update, vedere [CSP dei criteri - Aggiornamento](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrazione di Windows Defender ATP Intune

Windows Defender ATP e Microsoft Intune collaborano per evitare violazioni della sicurezza. Possono anche limitare l'impatto delle violazioni. Le funzionalità ATP forniscono il rilevamento delle minacce in tempo reale, oltre a consentire un controllo e una registrazione approfonditi dei dispositivi endpoint.

Per configurare l'integrazione di Windows Defender ATP e Intune, passare al portale di Azure.To configure integration of Windows Defender ATP and Intune, go to the Azure portal.

1. Passare alla**conformità** > dei dispositivi **Microsoft Intune** > **Windows Defender ATP**.
1. Nel passaggio 1 in Configurazione di **Windows Defender ATP**selezionare **Connetti Windows Defender ATP a Microsoft Intune in Windows Defender Security Center**.
1. In Windows Defender Security Center:

   1. Selezionare **Impostazioni** > **Funzioni avanzate**.
   1. Per **la connessione a Microsoft Intune**, scegliere **Attivato**.
   1. Selezionare **Save preferences** (Salva preferenze).

1. Dopo aver stabilito una connessione, tornare a Intune e selezionare **Aggiorna** nella parte superiore.
1. Impostare **Connetti i dispositivi Windows con versione 10.0.15063 e successiva a Windows Defender ATP** su **Attivato**.
1. Selezionare **Salva**.

Per altre informazioni, vedere [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Completare la protezione avanzata dei profili della workstation

Per completare correttamente la protezione avanzata della soluzione, scaricare ed eseguire lo script appropriato. Trovare i collegamenti di download per il **livello di profilo**desiderato:

| Profilo | Percorso di download | Nome file |
| --- | --- | --- |
| Bassa sicurezza | N/D | N/D |
| Sicurezza avanzata | https://aka.ms/securedworkstationgit | Migliorato-Workstation-Windows10-(1809).ps1 |
| Alta sicurezza | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializzata | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformità specializzata | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protetto | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Conformità specializzata è uno script che impone la configurazione Specialized fornita in NCSC Windows10 SecurityBaseline.

Dopo aver eseguito correttamente lo script, è possibile apportare aggiornamenti ai profili e ai criteri in Intune.After the script successfully executes, you can make updates to profiles and policies in Intune. Gli script per i profili avanzati e protetti creano criteri e profili per l'utente, ma è necessario assegnare il criterio al gruppo di dispositivi **Secure Workstations.**

* Qui è possibile trovare i profili di configurazione dei dispositivi Intune creati dagli script:**Profili**di**configurazione** > dei dispositivi Microsoft**Intune** >  **del portale** > di Azure.
* Qui è possibile trovare i criteri di conformità dei dispositivi Intune creati dagli script: **Portale** > di Azure**Microsoft Intune** > **Criteri**di**conformità** > dei dispositivi.

Per rivedere le modifiche apportate dagli script, è possibile esportare i profili. In questo modo è possibile determinare la protezione avanzata aggiuntiva che potrebbe essere necessaria come descritto nella [documentazione di SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Eseguire lo script `DeviceConfiguration_Export.ps1` di esportazione dei dati di Intune dal repository [GiuHub di DeviceConfiguration](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) per esportare tutti i profili di Intune correnti.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurazioni aggiuntive e protezione avanzata da considerare

Seguendo le istruzioni qui, è stata distribuita una workstation sicura. Tuttavia, è necessario considerare anche controlli aggiuntivi. Ad esempio:

* limitare l'accesso abrowser alternativi
* consentire HTTP in uscita
* bloccare alcuni siti Web selezionati
* impostare le autorizzazioni per l'esecuzione di script PowerShell personalizzatiSet permissions for running custom PowerShell scripts

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Impostare regole nel provider di servizi di configurazione firewall (CSP)Set rules in the firewall configuration service provider (CSP)

È possibile apportare ulteriori modifiche alla gestione delle regole in ingresso e in uscita in base alle esigenze per gli endpoint consentiti e bloccati. Mentre si continua a proteggere la workstation sicura, è possibile allentare la restrizione che nega tutto il traffico in ingresso e in uscita. È possibile aggiungere siti in uscita autorizzati per includere siti Web comuni e attendibili. Per ulteriori informazioni, vedere [Servizio di configurazione del](/Windows/client-management/mdm/firewall-csp)firewall .

La gestione restrittiva del traffico degli URL include:

* Nega tutto il traffico in ingresso: impostare lo script del profilo della workstation protetta.
* Nega tutti in uscita tranne i servizi di Azure e Microsoft selezionati, tra cui Azure Cloud Shell e la possibilità di consentire la reimpostazione della password di Azure.Deny All outbound except selected Azure and Microsoft services including Azure Cloud Shell and the ability to allows Azure Password Reset.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Se si desidera fornire maggiore granularità alle regole di blocco, è possibile fare riferimento al progetto Spamhaus che gestisce l'elenco di blocco dei domini [(DBL):](https://www.spamhaus.org/dbl/)una buona risorsa da utilizzare come set avanzato di regole da implementare per i siti di blocco.

### <a name="manage-local-applications"></a>Gestire le applicazioni locali

La workstation sicura passa a uno stato veramente indurito quando vengono rimosse le applicazioni locali, incluse le applicazioni di produttività. Qui si aggiunge Chrome come browser predefinito e si usa Intune per limitare la possibilità di un utente di modificare il browser e i relativi plug-in.

#### <a name="deploy-applications-using-intune"></a>Distribuire applicazioni con IntuneDeploy applications using Intune

In alcune situazioni, applicazioni come il browser Google Chrome sono necessarie sulla workstation protetta. L'esempio seguente fornisce istruzioni per l'installazione di Chrome nei dispositivi nel gruppo di sicurezza **Secure Workstations**.

1. Scarica il bundle di Chrome per il programma di installazione offline [per Windows a 64 bit.](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Estrarre i file e prendere nota della posizione del `GoogleChromeStandaloneEnterprise64.msi` file.
1. Nel **portale di Azure** passare alle**app** > client **di Microsoft Intune** > **App** > **Aggiungi**.
1. In Tipo di **app**scegliere **Line-of-business**.
1. In **File pacchetto**app `GoogleChromeStandaloneEnterprise64.msi` selezionare il file dal percorso estratto e scegliere **OK**.
1. In **Informazioni sull'app**specificare una descrizione e un autore. Selezionare **OK**.
1. Selezionare **Aggiungi**.
1. Nella scheda **Assegnazioni** selezionare **Disponibile per i dispositivi registrati** in Tipo di **assegnazione**.
1. In **Gruppi inclusi**aggiungere il gruppo Secure **Workstations.**
1. Selezionare **OK** e quindi **Salva**.

Per altre informazioni sulla configurazione delle impostazioni di Chrome, vedere [Gestire il browser Chrome con Microsoft Intune.](https://support.google.com/chrome/a/answer/9102677)

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurazione del portale aziendale per le app personalizzate

In modalità protetta, l'installazione dell'applicazione è limitata al portale aziendale di Intune.In a secured mode, application installation is restricted to the Intune company portal. Tuttavia, l'installazione del portale richiede l'accesso a Microsoft Store. Nella soluzione protetta è possibile rendere il portale aziendale disponibile per tutti i dispositivi tramite una modalità offline.

Una copia gestita da Intune del [portale aziendale](/Intune/store-apps-company-portal-app) consente di accedere su richiesta a strumenti aggiuntivi che è possibile eseguire il push verso il basso per gli utenti delle workstation protette.

Potrebbe essere necessario installare app a 32 bit di Windows o altre app la cui distribuzione richiede preparazioni speciali. In questi casi, lo strumento di preparazione del [contenuto win32 Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) può fornire un file di formato pronto all'uso `.intunewin` per l'installazione.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Accesso condizionale che consente solo la possibilità di accesso al portale di AzureConditional Access allowing only secured workstation ability to access Azure portal

Azure AD offre la possibilità di gestire e limitare, chi e cosa può accedere al portale di gestione cloud di Azure.Azure AD offers the ability to manage and restrict, who and what can access your Azure cloud management portal. L'abilitazione [dell'accesso condizionale](../conditional-access/overview.md) garantisce che solo la workstation protetta possa gestire o modificare le risorse. È essenziale che durante la distribuzione di questa funzionalità si consideri, se la funzionalità di accesso di [emergenza](../users-groups-roles/directory-emergency-access.md) può o deve essere utilizzata solo per casi estremi e l'account gestito tramite criteri.

> [!NOTE]
> Sarà necessario creare un gruppo di utenti e includere l'utente di emergenza che può ignorare i criteri di accesso condizionale. Per il nostro esempio abbiamo un gruppo di sicurezza chiamato **Emergency BreakGlass**

1. Passare al **portale** > di Azure Accesso condizionale**di Microsoft Intune** > **- Nuovi** > **criteri .**
1. Specificare un **nome** per il criterio.
1. Selezionare **Utenti e gruppi** > **Selezionare utenti e gruppi** 
1. Selezionare **Includi** > **ruoli directory** > scegliere i ruoli > Amministratore globale, Amministratore con privilegi, Amministratore autenticazione con privilegi, Amministratore sicurezza, Amministratore conformità, Amministratore con accesso condizionale, Amministratore applicazioni, Amministratore applicazione cloud, Amministratore servizio Intune
1. Selezionare **Escludi** > Scegli **utenti e gruppi** > Seleziona utenti **esclusi** > Seleziona il gruppo BreakGlass di **emergenza.**
1. Selezionare **App o azioni cloud** > Seleziona tutte le app **cloud**
1. Selezionare **le condizioni** > selezionare **Piattaforme dispositivo** > scegliere Configura **sì** > **Seleziona piattaforme dispositivo** Scegli **Windows**
1. Selezionare **Controlli di accesso** > Selezionare **Concedi accesso** **Sì** > Scegliere Richiedi dispositivo **da contrassegnare come conforme.** 
1. Selezionare **Abilita criterio** > **su**
 
Questo set di criteri garantisce che gli amministratori devono usare un dispositivo Windows conforme, impostato da Intune, e WDATP. 

Le organizzazioni devono anche considerare la possibilità di bloccare i protocolli di autenticazione legacy nei propri ambienti. Esistono diversi modi per eseguire questa attività, per altre informazioni sul blocco dei protocolli di autenticazione legacy, vedere l'articolo [Procedura: Bloccare l'autenticazione legacy in Azure AD con accesso condizionale](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Usare PowerShell per creare impostazioni personalizzateUse PowerShell to create custom settings

È anche possibile usare PowerShell per estendere le funzionalità di gestione host. Questo script di esempio imposta uno sfondo predefinito nell'host. Si tratta di una funzionalità disponibile anche tramite il portale e i profili di Azure.It's a capability that's available also through the Azure portal and profiles. Lo script di esempio serve solo per illustrare la funzionalità di PowerShell.The example script serve only to illustrate the PowerShell functionality.

Potrebbe essere necessario configurare alcuni controlli e impostazioni personalizzati sulle workstation protette. In questo esempio viene modificato lo sfondo della workstation utilizzando la capacità di Powershell di identificare facilmente il dispositivo come una workstation sicura e pronta all'uso.

Lo script [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) di Microsoft Scripting Center consente a Windows di caricare questa immagine di [sfondo generica gratuita all'avvio.](https://i.imgur.com/OAJ28zO.png)

1. Scaricare lo script in un dispositivo locale.
1. Aggiornare il customerXXXX e il percorso di download dell'immagine di sfondo. Nel nostro esempio, sostituiamo customerXXXX in sfondi.
1. Passare al **portale** > di Azure Configurazione**dispositivo** > **Microsoft Intune** > **Aggiungere****.** > 
1. Specificare un **Nome** per lo script e specificare il **percorso dello script**.
1. Selezionare **Configura**.
   1. Impostare **Esegui lo script utilizzando le credenziali di accesso** su **Sì**.
   1. Selezionare **OK**.
1. Selezionare **Crea**.
1. Selezionare **Assegnazioni** > **Selezionare gruppi**.
   1. Aggiungere il gruppo di protezione **Secure Workstations**.
   1. Selezionare **Salva**.

## <a name="enroll-and-validate-your-first-device"></a>Registrare e convalidare il primo dispositivo

1. Per registrare il dispositivo, sono necessarie le seguenti informazioni:
   * **Numero di serie** - trovato sullo chassis del dispositivo.
   * **Windows Product ID** - si trova in **Sistema** > **informazioni** dal menu Impostazioni di Windows.
   * È possibile eseguire [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) per ottenere un file hash CSV con tutte le informazioni necessarie per la registrazione del dispositivo.
   
     Eseguire `Get-WindowsAutoPilotInfo – outputfile device1.csv` per restituire le informazioni come file CSV che è possibile importare in Intune.Run to output the information as a CSV file that you can import into to Intune.

     > [!NOTE]
     > Lo script richiede diritti elevati. Viene eseguito come remoto firmato. Il `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando consente l'esecuzione corretta dello script.

   * Puoi raccogliere queste informazioni accedendo a un dispositivo Windows 10 versione 1809 o successiva. Queste informazioni possono inoltre fornire queste informazioni.
1. Nel **portale di Azure**passare a Dispositivi di**registrazione** > Windows per la registrazione di Windows per la registrazione di dispositivi di **microsoft Intune** > per la**registrazione** > dei dispositivi **- Gestire i dispositivi Windows Autopilot**.
1. Seleziona **Importa** e scegli il tuo file CSV.
1. Aggiungere il dispositivo al gruppo di sicurezza **Secure Workstations.**
1. Sul dispositivo Windows 10 che si desidera configurare, vai a Aggiornamento **impostazioni** > di Windows &**ripristino**della**sicurezza** > .
   1. Scegliere **Inizia** in **Reimposta il PC**.
   1. Seguire le istruzioni visualizzate per reimpostare e riconfigurare il dispositivo con i criteri di profilo e conformità configurati.

Dopo aver configurato il dispositivo, completare una revisione e controllare la configurazione. Verificare che il primo dispositivo sia configurato correttamente prima di continuare la distribuzione.

## <a name="assign-devices"></a>Assegnare dispositivi

Per assegnare dispositivi e utenti, è necessario eseguire il mapping dei [profili selezionati](/intune/device-profile-assign) al gruppo di sicurezza. Tutti i nuovi utenti che richiedono autorizzazioni per il servizio devono essere aggiunti anche al gruppo di sicurezza.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Utilizzo di Sentinel e Windows Defender ATP per monitorare e rispondere agli incidenti di sicurezza

Il monitoraggio della distribuzione sicura della workstation può essere eseguito abilitando [Sentinel] e utilizzando la gestione delle minacce e delle [vulnerabilità](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) Le linee guida non forniranno una caccia esaustiva alle minacce, ma forniranno buoni sforzi di buon senso per monitorare e rispondere a potenziali incidenti di sicurezza.

Useremo **Azure Sentinel** per: 

* Raccogliere dati da Intune, dal portale di Azure e Azure AD per il monitoraggio di utenti e dispositiviCollect data from Intune, Azure portal, and Azure AD for user and device monitoring
* Guida Esaminare le attività sospette sulla configurazione di utenti e dispositivi
* E guidare la capacità di esplorare le indagini di sicurezza utilizzando WDATP

Il monitoraggio Sentinel richiede la configurazione dei connettori alle origini dati, ad esempio Azure AD.

1. Nel **portale**di Azure passare a **Sentinel di Azure (anteprima)** > Seleziona **aggiungi**
1. Nella casella **Scegliere un'area di lavoro da aggiungere ad Azure Sentinel** Selezionare **Crea una nuova area di lavoro**
1. Digitare:
   * **Area di lavoro di Log Analytics** - 'Monitoraggio sicuro workstation'
   * **Abbonamento** - Selezionare l'abbonamento attivo
   * Gruppo di **risorse:** selezionare il > di posta elettronica Crea nuovo > Secure Workstation RG > **Ok**
   * **Posizione:** selezionare la posizione più adatta geograficamente per la distribuzione
   * **Livello di prezzo** - Seleziona **per GB (2018)**
1. Selezionare **Ok**.

Successivamente collegheremo le origini dati delle workstation sicure disponibili al monitoraggio.

1. Nel **portale di Azure**passare all'area di lavoro di **Azure Sentinel** >area di lavoro Seleziona **monitoraggio workstation sicura**
1. Selezione **dei connettori dati**
1. Scegliere **Azure Active Directory** > pagina Open Connector > dopo aver esaminato il prerequisito. Passare alla configurazione e selezionare **Connetti** sia per i log di accesso di Azure AD, sia per i log di controllo di Azure AD.
1. Scegliere **Attività di >** pagina Apri connettore > Dopo aver esaminato il prerequisito. Passare alla configurazione dei log attività di Azure > selezionare la sottoscrizione > selezionare **Connessione**

Quando i dati vengono raccolti da Sentinel, sarà possibile osservare l'attività selezionando **Il portale**di Azure , passare a Panoramica di **Azure Sentinel** 

Utilizzeremo **Windows Defender ATP (WDATP)** per:

* Osservare e monitorare continuamente vulnerabilità e configurazioni errate
* Utilizzare WDATP per dare priorità alle minacce dinamiche
* Aumentare la correlazione delle vulnerabilità con avvisi edr (Endpoint Detection and Response)
* Utilizzare il dashboard per identificare la vulnerabilità a livello di computer durante le indagini
* Push out remediations to Intune

Configurare il [dashboard di Defender ATP](https://securitycenter.windows.com/machines). Utilizzo delle indicazioni in [Threat & Vulnerability Management dashboard overview](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitoraggio dell'attività dell'applicazione tramite Microsoft Monitoring Agent (MMA)
A partire dalla workstation Specialized, l'app locker è abilitato per il monitoraggio dell'attività dell'applicazione su una workstation. Affinché il monitoraggio venga integrato nell'area di lavoro di Log Analytics, è necessario seguire un agente MMA e una configurazione. 

> [!NOTE]
> Il profilo workstation Specialized contiene i criteri di monitoraggio di AppLocker. La distribuzione dei criteri è necessaria per il monitoraggio dell'attività dell'applicazione su un client

Distribuire l'agente MMA con lo script di Intune PowerShellDeploy the MMA agent with Intune PowerShell script

1. Scaricare lo script di installazione [su un dispositivo locale.](https://aka.ms/securedworkstationgit)
1. Aggiornare i parametri, **i $WorkSpaceID** e **i $WorkSpaceKey**
1. Passare al **portale** > di Azure Configurazione**dispositivo** > **Microsoft Intune** > **Aggiungere****.** > 
1. Specificare un **Nome** per lo script e specificare il **percorso dello script**.
1. Selezionare **Configura**.
   1. Impostare **Esegui lo script utilizzando le credenziali di accesso** su **Sì**.
   1. Selezionare **OK**.
1. Selezionare **Crea**.
1. Selezionare **Assegnazioni** > **Selezionare gruppi**.
   1. Aggiungere il gruppo di protezione **Secure Workstations**.
   1. Selezionare **Salva**.

Successivamente è necessario configurare Log Analytics per ricevere i nuovi log
1. Nel **portale di Azure**passare a **Log Analytics Workspace** > selezionare - 'Secure Workstation Monitoring'
1. Selezionare **Impostazioni** > avanzate**Dati** > **Registri eventi di Windows**
1. In **Raccogli eventi dai registri eventi seguenti** 
1. Digitare:
   * 'Microsoft-Windows-AppLocker/EXE e DLL' > deselezionare **Informativo**
   * 'Microsoft-Windows-AppLocker/MSI e script' > deselezionare **Informazioni**
   * 'Microsoft-Windows-AppLocker/Packaged app-Deployment' > deselezionare **Informativo**
   * 'Microsoft-Windows-AppLocker/Packaged app-Execution' > deselezionare **Informativo**
1. Selezionare **Salva**

La registrazione dell'applicazione sarà disponibile nell'area di lavoro di Log Analytics selezionata.

## <a name="monitoring"></a>Monitoraggio

* Informazioni su come [rilevare le minacce con Azure Sentinel](/azure/sentinel/tutorial-detect-threats)
* [Esaminare gli eventi imprevisti con Azure SentinelInvestigate incidents with Azure Sentinel](/azure/sentinel/tutorial-investigate-cases)
* [Configurare le risposte automatiche alle minacce in Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Capire come rivedere il [tuo punteggio di esposizione](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Recensisci [la raccomandazione sulla sicurezza](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Gestire le [correzioni per la sicurezzaManage security Remediations](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* Gestire il rilevamento e la [risposta degli endpointManage endpoint detection and response](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitorare i profili con il [monitoraggio dei profili di Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni su [Microsoft Intune](/intune/index).
* Comprendere [Azure AD](../index.yml).
* Utilizzare [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Scopri [Azure Sentinel](/azure/sentinel/)
