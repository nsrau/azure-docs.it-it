---
title: Distribuire workstation - Azure Active Directory gestite di Azure
description: Informazioni su come distribuire workstation sicure gestiti di Azure per ridurre il rischio di violazione a causa di una configurazione errata o compromissione
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
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307228"
---
# <a name="deploy-a-secure-workstation"></a>Distribuire una workstation sicura

Ora che abbiamo appreso [perché è importante proteggere l'accesso workstation?](concept-azure-managed-workstation.md) è il momento di iniziare il processo di distribuzione usando gli strumenti disponibili. Questo materiale sussidiario useranno i profili definiti per creare una workstation in cui è più sicura dall'inizio.

![Distribuzione di una workstation sicura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Prima di distribuire la soluzione, è necessario selezionare il profilo che verrà utilizzato. È importante notare che è possibile applicare uno dei profili selezionati e passare a un altro tramite l'assegnazione di profilo di Intune in base ai requisiti. Più profili possono essere usati contemporaneamente in una distribuzione e assegnati tramite assegnazioni di gruppo o del tag.

| Profilo | Basso | Avanzato | Alto | Specializzata | Protetto | Isolato |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utente di Azure AD | Yes | Sì | Sì | Sì | Sì | Yes |
| Gestiti da Intune | Yes | Sì | Sì | Sì | Sì | Yes |
| Dispositivi registrati in Azure AD | Yes |  |  |  |  | |   |
| Dispositivi aggiunti ad Azure AD |   | Yes | Sì | Sì | Sì | Yes |
| Baseline della sicurezza Intune applicato |   | Yes <br> (Avanzato) | Yes <br> (HighSecurity) | Yes <br> (NCSC) | Yes <br> (Protetto) |  NA |
| L'hardware soddisfi gli standard di sicuro Windows 10 |   | Yes | Sì | Sì | Sì | Yes |
| Microsoft Defender ATP abilitata |   | Yes  | Sì | Sì | Sì | Yes |
| Rimozione dei diritti di amministratore |   |   | Yes  | Sì | Sì | Yes |
| Distribuzione mediante Microsoft Autopilot |   |   | Yes  | Sì | Sì | Yes |
| App installate solo da Intune |   |   |   | Yes | Sì |Yes |
| URL limitato al solo elenco approvato |   |   |   | Yes | Sì |Yes |
| Internet (in ingresso/in uscita bloccato) |   |   |   |  |  |Yes |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I concetti trattati in questa guida presuppone che Microsoft 365 Enterprise E5 o uno SKU equivalente. Alcuni suggerimenti in questa guida può essere implementato con SKU inferiori. Altre informazioni sono reperibili sul [licenza di Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

È possibile configurare [licenze basate su gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) agli utenti di automatizzare il provisioning delle licenze.

## <a name="azure-active-directory-configuration"></a>Configurazione di Azure Active Directory

Configurazione della directory di Azure Active Directory (Azure AD), che gestirà utenti, gruppi e dispositivi per le workstation dell'amministratore è necessario abilitare Servizi di identità e le funzioni con un [account administrator](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando si crea l'account di amministratore di workstation protetti, si espone l'account nella workstation corrente. Si consiglia che si eseguire la configurazione iniziale e tutte le configurazioni globali da un dispositivo sicuro noto. È possibile prendere in considerazione le indicazioni fornite per [evitare infezioni malware](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) per ridurre il rischio di esposizione prima di tutto l'esperienza primo da attacchi esterni.

Le organizzazioni devono richiedono l'autenticazione a più fattori, almeno per gli amministratori. Visualizzare [distribuire MFA basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) per indicazioni sull'implementazione.

### <a name="azure-ad-users-and-groups"></a>Utenti e gruppi azure AD

Dal portale di Azure, passare a **Azure Active Directory** > **utenti** > **nuovo utente**. [Creare l'utente di protezione delle Workstation](https://docs.microsoft.com/Intune/quickstart-create-user), che sarà l'amministratore del dispositivo.

* **Nome** -amministratore Workstation protette
* **Nome utente** - secure-ws-admin@identityitpro.com
* **Ruolo della directory** - **amministratore con limitazioni** e selezionare il ruolo di amministrazione seguente
   * **Amministratore di Intune**
* **Creare**

Si creerà due gruppi, uno per gli utenti delle workstation e una per le workstation stesse. Dal portale di Azure, passare a **Azure Active Directory** > **gruppi** > **nuovo gruppo**

Primo gruppo per gli utenti di workstation. È possibile configurare [licenze basate su gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) per gli utenti in questo gruppo per automatizzare il provisioning delle licenze agli utenti.

* **Tipo di gruppo** -sicurezza
* **Nome del gruppo** -proteggere gli utenti di Workstation
* **Tipo di appartenenza** : assegnato
* Aggiungere l'utente amministratore di workstation sicura al gruppo
   * secure-ws-admin@identityitpro.com
* È possibile aggiungere qualsiasi altro utente che gestiranno workstation sicure al gruppo
* **Creare**

Secondo gruppo per i dispositivi di workstation.

* **Tipo di gruppo** -sicurezza
* **Nome del gruppo** -Secure workstation
* **Tipo di appartenenza** : assegnato
* **Creare**

### <a name="azure-ad-device-configuration"></a>Configurazione del dispositivo AD Azure

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Specificare chi può aggiungere dispositivi ad Azure AD

Configurare i dispositivi configurazione in Active Directory per consentire il gruppo di protezione amministrativa aggiungere i dispositivi al dominio. Per configurare questa impostazione dal portale di Azure, passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo**. Scegli **Selected** sotto **gli utenti possono aggiungere dispositivi ad Azure AD** e selezionare il gruppo "Utenti di proteggere Workstation".

#### <a name="removal-of-local-admin-rights"></a>Rimozione dei diritti di amministratore locale

Come parte dell'implementazione degli utenti di workstation del VIP, DevOps e workstation sicure a livello non avranno alcun diritto di amministratore nelle proprie macchine. Per configurare questa impostazione dal portale di Azure, passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo**. Selezionare **None** sotto **amministratori locali aggiuntivi su Azure AD i dispositivi aggiunti**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Richiedere l'autenticazione a più fattori per aggiungere i dispositivi

Per potenziare ulteriormente il processo di aggiunta dei dispositivi ad Azure AD, passare a **Azure Active Directory** > **dispositivi** > **impostazioni dispositivo** Scegliere **Yes** sotto **Richiedi multi-Factor Auth per aggiungere i dispositivi** quindi scegliere **Salva**.

#### <a name="configure-mdm"></a>Configurare MDM

Dal portale di Azure, passare a **Azure Active Directory** > **servizi Mobility (MDM e MAM)**  > **Microsoft Intune**. Modificare l'impostazione **ambito utente MDM** al **tutte** e scegliere **Salva** come verrà consentito a qualsiasi dispositivo da gestire con Intune in questo scenario. Altre informazioni sono reperibili nell'articolo [Intune Quickstart: Configurare la registrazione automatica per i dispositivi Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Si creerà i criteri di configurazione e la conformità di Intune in un passaggio successivo.

#### <a name="azure-ad-conditional-access"></a>Accesso condizionale di Azure AD

Accesso condizionale di Azure AD consente di mantenere queste attività amministrative con privilegi dispositivi compatibili. Gli utenti sono stati definiti come membri delle **proteggere gli utenti di Workstation** gruppo sarà necessario eseguire l'autenticazione a più fattori durante l'accesso alle applicazioni cloud. Verrà seguire le procedure consigliate ed escludere i nostri account di accesso di emergenza dai criteri. Altre informazioni sono reperibili nell'articolo [gestire gli account di accesso di emergenza in Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Per configurare l'accesso condizionale dal portale di Azure, passare a **Azure Active Directory** > **accesso condizionale** > **nuovo criterio**.

* **Nome** -protetta dei dispositivi necessarie dei criteri
* Assegnazioni
   * **Utenti e gruppi**
      * Includere - **utenti e gruppi** : selezionare la **proteggere gli utenti di Workstation** gruppo creato in precedenza
      * Escludere - **utenti e gruppi** -selezionare l'account di accesso di emergenza dell'organizzazione
   * **App cloud**
      * Includere - **tutte le app cloud**
* Controlli di accesso
   * **Concessione** : selezionare questa opzione **concedere l'accesso** pulsante di opzione
      * **Richiedi autenticazione a più fattori**
      * **Richiedi che i dispositivi siano contrassegnati come conformi**
      * Per più controlli - **richiedono tutti i controlli selezionati**
* Abilitare i criteri - **su**

Le organizzazioni possono facoltativamente creare i criteri di blocco paesi in cui utenti non accedono alle risorse aziendali. Altre informazioni sui criteri di accesso condizionale basato su indirizzo IP sono reperibili nell'articolo [che cos'è la condizione della posizione nell'accesso condizionale di Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Configurazione di Intune

### <a name="configure-enrollment-status"></a>Configurare lo stato di registrazione

Come indicato nella gestione della supply chain, garantendo la workstation sicura è un dispositivo pulito attendibile, è consigliabile che al momento dell'acquisto di nuovi dispositivi, che i dispositivi da factory impostato su [Windows 10 Pro in modalità S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), che limita l'esposizione e le vulnerabilità durante la gestione della supply chain. Dopo che un dispositivo viene ricevuto dal fornitore, il dispositivo verrà rimosso dalla modalità S uso di Autopilot. Il materiale sussidiario seguente fornisce informazioni dettagliate sull'applicazione del processo di trasformazione.

Microsoft vuole garantire che i dispositivi siano completamente configurati prima dell'uso. Intune offre un mezzo per **blocca l'uso di dispositivi fino a quando tutte le App e i profili vengono installati**. 

1. Dal **portale di Azure** passare a:
1. **Microsoft Intune** > **registrazione di dispositivi** > **registrazione di Windows** > **pagina dello stato di registrazione (anteprima)**  >  **Default** > **impostazioni**.
1. Impostare **Mostra lo stato dell'installazione del profilo app** al **Yes**.
1. Impostare **blocca l'uso di dispositivi fino a quando tutti i profili e le app installati** al **Sì**.

### <a name="create-an-autopilot-deployment-profile"></a>Creare un profilo di distribuzione Autopilot

Dopo aver creato un gruppo di dispositivi, è necessario creare un profilo di distribuzione in modo che sia possibile configurare i dispositivi Autopilot.

1. In Intune nel portale di Azure, scegliere **registrazione del dispositivo** > **registrazione Windows** > **profili di distribuzione**  >   **Crea profilo**.
1. Per il nome, immettere **proteggere il profilo di distribuzione di workstation**. Per la descrizione, immettere **distribuzione di workstation sicure**.
1. Set convertire destinazione tutti i dispositivi Autopilot su Sì. Questa impostazione garantisce che nell'elenco di tutti i dispositivi registrati con il servizio di distribuzione Autopilot.  Consentire a 48 ore per la registrazione per l'elaborazione.
1. Per la modalità di distribuzione, scegliere **self-distribuzione (anteprima)** . I dispositivi con questo profilo sono associati all'utente la registrazione del dispositivo. Le credenziali dell'utente sono necessarie per registrare il dispositivo.
1. Nel Join Azure ad come finestra **aggiunti ad Azure AD** deve essere scelto e in grigio.
1. Selezionare l'esperienza di Out-of-box (OOBE), configurare i seguenti opzione e lasciare gli altri impostata sul valore predefinito e quindi selezionare **accettabile**:
   1. Tipo di account utente: **Standard**
1. Selezionare **Crea** per creare il profilo. Il profilo di distribuzione Autopilot è ora disponibile da assegnare ai dispositivi.
1. Scegli **assegnazioni** > **assegnare ai** > **gruppi selezionati**
   1. **Selezionare i gruppi da includere** -proteggere gli utenti di Workstation

### <a name="configure-windows-update"></a>Configurare Windows Update

Una delle operazioni più importanti che un'organizzazione può eseguire è aggiornare Windows 10. Per gestire Windows 10 in uno stato protetto, si distribuirà un anello di aggiornamento per gestire la velocità con cui gli aggiornamenti alla workstation. Questa configurazione è reperibile nella **portale di Azure** > **Microsoft Intune** > **gli aggiornamenti Software**  >  **Anelli di aggiornamento di Windows 10**.

Ci impegniamo **Create** un nuovo anello di aggiornamento con le seguenti impostazioni modificati i valori predefiniti.

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

Fare clic su **crea** successivamente nella **assegnazioni** scheda Aggiungi il **Secure workstation** gruppo come gruppo incluso.

Altre informazioni sui criteri di Windows Update sono reperibile [Policy CSP - aggiornamento](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Integrazione di Intune di Windows Defender ATP

Windows Defender ATP e Microsoft Intune interagiscono per aiutare a prevenire le violazioni della sicurezza e limitare l'impatto di violazioni della sicurezza. Le funzionalità fornirà rilevamento in tempo reale. ATP fornirà anche i controllo complete di distribuzione e la registrazione dei dispositivi dell'endpoint.

Per configurare l'integrazione di Windows Defender ATP Intune nel portale di Azure, passare a **Microsoft Intune** > **conformità del dispositivo** > **Windows Defender ATP** .

1. Nel passaggio 1 in **configurazione di Windows Defender ATP**, fare clic su **Connect Windows Defender ATP a Microsoft Intune in Windows Defender Security Center**.
1. In Windows Defender Security Center:
   1. Selezionare **le impostazioni** > **funzionalità avanzate**
   1. Per la **connessione di Microsoft Intune**, scegliere **su**
   1. Selezionare **salvare le preferenze**
1. Dopo aver stabilita una connessione, restituire a Intune e fare clic su **Aggiorna** nella parte superiore.
1. Impostare **Connect Windows 10.0.15063 versione di dispositivi e versioni successive di Windows Defender ATP** al **su**.
1. **Salva**

Altre informazioni sono reperibili nell'articolo [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Il completamento di protezione avanzata del profilo di workstation

Per completare il rafforzamento della soluzione, scaricare ed eseguire lo script basato sulla desiderato **a livello di profilo** dal grafico seguente.

| Profilo | Percorso di download | Nome del file |
| --- | --- | --- |
| Livello di protezione basso | N/D |  N/D |
| Sicurezza avanzata | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Sicurezza elevata  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializzata | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - SecurityBaseline.ps1 Windows10 (1803) |
| Conformità specializzato * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protetto | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Conformità * specializzata è uno script che applica la configurazione specializzata fornita nel SecurityBaseline Windows10 NCSC.

Una volta eseguito correttamente lo script selezionato, è possibile apportare aggiornamenti per i profili e i criteri in Microsoft Intune. Gli script per i profili avanzato e sicura per creano i criteri e profili, ma è necessario assegnare i criteri per i **workstation Secure** gruppo.

* Profili di configurazione dispositivo di Intune creati dagli script sono reperibili nel **portale di Azure** > **Microsoft Intune** > **configurazionedeldispositivo**  >  **Profili**.
* Criteri di conformità di dispositivi di Intune creati dagli script sono reperibili nel **portale di Azure** > **Microsoft Intune** > **conformità del dispositivo**  >  **Criteri**.

Per rivedere le modifiche è inoltre possibile esportare i profili e applicare le modifiche nel file di esportazione, come descritto nel SECCON documentazione in base e aggiuntive protezione avanzata che è necessaria.

In esecuzione i dati di Intune esportare uno script `DeviceConfiguration_Export.ps1` dal [DeviceConfiguration GiuHub repository](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) fornirà l'esportazione corrente di tutti i profili di Intune esistenti.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurazioni aggiuntive e protezione avanzata da considerare

Le indicazioni fornite ha abilitato una workstation protetta, controlli aggiuntivi anche da considerare, ad esempio i browser alternativi di accesso, in uscita HTTP consentite e bloccate, siti Web e la possibilità di eseguire script di PowerShell personalizzati.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Regole in ingresso e in uscita restrittive nel provider di servizi di configurazione di firewall (CSP)

Gestione aggiuntiva delle regole in ingresso e in uscita può essere aggiornati per riflettere gli endpoint consentiti e bloccati. Mentre continuiamo a rafforzare la protezione della workstation sicura, si sposta la restrizione su una nega tutto in ingresso e in uscita come valore predefinito e aggiungere siti consentiti per in uscita in modo da riflettere i siti web attendibili e comuni. Informazioni di configurazione aggiuntive per il provider di servizi di configurazione di Firewall sono reperibili nell'articolo [CSP Firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Raccomandazioni con restrizioni predefinite sono:

* Nega tutto in ingresso
* Nega tutto in uscita

Il progetto Spamhaus mantiene un elenco che le organizzazioni possono usare come punto di partenza per il blocco di siti nota come [elenco di blocchi di dominio (doppio)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>La gestione delle applicazioni locali

La rimozione delle applicazioni locali, tra cui la rimozione di applicazioni di produttività sposterà le workstation sicura a uno stato realmente con protezione avanzato. In questo esempio, verrà aggiunto Chrome come browser predefinito e limitare la possibilità di modificare il browser tra cui plugin usando Intune.

#### <a name="deploy-applications-using-intune"></a>Distribuire le applicazioni con Intune

In alcune situazioni, applicazioni, ad esempio il browser Google Chrome sono necessari nella workstation protette. Nell'esempio seguente vengono fornite istruzioni per installare Chrome per i dispositivi nel gruppo di sicurezza **workstation Secure** creato in precedenza.

1. Scaricare il programma di installazione offline [bundle di Chrome per Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Estrarre i file e prendere nota della posizione del `GoogleChromeStandaloneEnterprise64.msi` installare con Intune
1. Nel **portale di Azure** individuare **Microsoft Intune** > **App Client** > **app**  >  **Aggiungere**
1. Sotto **tipo di App**, scegliere **Line-of-business**
1. Sotto **file pacchetto dell'App**, selezionare la `GoogleChromeStandaloneEnterprise64.msi` il percorso estratta e fare clic su **OK**
1. Sotto **le informazioni sull'App**, specificare una descrizione e un server di pubblicazione e scegliere **OK**
1. Fare clic su **Aggiungi**.
1. Nel **assegnazioni** seleziona **disponibile per i dispositivi registrati** sotto **tipo di assegnazione**
1. Sotto **gruppi inclusi**, aggiungere il **Secure workstation** gruppo creato in precedenza
1. Fare clic su **OK** quindi **salvare**

Informazioni aggiuntive sulla configurazione delle impostazioni di Chrome sono reperibile nel loro articolo del supporto tecnico [gestire Browser Chrome con Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurazione del portale aziendale per le app personalizzate

In modalità protetta, installare le applicazioni saranno limitate a portale aziendale di Intune. Tuttavia, l'installazione del portale richiede l'accesso a Microsoft Store. Nella nostra soluzione protetta, Microsoft farà il portale disponibile per tutti i dispositivi usando una modalità offline dell'App portale aziendale.

Installare una di Intune gestito copia del [portale aziendale](https://docs.microsoft.com/Intune/store-apps-company-portal-app) consentirà la possibilità di eseguire il push down strumenti aggiuntivi su richiesta agli utenti delle workstation protetti.

Alcune organizzazioni potrebbero essere necessari per installare le app di Windows 32 bit o le app che richiedono altre operazioni preliminari per la distribuzione. Per queste applicazioni, il [strumento di preparazione del contenuto di Microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) fornirà un pronti all'uso `.intunewin` file di formato per l'installazione.

### <a name="setting-up-custom-settings-using-powershell"></a>Configurare le impostazioni personalizzate usando PowerShell

Inoltre, si userà un esempio di come usare PowerShell per fornire estendibilità in la gestione dell'host. Lo script verrà impostato il colore di sfondo sull'host. Questa funzionalità è disponibile anche nei profili e viene usata solo per illustrare le funzionalità.

Nella soluzione potrebbe essere necessario configurare alcuni controlli personalizzati e le impostazioni nelle workstation sicura. In questo esempio, modificare lo sfondo della workstation usando Powershell sia in grado di identificare facilmente il dispositivo come una workstation sicura pronta per l'uso. Sebbene questo esempio Usa PowerShell per completare questa attività, si può essere eseguito anche nel portale di Azure.

Questo esempio utilizza la seguente [immagine di sfondo generico gratuito](https://i.imgur.com/OAJ28zO.png) e il [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) dal Microsoft Scripting Center per consentire di Windows caricare lo sfondo nel menu start.

1. Scaricare lo script in un dispositivo locale
1. Aggiornare il customerXXXX e il percorso di download di sfondo desiderato per l'uso nello script in modo da riflettere il file in background e cartella che si desidera la distribuzione da usare. In questo esempio, abbiamo sostituire customerXXXX per gli sfondi.  
1. Selezionare il **portale di Azure** > **Microsoft Intune** > **configurazione del dispositivo** > **PowerShell gli script** > **Add**
1. Fornire una **Name** per lo script e specificare le **percorso dello Script** in cui è stato scaricato
1. Selezionare **Configura**
   1. Impostare **eseguire questo script usando usato per l'accesso alle credenziali**, a **Sì**
   1. Fare clic su **OK**.
1. Fare clic su **Crea**
1. Selezionare **assegnazioni** > **selezionare i gruppi**
   1. Aggiungere il gruppo di sicurezza **workstation sicura** creato in precedenza e fare clic su **salvare**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>L'uso dell'anteprima: Baseline della sicurezza MDM per ottobre 2018

Microsoft Intune è introdotta la funzionalità di gestione della linea di base di sicurezza fornendo agli amministratori un modo semplice per applicare una condizione di sicurezza della linea di base comune. La linea di base fornisce un mezzo simile per ottenere un blocco profilo tasche avanzato.

Per la workstation sicura, implementazione di che questa linea di base non viene utilizzato perché è in conflitto con la distribuzione di una configurazione protetta.

|   |   |   |
| :---: | :---: | :---: |
| Blocco sopra | Installazione dei dispositivi | Servizi Desktop remoto |
| Fase di esecuzione di App | Blocco del dispositivo | Gestione remota |
| Gestione di applicazioni | Servizio Registro eventi | Chiamata di procedura remota |
| Riproduzione automatica | Esperienza | Ricerca |
| BitLocker | Exploit Guard | SmartScreen |
| Browser | Esplora file | Requisiti di sistema|
| Connettività | Internet Explorer | Wi-Fi |
| Delega delle credenziali | Opzioni di sicurezza Criteri locali | Gestione connessione di Windows |
| Credenziali dell'interfaccia utente | Guida alla sicurezza MS | Windows Defender|
| Protezione dati | MSS Legacy | Area di lavoro Windows Ink |
| Device Guard | Potenza | Windows PowerShell |

Altre informazioni su questa funzionalità in anteprima sono reperibili nell'articolo [impostazioni di base di sicurezza di Windows per Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Registrare e convalidare il primo dispositivo

1. Per registrare il dispositivo, sono necessarie le informazioni seguenti:
   * **Numero di serie** : trovato sullo chassis del dispositivo
   * **ID del prodotto Windows** : trovato sotto **System** > **sulle** nel menu delle impostazioni di Windows.
   * In esecuzione [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) fornirà un file CSV di hash per registrazione del dispositivo con tutte le informazioni necessarie. 
      * Eseguire `Get-WindowsAutoPilotInfo – outputfile device1.csv` per restituire le informazioni come file CSV che può essere importato Intune.

   > [!NOTE]
   > Lo script verrà richiedono diritti con privilegi elevati ed eseguire come remoto firmato. È possibile usare il comando seguente per consentire il corretta esecuzione dello script. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  È possibile raccogliere queste informazioni tramite l'accesso a una versione di Windows 10 1809 o dispositivo superiore per raccogliere le informazioni o il rivenditore di hardware può fornire queste informazioni ordinando i nuovi dispositivi.
1. Raccogliere le informazioni necessarie e restituire per il **portale di Azure**. Passare a **Microsoft Intune** > **registrazione di dispositivi** > **registrazione Windows** > **per i dispositivi: Gestire i dispositivi Windows Autopilot**, selezionare **importazione**, quindi scegliere il file CSV è stato creato o sono stati specificati.
1. Aggiungere il dispositivo ora registrato per il gruppo di sicurezza **workstation Secure** creato in precedenza.
1. Dal dispositivo Windows 10 che si desidera configurare, individuare **delle impostazioni di Windows** > **aggiornamento e sicurezza** > **ripristino**. Scegli **iniziare** sotto **reimpostare questo PC** e seguire le istruzioni per reimpostare e riconfigurare il dispositivo usando i criteri di conformità e del profilo configurati.

Dopo aver configurato il dispositivo, completare una verifica e controllare la configurazione. Verificare che il primo dispositivo sia configurato correttamente prima di continuare la distribuzione.

## <a name="assignment-and-monitoring"></a>Assegnazione e monitoraggio

Per assegnare utenti e dispositivi è necessario il mapping del [i profili selezionati](https://docs.microsoft.com/intune/device-profile-assign) per la sicurezza del gruppo e tutti i nuovi utenti che verranno concessa l'autorizzazione per il servizio verrà richiesto di essere aggiunti al gruppo di sicurezza nonché.

I profili di monitoraggio può essere eseguito tramite il monitoraggio [profili Microsoft Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Passaggi successivi

[Microsoft Intune](https://docs.microsoft.com/intune/index) documentazione

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) documentazione