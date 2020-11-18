---
title: Usare Azure AD Multi-Factor Authentication con NPS-Azure Active Directory
description: Informazioni su come usare Azure AD Multi-Factor Authentication le funzionalità con l'infrastruttura di autenticazione server dei criteri di rete (NPS) esistente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 576b9c11f167f7c0d5fcb06e484347c643589a66
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839064"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Integrare l'infrastruttura server dei criteri di rete (NPS) esistente con Azure AD Multi-Factor Authentication

L'estensione NPS (Network Policy Server) per Azure AD Multi-Factor Authentication aggiunge funzionalità di autenticazione a più fattori basate sul cloud all'infrastruttura di autenticazione usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente senza dover installare, configurare e gestire nuovi server.

L'estensione NPS funge da adattatore tra RADIUS e Azure AD basato sul cloud Multi-Factor Authentication per fornire un secondo fattore di autenticazione per gli utenti federati o sincronizzati.

## <a name="how-the-nps-extension-works"></a>Funzionamento dell'estensione NPS

Quando si usa l'estensione NPS per Azure AD Multi-Factor Authentication, il flusso di autenticazione include i componenti seguenti:

1. Il **Server NAS/VPN** riceve le richieste dei client VPN e le converte in richieste RADIUS per il Server dei criteri di rete.
2. Il **server NPS** si connette a Active Directory Domain Services (ad DS) per eseguire l'autenticazione principale per le richieste RADIUS e, al completamento dell'operazione, passa la richiesta a tutte le estensioni installate.  
3. L' **estensione NPS** attiva una richiesta di Azure ad multi-factor authentication per l'autenticazione secondaria. Dopo che l'estensione riceve la risposta e se la richiesta di verifica MFA ha esito positivo, la richiesta di autenticazione viene completata, fornendo al server di Server dei criteri di rete i token di sicurezza che includono un'attestazione MFA, emessa dal servizio token di sicurezza di Azure.
4. **Azure ad** multi-factor authentication comunica con Azure Active Directory (Azure ad) per recuperare i dettagli dell'utente ed esegue l'autenticazione secondaria usando un metodo di verifica configurato per l'utente.

Il diagramma seguente illustra questo flusso di richiesta di autenticazione ad alto livello:

