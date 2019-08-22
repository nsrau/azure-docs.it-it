---
title: Usare i server NPS esistenti per fornire funzionalità di autenticazione a più fattori di Azure-Azure Active Directory
description: Aggiungere le funzionalità di verifica in due passaggi basate sul cloud all'infrastruttura di autenticazione esistente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 879404b264e9ea6c544c6edf509001b38997bb0c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874339"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente senza dover installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni VPN senza distribuire Azure MFA Server. L'estensione del server dei criteri di rete funge da adattatore tra RADIUS e Azure MFA basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.

Quando si usa l'estensione dei criteri di rete per di Azure MFA, il flusso di autenticazione include i componenti seguenti: 

1. Il **Server NAS/VPN** riceve le richieste dei client VPN e le converte in richieste RADIUS per il Server dei criteri di rete. 
2. Il **Server dei criteri di rete** si connette ad Active Directory per eseguire l'autenticazione principale per le richieste RADIUS e, al completamento dell'operazione, passa la richiesta alle estensioni installate.  
3. L'**estensione di Server dei criteri di rete** attiva una richiesta di autenticazione secondaria per Azure MFA. Dopo che l'estensione riceve la risposta e se la richiesta di verifica MFA ha esito positivo, la richiesta di autenticazione viene completata, fornendo al server di Server dei criteri di rete i token di sicurezza che includono un'attestazione MFA, emessa dal servizio token di sicurezza di Azure.  
4. **Azure MFA** comunica con Azure Active Directory per recuperare i dettagli dell'utente ed esegue l'autenticazione secondaria grazie al metodo di verifica configurato per l'utente.

Il diagramma seguente illustra questo flusso di richiesta di autenticazione ad alto livello: 

![Diagramma del flusso di autenticazione](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Pianificare la distribuzione

L'estensione di Server dei criteri di rete gestisce automaticamente la ridondanza, pertanto non è necessaria una configurazione speciale.

È possibile creare un qualsiasi numero di Server dei criteri di rete abilitati per Azure MFA. Se si installano più server, è consigliabile usare un certificato client di differenza per ciascuno. La creazione di un certificato per ogni server significa che è possibile aggiornare singolarmente ogni certificato senza doversi preoccupare dei tempi di inattività in tutti i server.

I server VPN indirizzano le richieste di autenticazione, quindi è necessario essere a conoscenza dei nuovi Server dei criteri di rete abilitati per Azure MFA.

## <a name="prerequisites"></a>Prerequisiti

L'estensione di Server dei criteri di rete è progettata per funzionare con l'infrastruttura esistente. Prima di iniziare, verificare che i prerequisiti seguenti siano disponibili.

### <a name="licenses"></a>Licenze

L'estensione di Server dei criteri di rete per Azure MFA è disponibile per i clienti dotati di [licenze per Multi-Factor Authentication di Azure](multi-factor-authentication.md), tra cui una licenza autonoma di Azure AD Premium, EMS o MFA. Le licenze in base al consumo per Azure MFA, ad esempio le licenze per utente o per autenticazione, non sono compatibili con l'estensione Server dei criteri di rete. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 o versione successiva.

### <a name="libraries"></a>Librerie

Queste librerie vengono installate automaticamente con l'estensione.

- [Visual C++ Redistributable Packages per Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Modulo di Microsoft Azure Active Directory per Windows PowerShell versione 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Il modulo di Microsoft Azure Active Directory per Windows PowerShell viene installato, se non è già presente, tramite uno script di configurazione eseguito come parte del processo di installazione. Non è necessario installare il modulo in anticipo, se non è già installato.

### <a name="azure-active-directory"></a>Azure Active Directory

Gli utenti che usano l'estensione di Server dei criteri di rete devono essere sincronizzati con Azure Active Directory tramite Azure AD Connect ed essere registrati a MFA.

Quando si installa l'estensione, per il tenant di Azure AD sono necessarie le credenziali di amministrazione e l'ID della directory. L'ID della directory si trova nel [Portale di Azure](https://portal.azure.com). Accedere come amministratore, selezionare l'icona di **Azure Active Directory** sulla sinistra, quindi selezionare **Proprietà**. Copiare il GUID nella casella **ID directory** e salvare. Questo GUID verrà usato come ID tenant quando si installerà l'estensione di Server dei criteri di rete.

![L'ID directory si trova nelle proprietà di Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Requisiti di rete

Il server NPS deve essere in grado di comunicare con gli URL seguenti sulle porte 80 e 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Inoltre, è necessaria la connettività agli URL seguenti per completare l' [installazione dell'adapter mediante lo script di PowerShell fornito](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Prima di installare l'estensione di Server dei criteri di rete, è necessario preparare l'ambiente per gestire il traffico di autenticazione.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Abilitare il ruolo del Server dei criteri di rete in un server appartenente a un dominio

Il Server dei criteri di rete si connette ad Azure Active Directory e autentica le richieste di MFA. Scegliere un server per questo ruolo. Si consiglia di scegliere un server che non gestisce le richieste provenienti da altri servizi, poiché l'estensione di Server dei criteri di rete genera errori per qualsiasi richiesta non RADIUS. Il Server dei criteri di rete deve essere configurato come server di autenticazione primaria e secondaria per l'ambiente. Non può inviare tramite proxy le richieste RADIUS a un altro server.

1. Sul server avviare l'**aggiunta guidata ruoli e funzionalità** dal menu Avvio rapido di Server Manager.
2. Come tipo di installazione scegliere **Installazione basata su ruoli o basata su funzionalità**.
3. Selezionare il ruolo del server **Servizi di accesso e criteri di rete**. Potrebbe essere visualizzata una finestra per informare l'utente riguardo alle funzionalità necessarie per eseguire questo ruolo.
4. Continuare la procedura guidata fino alla pagina di conferma. Selezionare **Installa**.

Ora che si dispone di un server designato come Server dei criteri di rete, è inoltre necessario configurarlo per gestire le richieste RADIUS in ingresso dalla soluzione VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurare la soluzione VPN in modo che comunichi con il Server dei criteri di rete

La procedura per configurare i criteri di autenticazione RADIUS può variare a seconda della soluzione VPN in uso. Configurare i criteri in modo che la soluzione punti al Server dei criteri di rete RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizzare gli utenti del dominio con il cloud

Questo passaggio potrebbe essere già completato nel tenant, tuttavia è consigliabile verificare che Azure AD Connect abbia sincronizzato i database di recente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Selezionare **Azure Active Directory** > **Azure AD Connect**
3. Verificare che lo stato della sincronizzazione sia **Abilitata** e che l'ultima sincronizzazione sia stata eseguita da meno di un'ora.

Se è necessario avviare un nuovo ciclo di sincronizzazione, usare le istruzioni riportate in [Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinare i metodi di autenticazione che è possibile usare

Sono due i fattori che determinano i metodi di autenticazione disponibili con una distribuzione dell'estensione di Server dei criteri di rete:

1. L'algoritmo di crittografia della password usato tra il client RADIUS (VPN, server Netscaler o altri) e i Server dei criteri di rete.
   - **PAP** supporta tutti i metodi di autenticazione di Azure MFA nel cloud: chiamata telefonica, SMS unidirezionale, notifica dell'app per dispositivi mobili e codice di verifica dell'app per dispositivi mobili.
   - **CHAPV2** e **EAP** supportano la chiamata telefonica e la notifica dell'app per dispositivi mobili.

      > [!NOTE]
      > Quando si distribuisce l'estensione di Server dei criteri di rete, usare questi fattori per valutare i metodi disponibili per gli utenti. Se il client RADIUS supporta PAP, ma nel client non esistono campi di input per un codice di verifica, la chiamata telefonica e la notifica dell'app per dispositivi mobili sono le due opzioni supportate.
      >
      > Inoltre, se il client VPN UX supporta il campo di input e sono stati configurati i criteri di accesso alla rete, l'autenticazione potrebbe avere esito positivo, tuttavia nessuno degli attributi RADIUS configurati nei criteri di rete verrà applicato né al dispositivo di accesso alla rete. come il server RRAS, né il client VPN. Di conseguenza, il client VPN potrebbe avere un accesso maggiore di quello desiderato o meno a nessun accesso.
      >

2. I metodi di input che l'applicazione client (VPN, server Netscaler o altra) può gestire. Ad esempio, gli strumenti usati dal client VPN per consentire all'utente di digitare un codice di verifica da un testo o da un'app per dispositivi mobili.

È possibile [disabilitare i metodi di autenticazione non supportati](howto-mfa-mfasettings.md#verification-methods) in Azure.

### <a name="register-users-for-mfa"></a>Registrare utenti per l'MFA

Prima di distribuire e usare l'estensione NPS, gli utenti che devono eseguire la verifica in due passaggi devono essere registrati per l'MFA. Per testare l'estensione in modo più immediato mentre viene distribuita, è necessario almeno un account di test completamente registrato per l'MFA.

Seguire questa procedura per avviare un account di test:

1. Accedere a [https://aka.ms/mfasetup](https://aka.ms/mfasetup) con un account di prova.
2. Seguire le richieste per configurare un metodo di verifica.
3. [Creare un criterio di accesso condizionale](howto-mfa-getstarted.md#create-conditional-access-policy) per richiedere l'autenticazione a più fattori per l'account di test.

## <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete

> [!IMPORTANT]
> Installare l'estensione di Server dei criteri di rete in un server diverso rispetto al punto di accesso della VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Scaricare e installare l'estensione di Server dei criteri di rete per Azure MFA

1. [Scaricare l'estensione di Server dei criteri di rete](https://aka.ms/npsmfa) dall'Area download di Microsoft.
2. Copiare il file binario nel Server dei criteri di rete da configurare.
3. Eseguire *setup.exe* e seguire le istruzioni di installazione. Se si verificano errori, controllare che le due librerie indicate nella sezione sui prerequisiti siano state installate correttamente.

#### <a name="upgrade-the-nps-extension"></a>Aggiornare l'estensione NPS

Quando si aggiorna un'installazione di estensione NPS esistente, per evitare il riavvio del server sottostante completare i passaggi seguenti:

1. Disinstallare la versione esistente
1. Eseguire il nuovo programma di installazione
1. Riavviare il servizio Server dei criteri di rete (IAS)

### <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell

Il programma di installazione crea uno script di PowerShell in questa posizione: `C:\Program Files\Microsoft\AzureMfa\Config` (dove C:\ è l'unità di installazione). Ogni volta che viene eseguito, lo script di PowerShell esegue le azioni seguenti:

- Creare un certificato autofirmato.
- Associare la chiave pubblica del certificato all'entità servizio su Azure AD.
- Archiviare il certificato nell'archivio certificati del computer locale.
- Concedere l'accesso alla chiave privata del certificato all'utente di rete.
- Riavviare il Server dei criteri di rete.

A meno che non si desideri utilizzare i propri certificati (invece dei certificati autofirmati generati dallo script di PowerShell), eseguire lo script di PowerShell per completare l'installazione. Se si installa l'estensione su più server, ciascun server dovrebbe avere il proprio certificato.

1. Eseguire Windows PowerShell come amministratore.
2. Cambiare le directory.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Eseguire lo script di PowerShell creato dal programma di installazione.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Accedere ad Azure AD come amministratore.
5. Prompt di PowerShell per l'ID tenant. Usare il GUID dell'ID directory copiato dal portale di Azure nella sezione relativa ai prerequisiti.
6. Al termine dello script, PowerShell mostra un messaggio di conferma.  

Ripetere questi passaggi per tutti i server dei criteri di rete aggiuntivi che si intende configurare per il bilanciamento del carico.

Se il certificato del computer precedente è scaduto ed è stato generato un nuovo certificato, è necessario eliminare eventuali certificati scaduti. La presenza di certificati scaduti può causare problemi con l'avvio dell'estensione NPS.

> [!NOTE]
> Se si usano i propri certificati invece di generare certificati con lo script di PowerShell, verificare che rispettino la convenzione di denominazione di Server dei criteri di rete. Il nome oggetto deve essere **CN=\<TenantID\>,OU=Estensione di Server dei criteri di rete Microsoft**. 

### <a name="certificate-rollover"></a>Rollover dei certificati

Con la versione 1.0.1.32 dell'estensione NPS, è ora supportata la lettura di più certificati. Questa funzionalità consente di semplificare gli aggiornamenti del certificato in sequenza prima della scadenza. Se l'organizzazione esegue una versione precedente dell'estensione NPS, è necessario eseguire l'aggiornamento alla versione 1.0.1.32 o successiva.

I `AzureMfaNpsExtnConfigSetup.ps1` certificati creati dallo script sono validi per 2 anni. Le organizzazioni IT dovrebbero monitorare i certificati per la scadenza. I certificati per l'estensione NPS vengono inseriti nell'archivio certificati del computer locale in personale e vengono rilasciati all'ID tenant fornito allo script.

Quando un certificato si avvicina alla data di scadenza, è necessario creare un nuovo certificato per sostituirlo.  Questo processo viene eseguito `AzureMfaNpsExtnConfigSetup.ps1` eseguendo di nuovo e mantenendo lo stesso ID tenant quando richiesto. Questo processo deve essere ripetuto in ogni server NPS nell'ambiente in uso.

## <a name="configure-your-nps-extension"></a>Configurare l'estensione di Server dei criteri di rete

In questa sezione sono disponibili considerazioni e suggerimenti sulla progettazione per una corretta distribuzione dell'estensione di Server dei criteri di rete.

### <a name="configuration-limitations"></a>Limitazioni di configurazione

- L'estensione di Server dei criteri di rete per Azure MFA non include strumenti per la migrazione degli utenti e impostazioni dal Server MFA al cloud. Per questo motivo, è consigliabile usare l'estensione per le distribuzioni nuove piuttosto che per quelle esistenti. Se si usano le estensioni in una distribuzione esistente, gli utenti dovranno ripetere il processo di registrazione per popolare i dettagli di Azure MFA nel cloud.  
- L'estensione di Server dei criteri di rete usa UPN dell'Active Directory locale per identificare l'utente in Azure MFA che deve eseguire l'autenticazione secondaria. L'estensione può essere configurata per usare un identificatore differente come ID di accesso alternativo o campo Active Directory personalizzato diverso dall'UPN. Per altre informazioni, vedere l'articolo [Opzioni di configurazione avanzate per l'estensione NPS per Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Non tutti i protocolli di crittografia supportano tutti i metodi di verifica.
   - **PAP** supporta la chiamata telefonica, gli SMS unidirezionali, la notifica dell'app per dispositivi mobili e il codice di verifica app per dispositivi mobili
   - **CHAPV2** e **EAP** supportano la chiamata telefonica e la notifica dell'app per dispositivi mobili

### <a name="control-radius-clients-that-require-mfa"></a>Client RADIUS di controllo che richiedono MFA

Dopo aver abilitato MFA per un client RADIUS utilizzando l'estensione di Server dei criteri di rete, tutte le autenticazioni per questo client devono eseguire MFA. Se si desidera abilitare MFA solo per alcuni client RADIUS, è possibile configurare due server di Server dei criteri di rete e installare l'estensione solo su uno di questi. Configurare i client RADIUS per cui si vuole fare in modo che MFA invii richieste al server di Server dei criteri di rete configurato con l'estensione e gli altri client RADIUS al server di Server dei criteri di rete senza configurazione per l'estensione.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Impostazioni per gli utenti che non sono registrati per MFA

Se sono presenti utenti che non sono registrati per MFA, è possibile stabilire cosa succede quando questi tentano di eseguire l'autenticazione. Usare l'impostazione del registro di sistema *REQUIRE_USER_MATCH* nel percorso del registro di sistema *HKLM\Software\Microsoft\AzureMFA* per controllare il comportamento della funzionalità. Questa impostazione non ha un'unica opzione di configurazione:

| Chiave | Value | Predefinito |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERO/FALSO | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Quando la chiave non esiste, non è impostata o è impostata su VERO e l'utente non è registrato, allora l'estensione non esegue correttamente la richiesta di verifica MFA. Quando la chiave è impostata su FALSO e l'utente non è registrato, l'autenticazione procede senza eseguire MFA. Se un utente è registrato in MFA, è necessario eseguire l'autenticazione con MFA anche se REQUIRE_USER_MATCH è impostato su FALSE.

È possibile scegliere di creare questa chiave e impostarla su FALSE, durante il caricamento degli utenti che potrebbero non essere ancora registrati per Azure MFA. Poiché l'impostazione della chiave consente agli utenti che non sono registrati all'MFA di accedere, è necessario rimuovere la chiave prima di passare all'ambiente di produzione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="nps-extension-health-check-script"></a>Script di controllo integrità estensione NPS

Lo script seguente è disponibile nella raccolta TechNet per eseguire i passaggi di base del controllo di integrità durante la risoluzione dei problemi relativi all'estensione NPS.

[MFA_NPS_Troubleshooter.ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Come verificare che il certificato client sia installato come previsto?

Cercare il certificato autofirmato creato dal programma di installazione nell'archivio dei certificati e verificare che la chiave privata disponga delle autorizzazioni concesse all'utente **Servizio di rete**. Il certificato ha come nome oggetto **CN \<tenantid\>, OU = Estensione di Server dei criteri di rete Microsoft**

Anche i certificati autofirmati generati dallo script *AzureMfaNpsExtnConfigSetup. ps1* hanno una durata di validità di due anni. Quando si verifica che il certificato sia installato, è necessario verificare anche che il certificato non sia scaduto.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Come verificare che il certificato client sia associato al tenant in Azure Active Directory?

Aprire il prompt dei comandi di PowerShell ed eseguire i comandi seguenti:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Questi comandi consentono di stampare tutti i certificati associando il tenant con l'istanza dell'estensione di Server dei criteri di rete nella sessione di PowerShell. Cercare il certificato esportando il certificato client come file "Codificato Base 64 X.509 (.CER)" senza la chiave privata e confrontarlo con l'elenco di PowerShell.

Il comando seguente crea un file denominato "npscertificate" nell'unità "C:" in formato con estensione cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Dopo aver eseguito questo comando, passare all'unità C, individuare il file e fare doppio clic su di esso. Passare ai dettagli e scorrere verso il basso fino all'"identificazione personale" e confrontare l'identificazione personale del certificato installato nel server con questa. Le identificazioni personali devono corrispondere.

I timbri data/ora Valido-dal e Valido-fino al, che sono in formato leggibile, possono essere usati per filtrare i risultati errati se il comando restituisce più di un certificato.

---

### <a name="why-cant-i-sign-in"></a>Perché non è possibile accedere?

Verificare che la password non sia scaduta. L'estensione NPS non supporta la modifica delle password come parte del flusso di lavoro di accesso. Per ulteriore assistenza, contattare il personale IT dell'organizzazione.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Perché le richieste hanno esito negativo con errore di token ADAL?

Questo errore potrebbe essere dovuto a diverse ragioni. Usare la procedura seguente per la risoluzione:

1. Riavviare il server di Server dei criteri di rete.
2. Verificare che il certificato client sia installato come previsto.
3. Verificare che il certificato sia associato al tenant in Azure AD.
4. Verificare che https://login.microsoftonline.com/ sia accessibile dal server che esegue l'estensione.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Perché l'autenticazione ha esito negativo e restituisce un errore nei log HTTP che indica che l'utente non è stato trovato?

Verificare che AD Connect sia in esecuzione e che l'utente sia presente sia in Active Directory di Windows sia in Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Perché vengono visualizzati errori di connessione HTTP nei log che contengono le autenticazioni non riuscite?

Verificare che https://adnotifications.windowsazure.com sia raggiungibile dal server che esegue l'estensione del server dei criteri di rete.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Perché l'autenticazione non funziona, nonostante sia presente un certificato valido?

Se il certificato del computer precedente è scaduto ed è stato generato un nuovo certificato, è necessario eliminare eventuali certificati scaduti. La presenza di certificati scaduti può causare problemi con l'avvio dell'estensione NPS.

Per verificare se si dispone di un certificato valido, controllare l'archivio certificati dell'account computer locale utilizzando MMC e verificare che il certificato non abbia superato la data di scadenza. Per generare un certificato appena valido, eseguire di nuovo i passaggi descritti nella sezione "[eseguire lo script di PowerShell](#run-the-powershell-script)".

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gestione dei protocolli TLS/SSL e dei pacchetti di crittografia

È consigliabile disabilitare o rimuovere i pacchetti di crittografia meno recenti e meno sicuri, a meno che non siano richiesti dall'organizzazione. Altre informazioni sul completamento di questa attività sono disponibili nell'articolo [Gestione dei protocolli SSL/TLS e dei pacchetti di crittografia per AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Risoluzione dei problemi aggiuntiva

Ulteriori indicazioni per la risoluzione dei problemi e le possibili soluzioni sono disponibili nell'articolo [risolvere i messaggi di errore dall'estensione NPS per Azure Multifactor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Passaggi successivi

- Configurare gli ID alternativi per l'accesso o impostare un elenco di eccezioni per gli indirizzi IP che non devono eseguire la verifica in due passaggi in [Advanced configuration options for the NPS extension for Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md) (Opzioni di configurazione avanzate per l'estensione del server dei criteri di rete per Multi-Factor Authentication).

- Informazioni su come integrare [Gateway Desktop remoto](howto-mfa-nps-extension-rdg.md) e [server VPN](howto-mfa-nps-extension-vpn.md) usando l'estensione NPS

- [Risolvere i messaggi di errore dall'estensione del Server dei criteri di rete per Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