![Diagramma del flusso di autenticazione per l'autenticazione degli utenti tramite un server VPN al server dei criteri di rete e il Azure AD Multi-Factor Authentication estensione NPS](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Comportamento del protocollo RADIUS e dell'estensione NPS

Poiché RADIUS è un protocollo UDP, il mittente presuppone una perdita di pacchetti e attende una risposta. Dopo un periodo di tempo, è possibile che si verifichi il timeout della connessione. In tal caso, il pacchetto viene inviato nuovamente perché il mittente presuppone che il pacchetto non raggiunga la destinazione. Nello scenario di autenticazione di questo articolo, i server VPN inviano la richiesta e attendono una risposta. Se si verifica il timeout della connessione, il server VPN invia nuovamente la richiesta.

![Diagramma del flusso di pacchetti UDP RADIUS e richieste dopo il timeout in risposta dal server NPS](./media/howto-mfa-nps-extension/radius-flow.png)

Il server NPS potrebbe non rispondere alla richiesta originale del server VPN prima che si verifichi il timeout della connessione perché la richiesta di autenticazione a più fattori potrebbe essere ancora in corso di elaborazione. È possibile che l'utente non abbia risposto correttamente alla richiesta di autenticazione a più fattori, quindi il Azure AD Multi-Factor Authentication estensione NPS è in attesa del completamento dell'evento. In questa situazione, il server dei criteri di rete identifica richieste server VPN aggiuntive come richiesta di duplicazione. Il server dei criteri di rete elimina queste richieste di server VPN duplicate.

![Diagramma del server NPS che ignora le richieste duplicate dal server RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Se si esaminano i log del server NPS, è possibile che vengano rimosse le seguenti richieste aggiuntive. Questo comportamento è progettato per impedire all'utente finale di ottenere più richieste per un singolo tentativo di autenticazione. Le richieste scartate nel registro eventi del server dei criteri di server non indicano che si è verificato un problema con il server NPS o con Azure AD Multi-Factor Authentication estensione NPS.

Per ridurre al minimo le richieste scartate, è consigliabile che i server VPN siano configurati con un timeout di almeno 60 secondi. Se necessario, o per ridurre le richieste scartate nei registri eventi, è possibile aumentare il valore di timeout del server VPN a 90 o 120 secondi.

A causa di questo comportamento del protocollo UDP, il server NPS può ricevere una richiesta duplicata e inviare un altro prompt di autenticazione a più fattori, anche dopo che l'utente ha già risposto alla richiesta iniziale. Per evitare questa condizione di intervallo, il Azure AD Multi-Factor Authentication estensione NPS continua a filtrare ed eliminare richieste duplicate per un massimo di 10 secondi dopo l'invio di una risposta riuscita al server VPN.

![Diagramma del server dei criteri di rete che continua a rimuovere le richieste duplicate dal server VPN per dieci secondi dopo la restituzione di una risposta corretta](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Anche in questo caso, è possibile che vengano rilevate richieste Eliminate nei registri eventi del server dei criteri di accesso, anche quando il Azure AD Multi-Factor Authentication prompt è stato completato correttamente. Si tratta di un comportamento previsto e non indica un problema con il server NPS o Azure AD Multi-Factor Authentication estensione NPS.

## <a name="plan-your-deployment"></a>Pianificare la distribuzione

L'estensione di Server dei criteri di rete gestisce automaticamente la ridondanza, pertanto non è necessaria una configurazione speciale.

È possibile creare tutti i server NPS Azure AD abilitati per Multi-Factor Authentication, in modo che siano necessari. Se si installano più server, è consigliabile usare un certificato client di differenza per ciascuno. La creazione di un certificato per ogni server significa che è possibile aggiornare ogni certificato singolarmente e non preoccuparsi dei tempi di inattività in tutti i server.

I server VPN instradano le richieste di autenticazione, pertanto è necessario conoscere i nuovi server dei criteri di rete abilitati per Multi-Factor Authentication Azure AD.

## <a name="prerequisites"></a>Prerequisiti

L'estensione di Server dei criteri di rete è progettata per funzionare con l'infrastruttura esistente. Prima di iniziare, verificare che i prerequisiti seguenti siano disponibili.

### <a name="licenses"></a>Licenze

L'estensione NPS per Azure AD Multi-Factor Authentication è disponibile per i clienti che dispongono di [licenze per Azure AD multi-factor authentication](multi-factor-authentication.md). Le licenze basate sul consumo per Azure AD Multi-Factor Authentication, ad esempio per utente o licenze per autenticazione, non sono compatibili con l'estensione NPS.

### <a name="software"></a>Software

Windows Server 2012 o versione successiva.

### <a name="libraries"></a>Librerie

È necessario installare manualmente la libreria seguente:

- [Visual C++ Redistributable per Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Le librerie seguenti vengono installate automaticamente con l'estensione.

- [Visual C++ Redistributable Packages per Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Modulo di Microsoft Azure Active Directory per Windows PowerShell versione 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Il Modulo di Microsoft Azure Active Directory per Windows PowerShell viene installato anche tramite uno script di configurazione eseguito come parte del processo di installazione, se non è già presente. Non è necessario installare questo modulo in anticipo, se non è già installato.

### <a name="azure-active-directory"></a>Azure Active Directory

Tutti gli utenti che usano l'estensione NPS devono essere sincronizzati con i Azure AD usando Azure AD Connect e devono essere registrati per l'autenticazione a più fattori.

Quando si installa l'estensione, sono necessari l' *ID tenant* e le credenziali di amministratore per il tenant di Azure ad. Per ottenere l'ID tenant, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale del tenant di Azure.
1. Cercare e selezionare il **Azure Active Directory**.
1. Nella pagina **Panoramica** vengono visualizzate le *informazioni sul tenant* . Accanto all' *ID tenant* selezionare l'icona di **copia** , come illustrato nella schermata di esempio seguente:

   ![Recupero dell'ID tenant dal portale di Azure](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Requisiti di rete

Il server NPS deve essere in grado di comunicare con i seguenti URL sulle porte 80 e 443:

* *https: \/ /adnotifications.WindowsAzure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /Credentials.Azure.com*

Inoltre, è necessaria la connettività agli URL seguenti per completare l' [installazione dell'adapter usando lo script di PowerShell fornito](#run-the-powershell-script):

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.NET*

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Prima di installare l'estensione NPS, preparare l'ambiente per gestire il traffico di autenticazione.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Abilitare il ruolo del Server dei criteri di rete in un server appartenente a un dominio

Il server NPS si connette a Azure AD e autentica le richieste multi-factor authentication. Scegliere un server per questo ruolo. Si consiglia di scegliere un server che non gestisce le richieste provenienti da altri servizi, poiché l'estensione di Server dei criteri di rete genera errori per qualsiasi richiesta non RADIUS. Il server NPS deve essere configurato come server di autenticazione primario e secondario per l'ambiente in uso. Non è possibile eseguire il proxy di richieste RADIUS a un altro server.

1. Nel server aprire **Server Manager**. Scegliere **Aggiungi ruoli e funzionalità guidata** dal menu *avvio rapido* .
2. Per il tipo di installazione, scegliere **installazione basata su ruoli o basata su funzionalità**.
3. Selezionare il ruolo del server **Servizi di accesso e criteri di rete**. È possibile che venga visualizzata una finestra per informare le altre funzionalità necessarie per eseguire questo ruolo.
4. Continuare la procedura guidata fino alla pagina di *conferma* . Quando si è pronti, selezionare **Installa**.

L'installazione del ruolo server NPS potrebbe richiedere alcuni minuti. Al termine, continuare con le sezioni seguenti per configurare il server in modo che gestisca le richieste RADIUS in ingresso dalla soluzione VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurare la soluzione VPN in modo che comunichi con il Server dei criteri di rete

La procedura per configurare i criteri di autenticazione RADIUS può variare a seconda della soluzione VPN in uso. Configurare i criteri VPN in modo che puntino al server dei criteri di rete RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizzare gli utenti del dominio con il cloud

Questo passaggio potrebbe essere già completato nel tenant, tuttavia è consigliabile verificare che Azure AD Connect abbia sincronizzato i database di recente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Selezionare **Azure Active Directory** > **Azure AD Connect**
3. Verificare che lo stato della sincronizzazione sia **Abilitata** e che l'ultima sincronizzazione sia stata eseguita da meno di un'ora.

Se è necessario avviare un nuovo ciclo di sincronizzazione, vedere [Azure ad Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinare i metodi di autenticazione che è possibile usare

Sono due i fattori che determinano i metodi di autenticazione disponibili con una distribuzione dell'estensione di Server dei criteri di rete:

* L'algoritmo di crittografia della password usato tra il client RADIUS (VPN, server Netscaler o altri) e i Server dei criteri di rete.
   - **PAP** supporta tutti i metodi di autenticazione di Azure ad multi-factor authentication nel cloud: telefonata, SMS unidirezionale, notifica dell'app per dispositivi mobili, token hardware del giuramento e codice di verifica dell'app per dispositivi mobili.
   - **CHAPV2** e **EAP** supportano la chiamata telefonica e la notifica dell'app per dispositivi mobili.

    > [!NOTE]
    > Quando si distribuisce l'estensione di Server dei criteri di rete, usare questi fattori per valutare i metodi disponibili per gli utenti. Se il client RADIUS supporta PAP, ma nel client non esistono campi di input per un codice di verifica, la chiamata telefonica e la notifica dell'app per dispositivi mobili sono le due opzioni supportate.
    >
    > Inoltre, indipendentemente dal protocollo di autenticazione usato (PAP, CHAP o EAP), se il metodo multi-factor authentication è basato su testo (SMS, codice di verifica dell'app per dispositivi mobili o token hardware del GIURAmento) e richiede all'utente di immettere un codice o un testo nel campo di input dell'interfaccia utente del client VPN, l'autenticazione potrebbe avere esito positivo. *Tuttavia* , qualsiasi attributo RADIUS configurato nei criteri di accesso alla rete *non* viene inviato al RADIUS ciente (il dispositivo di accesso alla rete, ad esempio il gateway VPN). Di conseguenza, il client VPN potrebbe avere un accesso maggiore di quello che si vuole avere o meno accesso.

* I metodi di input che l'applicazione client (VPN, server Netscaler o altra) può gestire. Ad esempio, gli strumenti usati dal client VPN per consentire all'utente di digitare un codice di verifica da un testo o da un'app per dispositivi mobili.

È possibile [disabilitare i metodi di autenticazione non supportati](howto-mfa-mfasettings.md#verification-methods) in Azure.

### <a name="register-users-for-mfa"></a>Registrare utenti per l'MFA

Prima di distribuire e utilizzare l'estensione NPS, gli utenti necessari per eseguire Azure AD Multi-Factor Authentication devono essere registrati per l'autenticazione a più fattori. Per testare l'estensione mentre viene distribuita, è necessario almeno un account di test registrato completamente per Azure AD Multi-Factor Authentication.

Se è necessario creare e configurare un account di prova, attenersi alla procedura seguente:

1. Accedere a [https://aka.ms/mfasetup](https://aka.ms/mfasetup) con un account di prova.
2. Seguire le richieste per configurare un metodo di verifica.
3. Nel portale di Azure come utente amministratore, [creare un criterio di accesso condizionale](howto-mfa-getstarted.md#create-conditional-access-policy) per richiedere l'autenticazione a più fattori per l'account di test.

> [!IMPORTANT]
>
> Verificare che gli utenti siano stati registrati correttamente per Azure AD Multi-Factor Authentication. Se gli utenti sono stati registrati in precedenza solo per la reimpostazione della password self-service (SSPR), *StrongAuthenticationMethods* viene abilitato per i loro account. Azure AD Multi-Factor Authentication viene applicato quando viene configurato *StrongAuthenticationMethods* , anche se l'utente è stato registrato solo per sspr.
>
> È possibile abilitare la registrazione di sicurezza combinata per configurare SSPR e Azure AD Multi-Factor Authentication contemporaneamente. Per altre informazioni, vedere [Abilitare la registrazione delle informazioni di sicurezza combinate in Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> È anche possibile [imporre agli utenti di registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-user-authentication-options) se in precedenza erano abilitati solo per la reimpostazione della password self-service.

## <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete

> [!IMPORTANT]
> Installare l'estensione di Server dei criteri di rete in un server diverso rispetto al punto di accesso della VPN.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Scaricare e installare l'estensione NPS per Azure AD autenticazione a più fattori

Per scaricare e installare l'estensione NPS, attenersi alla procedura seguente:

1. [Scaricare l'estensione di Server dei criteri di rete](https://aka.ms/npsmfa) dall'Area download di Microsoft.
1. Copiare il file binario nel Server dei criteri di rete da configurare.
1. Eseguire *setup.exe* e seguire le istruzioni di installazione. Se si verificano errori, assicurarsi che le [librerie della sezione dei prerequisiti](#libraries) siano state installate correttamente.

#### <a name="upgrade-the-nps-extension"></a>Aggiornare l'estensione di Server dei criteri di rete

Se successivamente si aggiorna un'installazione di estensione NPS esistente, per evitare il riavvio del server sottostante, completare i passaggi seguenti:

1. Disinstallare la versione esistente.
1. Eseguire il nuovo programma di installazione.
1. Riavviare il servizio *Server dei criteri di rete (IAS)* .

### <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell

Il programma di installazione crea uno script di PowerShell in `C:\Program Files\Microsoft\AzureMfa\Config` (dove `C:\` è l'unità di installazione). Questo script di PowerShell esegue le azioni seguenti ogni volta che viene eseguito:

* Crea un certificato autofirmato.
* Associa la chiave pubblica del certificato all'entità servizio in Azure AD.
* Archivia il certificato nell'archivio certificati del computer locale.
* Concede l'accesso alla chiave privata del certificato all'utente di rete.
* Riavvia il servizio Server dei criteri di rete.

A meno che non si desideri usare i propri certificati, anziché i certificati autofirmati generati dallo script di PowerShell, eseguire lo script di PowerShell per completare l'installazione dell'estensione NPS. Se si installa l'estensione su più server, ogni server deve disporre del proprio certificato.

Per fornire le funzionalità di bilanciamento del carico o per la ridondanza, ripetere questi passaggi in server dei criteri di ricerca aggiuntivi, se necessario:

1. Aprire un prompt di Windows PowerShell come amministratore.
1. Passare alla directory in cui è stato creato lo script di PowerShell per il programma di installazione:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Eseguire lo script di PowerShell creato dal programma di installazione.

   > [!IMPORTANT]
   > Per i clienti che usano i cloud 21Vianet di Azure per enti pubblici o Azure China, modificare prima i `Connect-MsolService` cmdlet nello script *AzureMfaNpsExtnConfigSetup.ps1* per includere i parametri *AzureEnvironment* per il cloud richiesto. Ad esempio, specificare *-AzureEnvironment USGovernment* o *-AzureEnvironment AzureChinaCloud*.
   >
   > Per altre informazioni, vedere [riferimento al parametro Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Quando richiesto, accedere a Azure AD come amministratore.
1. Prompt di PowerShell per l'ID tenant. Usare il GUID *ID tenant* copiato dal portale di Azure nella sezione prerequisiti.
1. Al termine dello script viene visualizzato un messaggio di operazione completata.  

Se il certificato del computer precedente è scaduto ed è stato generato un nuovo certificato, è consigliabile eliminare eventuali certificati scaduti. La presenza di certificati scaduti può causare problemi con l'avvio dell'estensione di Server dei criteri di rete.

> [!NOTE]
> Se si usano i propri certificati invece di generare certificati con lo script di PowerShell, verificare che rispettino la convenzione di denominazione di Server dei criteri di rete. Il nome del soggetto deve essere **CN = \<TenantID\> , OU = Microsoft NPS Extension**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure per enti pubblici o Azure Cina 21Vianet passaggi aggiuntivi

Per i clienti che usano Azure per enti pubblici o Azure Cina 21Vianet cloud, sono necessari i passaggi di configurazione aggiuntivi seguenti in ogni server NPS.

> [!IMPORTANT]
> Configurare queste impostazioni del registro di sistema solo se si è un cliente di Azure per enti pubblici o Azure Cina 21Vianet.

1. Se si è un cliente di Azure per enti pubblici o Azure Cina 21Vianet, aprire l' **Editor del registro di sistema** nel server NPS.
1. Accedere a `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Per i clienti di Azure per enti pubblici, impostare i valori chiave seguenti:

    | Chiave del Registro di sistema       | valore |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Per i clienti di Azure China 21Vianet, impostare i valori chiave seguenti:

    | Chiave del Registro di sistema       | valore |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Ripetere i due passaggi precedenti per impostare i valori della chiave del Registro di sistema per ogni Server dei criteri di rete.
1. Riavviare il servizio Server dei criteri di rete per ogni Server dei criteri di rete.

    Per limitare l'impatto, escludere ogni Server dei criteri di rete dalla rotazione del bilanciamento del carico uno alla volta e attendere lo svuotamento di tutte le connessioni.

### <a name="certificate-rollover"></a>Rollover dei certificati

Con la versione *1.0.1.32* dell'estensione NPS, è ora supportata la lettura di più certificati. Questa funzionalità consente di semplificare gli aggiornamenti del certificato in sequenza prima della scadenza. Se l'organizzazione esegue una versione precedente dell'estensione NPS, eseguire l'aggiornamento alla versione *1.0.1.32* o successiva.

I certificati creati dallo script `AzureMfaNpsExtnConfigSetup.ps1` sono validi per 2 anni. Monitorare i certificati per la scadenza. I certificati per l'estensione NPS vengono inseriti nell'archivio certificati del *computer locale* in *personale* e vengono *rilasciati all'* ID tenant fornito allo script di installazione.

Quando un certificato si avvicina alla data di scadenza, è necessario creare un nuovo certificato per sostituirlo.  Questo processo viene eseguito eseguendo di nuovo `AzureMfaNpsExtnConfigSetup.ps1` e mantenendo lo stesso ID tenant quando richiesto. Questo processo deve essere ripetuto in ogni Server dei criteri di rete nell'ambiente in uso.

## <a name="configure-your-nps-extension"></a>Configurare l'estensione di Server dei criteri di rete

Con l'ambiente preparato e l'estensione NPS ora installata nei server necessari, è possibile configurare l'estensione.

In questa sezione sono disponibili considerazioni e suggerimenti sulla progettazione per una corretta distribuzione dell'estensione di Server dei criteri di rete.

### <a name="configuration-limitations"></a>Limitazioni di configurazione

- L'estensione NPS per Azure AD Multi-Factor Authentication non include strumenti per la migrazione di utenti e impostazioni dal server multi-factor authentication al cloud. Per questo motivo, è consigliabile usare l'estensione per le distribuzioni nuove piuttosto che per quelle esistenti. Se si usano le estensioni in una distribuzione esistente, gli utenti dovranno ripetere il processo di registrazione per popolare i dettagli di Azure MFA nel cloud.  
- L'estensione NPS usa il nome UPN dell'ambiente di servizi di dominio Active Directory locale per identificare l'utente in Azure AD Multi-Factor Authentication per l'esecuzione dell'autenticazione secondaria. L'estensione può essere configurata in modo da usare un identificatore diverso come ID di accesso alternativo o campo di servizi di dominio Active Directory personalizzato diverso da UPN. Per altre informazioni, vedere l'articolo [Opzioni di configurazione avanzate per l'estensione NPS per Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Non tutti i protocolli di crittografia supportano tutti i metodi di verifica.
   - **PAP** supporta la chiamata telefonica, gli SMS unidirezionali, la notifica dell'app per dispositivi mobili e il codice di verifica app per dispositivi mobili
   - **CHAPV2** e **EAP** supportano la chiamata telefonica e la notifica dell'app per dispositivi mobili

### <a name="control-radius-clients-that-require-mfa"></a>Client RADIUS di controllo che richiedono MFA

Dopo aver abilitato l'autenticazione a più fattori per un client RADIUS usando l'estensione NPS, tutte le autenticazioni per questo client sono necessarie per eseguire l'autenticazione a più fattori. Se si desidera abilitare MFA solo per alcuni client RADIUS, è possibile configurare due server di Server dei criteri di rete e installare l'estensione solo su uno di questi.

Configurare i client RADIUS per cui si vuole fare in modo che MFA invii richieste al server di Server dei criteri di rete configurato con l'estensione e gli altri client RADIUS al server di Server dei criteri di rete senza configurazione per l'estensione.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Impostazioni per gli utenti che non sono registrati per MFA

Se sono presenti utenti che non sono registrati per MFA, è possibile stabilire cosa succede quando questi tentano di eseguire l'autenticazione. Per controllare questo comportamento, usare l'impostazione *REQUIRE_USER_MATCH* nel percorso del registro di sistema *HKLM\Software\Microsoft\AzureMFA*. Questa impostazione non ha un'unica opzione di configurazione:

| Chiave | valore | Predefinito |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERO/FALSO | Non impostato (equivalente a VERO) |

Questa impostazione determina le operazioni da eseguire quando un utente non è registrato per l'autenticazione a più fattori. Quando la chiave non esiste, non è impostata o è impostata su *true* e l'utente non è registrato, l'estensione non supera la richiesta di autenticazione a più fattori.

Quando la chiave è impostata su *false* e l'utente non è registrato, l'autenticazione continua senza eseguire l'autenticazione a più fattori. Se un utente è registrato in multi-factor authentication, deve autenticarsi con l'autenticazione a più fattori anche se *REQUIRE_USER_MATCH* è impostato su *false*.

È possibile scegliere di creare questa chiave e impostarla su *false* mentre gli utenti sono in fase di onboarding e potrebbe non essere ancora stata registrata per Azure ad multi-factor authentication. Poiché l'impostazione della chiave consente agli utenti che non sono registrati all'MFA di accedere, è necessario rimuovere la chiave prima di passare all'ambiente di produzione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="nps-extension-health-check-script"></a>Script di controllo integrità dell'estensione Server dei criteri di rete

Lo script seguente è disponibile per eseguire i passaggi di base del controllo integrità durante la risoluzione dei problemi relativi all'estensione Server dei criteri di rete.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Come verificare che il certificato client sia installato come previsto?

Cercare il certificato autofirmato creato dal programma di installazione nell'archivio dei certificati e verificare che la chiave privata disponga delle autorizzazioni concesse all'utente *Servizio di rete*. Il certificato ha un nome soggetto di **CN \<tenantid\> , ou = estensione NPS Microsoft**

I certificati autofirmati generati dallo `AzureMfaNpsExtnConfigSetup.ps1` script hanno una durata di validità di due anni. Quando si verifica che il certificato sia installato, è necessario verificare anche che il certificato non sia scaduto.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Come è possibile verificare che il certificato client sia associato al tenant in Azure AD?

Aprire il prompt dei comandi di PowerShell ed eseguire i comandi seguenti:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Questi comandi consentono di stampare tutti i certificati associando il tenant con l'istanza dell'estensione di Server dei criteri di rete nella sessione di PowerShell. Cercare il certificato esportando il certificato client come file con estensione *CER X. 509 con codifica Base 64* senza la chiave privata e confrontarlo con l'elenco di PowerShell.

Il seguente comando creerà un file denominato *npscertificate* nella radice dell'unità *C:* in format *. cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Dopo aver eseguito questo comando, passare alla radice dell'unità *C:* , individuare il file e fare doppio clic su di esso. Passare a dettagli e scorrere verso il basso fino a "identificazione personale". Confrontare l'identificazione personale del certificato installato nel server con questo. Le identificazioni personali devono corrispondere.

I timestamp *validi da* e *validi fino* a, che sono in formato leggibile, possono essere usati per filtrare i disadattati evidenti se il comando restituisce più di un certificato.

### <a name="why-cannot-i-sign-in"></a>Perché non è possibile accedere?

Verificare che la password non sia scaduta. L'estensione NPS non supporta la modifica delle password come parte del flusso di lavoro di accesso. Contattare il personale IT dell'organizzazione per ricevere assistenza.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Perché le richieste hanno esito negativo con errore di token ADAL?

Questo errore potrebbe essere dovuto a diverse ragioni. Per risolvere i problemi, attenersi alla procedura seguente:

1. Riavviare il server di Server dei criteri di rete.
2. Verificare che il certificato client sia installato come previsto.
3. Verificare che il certificato sia associato al tenant in Azure AD.
4. Verificare che `https://login.microsoftonline.com/` sia accessibile dal server che esegue l'estensione.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Perché l'autenticazione ha esito negativo e restituisce un errore nei log HTTP che indica che l'utente non è stato trovato?

Verificare che AD Connect sia in esecuzione e che l'utente sia presente sia nell'ambiente di servizi di dominio Active Directory locale sia in Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Perché vengono visualizzati errori di connessione HTTP nei log che contengono le autenticazioni non riuscite?

Verificare che https://adnotifications.windowsazure.com sia raggiungibile dal server che esegue l'estensione del server dei criteri di rete.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Perché l'autenticazione non funziona, nonostante sia presente un certificato valido?

Se il certificato del computer precedente è scaduto ed è stato generato un nuovo certificato, eliminare eventuali certificati scaduti. I certificati scaduti possono causare problemi con l'avvio dell'estensione NPS.

Per verificare se si dispone di un certificato valido, controllare l' *archivio certificati dell'account computer* locale utilizzando MMC e verificare che il certificato non abbia superato la data di scadenza. Per generare un certificato appena valido, ripetere i passaggi da [eseguire lo script del programma di installazione di PowerShell](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Perché vengono visualizzate le richieste scartate nei log del server NPS?

Un server VPN può inviare richieste ripetute al server dei criteri di rete se il valore di timeout è troppo basso. Il server NPS rileva queste richieste duplicate e le ignora. Questo comportamento è dovuto alla progettazione e non indica un problema con il server NPS o con Azure AD Multi-Factor Authentication estensione NPS.

Per ulteriori informazioni sui motivi per cui è possibile visualizzare i pacchetti scartati nei log del server NPS, vedere il [comportamento del protocollo RADIUS e l'estensione NPS](#radius-protocol-behavior-and-the-nps-extension) all'inizio di questo articolo.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gestione dei protocolli TLS/SSL e dei pacchetti di crittografia

È consigliabile disabilitare o rimuovere i pacchetti di crittografia precedenti e più vulnerabili, a meno che non siano richiesti dall'organizzazione. Per informazioni su come completare questa attività, vedere l'articolo [gestione dei protocolli SSL/TLS e dei pacchetti di crittografia per ad FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Risoluzione dei problemi aggiuntiva

Ulteriori indicazioni sulla risoluzione dei problemi e le possibili soluzioni sono disponibili nell'articolo [risolvere i messaggi di errore dall'estensione NPS per Azure AD multi-factor authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica e configurazione di Server dei criteri di rete in Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Configurare gli ID alternativi per l'accesso o impostare un elenco di eccezioni per gli indirizzi IP che non devono eseguire la verifica in due passaggi in [Advanced configuration options for the NPS extension for Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md) (Opzioni di configurazione avanzate per l'estensione del server dei criteri di rete per Multi-Factor Authentication).

- Informazioni su come integrare [Gateway Desktop remoto](howto-mfa-nps-extension-rdg.md) e [server VPN](howto-mfa-nps-extension-vpn.md) usando l'estensione NPS

- [Risolvere i messaggi di errore dall'estensione NPS per Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
