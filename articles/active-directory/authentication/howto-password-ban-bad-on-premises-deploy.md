---
title: Distribuire la protezione Azure AD password locale
description: Informazioni su come pianificare e distribuire Azure AD la protezione delle password in un ambiente Active Directory Domain Services locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66df1bbe531c072ff5aa2bebe7b197201e6931a2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077728"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Pianificare e distribuire la protezione Azure Active Directory password locale

Gli utenti creano spesso password che usano parole locali comuni, ad esempio il nome di una scuola, una squadra sportiva o un personaggio famoso. Queste password sono facili da indovinare e vulnerabili ad attacchi basati su dizionario. Per applicare password complesse nell'organizzazione, la protezione con password Azure Active Directory (Azure AD) fornisce un elenco di password vietate globali e personalizzate. Una richiesta di modifica della password ha esito negativo se è presente una corrispondenza nell'elenco delle password escluse.

Per proteggere l'ambiente di Active Directory Domain Services locale (AD DS), è possibile installare e configurare Azure AD la protezione delle password per l'uso con il controller di dominio locale. Questo articolo illustra come installare e registrare il servizio proxy Azure AD Password Protection e Azure AD agente di controller di dominio per la protezione delle password nell'ambiente locale.

Per ulteriori informazioni sul funzionamento di Azure AD la protezione delle password in un ambiente locale, vedere [come applicare Azure ad la protezione con password per Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategia di distribuzione

Il diagramma seguente illustra il modo in cui i componenti di base di Azure AD la protezione delle password interagiscono in un ambiente Active Directory locale:

![Come interagiscono i componenti di Protezione password di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

È consigliabile esaminare il funzionamento del software prima di distribuirlo. Per ulteriori informazioni, vedere [panoramica concettuale di Azure ad la protezione con password](concept-password-ban-bad-on-premises.md).

Si consiglia di avviare le distribuzioni in modalità di *controllo* . La modalità di controllo è l'impostazione iniziale predefinita, in cui è possibile continuare a impostare le password. Le password che verrebbero bloccate vengono registrate nel registro eventi. Dopo aver distribuito i server proxy e gli agenti del controller di dominio in modalità di controllo, monitorare l'effetto che i criteri password avranno sugli utenti quando vengono applicati i criteri.

Durante la fase di controllo, molte organizzazioni riscontrano che si applicano le situazioni seguenti:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Spesso gli utenti usano password non sicure.
* Devono informare gli utenti sulla modifica imminente dell'applicazione della sicurezza, sulla possibile incidenza su di essi e su come scegliere password più sicure.

È anche possibile che la convalida delle password più avanzata influisca sull'automazione della distribuzione del controller di dominio Active Directory esistente. Per individuare tali problemi, è consigliabile eseguire almeno una promozione del controller di dominio e un'abbassamento di livello del controller di dominio durante la valutazione del periodo di controllo. Per altre informazioni, vedere gli articoli seguenti:

* [Ntdsutil.exe non è in grado di impostare una password per la modalità di ripristino di servizi directory debole](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [La promozione della replica del controller di dominio non riesce a causa di una password della modalità ripristino servizi directory debole](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [L'abbassamento di livello del controller di dominio non riesce a causa di una password amministratore locale](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Dopo che la funzionalità è stata eseguita in modalità di controllo per un periodo di tempo ragionevole, è possibile cambiare la configurazione da *controllo* a *Imponi* per richiedere password più sicure. Il monitoraggio aggiuntivo durante questo periodo di tempo è una scelta efficace.

È importante notare che Azure AD Password Protection può convalidare le password solo durante le operazioni di modifica o impostazione della password. Le password accettate e archiviate in Active Directory prima della distribuzione di Azure AD la protezione con password non verranno mai convalidate e continueranno a funzionare così come sono. Nel corso del tempo, tutti gli utenti e gli account inizieranno a usare Azure AD password convalidate per la protezione delle password perché le password esistenti scadono normalmente. Gli account configurati con "Nessuna scadenza password" sono esenti da questa.

### <a name="multiple-forest-considerations"></a>Considerazioni su più foreste

Non sono previsti requisiti aggiuntivi per distribuire Protezione password di Azure AD in più foreste.

Ogni foresta è configurata in modo indipendente, come descritto nella sezione seguente per [distribuire la protezione Azure ad password](#download-required-software)locale. Ogni proxy di Azure AD Password Protection può supportare solo i controller di dominio della foresta a cui è aggiunto.

Il software di protezione Azure AD password in qualsiasi foresta non è a conoscenza del software di protezione delle password distribuito in altre foreste, indipendentemente dalle configurazioni di Active Directory trust.

### <a name="read-only-domain-controller-considerations"></a>Considerazioni sul controller di dominio di sola lettura

Gli eventi di modifica o di impostazione della password non vengono elaborati e resi permanente nei controller di dominio di sola lettura (RODC). Vengono invece trasmessi ai controller di dominio scrivibili. Non è necessario installare il software dell'agente del controller di dominio Azure AD Password Protection in RODC.

Non è inoltre supportata l'esecuzione del servizio proxy Azure AD Password Protection in un controller di dominio di sola lettura.

### <a name="high-availability-considerations"></a>Considerazioni sulla disponibilità elevata

La preoccupazione principale per la protezione delle password è la disponibilità di Azure AD server proxy di protezione delle password quando i controller di dominio in una foresta tentano di scaricare nuovi criteri o altri dati da Azure. Ogni agente del controller di dominio Azure AD Password Protection usa un semplice algoritmo di tipo Round Robin per decidere quale server proxy chiamare. L'agente ignora i server proxy che non rispondono.

Per la maggior parte delle distribuzioni di Active Directory completamente connesse con una replica corretta dello stato della cartella e della cartella SYSVOL, due Azure AD server proxy di protezione delle password sono sufficienti per garantire la disponibilità. Questa configurazione comporta il download tempestivo dei nuovi criteri e di altri dati. Se lo si desidera, è possibile distribuire server proxy aggiuntivi Azure AD password protection.

La progettazione del software dell'agente del controller di dominio Azure AD password protection attenua i normali problemi associati alla disponibilità elevata. L'agente del controller di dominio Azure AD Password Protection mantiene una cache locale dei criteri password scaricati più di recente. Anche se tutti i server proxy registrati diventano non disponibili, gli agenti del controller di dominio Azure AD password protection continuano a applicare i criteri password memorizzati nella cache.

Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere di giorni, non di ore o meno. Quindi, le brevi interruzioni dei server proxy non influiscono in modo significativo Azure AD la protezione delle password.

## <a name="deployment-requirements"></a>Requisiti per la distribuzione

Per informazioni sulle licenze, vedere [Azure ad requisiti di licenza](concept-password-ban-bad.md#license-requirements)per la protezione delle password.

Si applicano i requisiti di base seguenti:

* Per tutti i computer, inclusi i controller di dominio, in cui sono installati Azure AD componenti di protezione delle password è necessario che sia installato il runtime di C universale.
    * È possibile ottenere il runtime assicurandosi di avere tutti gli aggiornamenti da Windows Update. In alternativa, è possibile ottenerlo in un pacchetto di aggiornamento specifico del sistema operativo. Per ulteriori informazioni, vedere [aggiornamento per Universal C Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* È necessario un account con Active Directory privilegi di amministratore di dominio nel dominio radice della foresta per registrare la foresta di Windows Server Active Directory con Azure AD.
* Il servizio di distribuzione delle chiavi deve essere abilitato in tutti i controller di dominio nel dominio che esegue Windows Server 2012. Per impostazione predefinita, questo servizio viene abilitato tramite l'avvio manuale del trigger.
* La connettività di rete deve esistere tra almeno un controller di dominio in ogni dominio e almeno un server che ospita il servizio proxy per la protezione Azure AD password. Questa connettività deve consentire al controller di dominio di accedere alla porta di mapping degli endpoint RPC 135 e alla porta del server RPC nel servizio proxy.
    * Per impostazione predefinita, la porta del server RPC è una porta RPC dinamica, ma può essere configurata per l' [utilizzo di una porta statica](#static).
* Tutti i computer in cui verrà installato il servizio proxy Azure AD password protection dovranno avere accesso di rete agli endpoint seguenti:

    |**Endpoint**|**Scopo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Richieste di autenticazione|
    |`https://enterpriseregistration.windows.net`|Funzionalità di Protezione password di Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente di controller di dominio Azure AD Password Protection

I requisiti seguenti si applicano all'agente di controller di dominio Azure AD password protection:

* In tutti i computer in cui verrà installato il software dell'agente di Azure AD password protection controller di dominio deve essere eseguito Windows Server 2012 o versione successiva.
    * Il dominio o la foresta Active Directory non deve trovarsi nel livello di funzionalità del dominio di Windows Server 2012 (DFL) o nel livello di funzionalità della foresta (FFL). Come indicato nei [principi di progettazione](concept-password-ban-bad-on-premises.md#design-principles), non è necessario alcun valore minimo di DFL o FFL per l'esecuzione del software proxy o dell'agente DC.
* Per tutti i computer in cui è in esecuzione Azure AD Password Protection Agent è necessario che sia installato .NET 4,5.
* Qualsiasi Active Directory dominio in cui è in esecuzione il servizio agente di Azure AD password protection controller di dominio deve utilizzare la replica file system distribuito (DFSR) per la replica SYSVOL.
   * Se il dominio non usa già DFSR, è necessario eseguire la migrazione prima di installare Azure AD la protezione delle password. Per ulteriori informazioni, vedere [Guida alla migrazione della replica SYSVOL: FRS a replica DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Il software dell'agente del controller di dominio Azure AD Password Protection verrà attualmente installato nei controller di dominio in domini che usano ancora FRS (la tecnologia predecessore per DFSR) per la replica SYSVOL, ma il software non funzionerà correttamente in questo ambiente.
    >
    > Gli effetti collaterali negativi aggiuntivi includono i singoli file che non riescono a eseguire la replica e le procedure di ripristino di SYSVOL sembrano avere esito positivo ma non riescono a replicare tutti i file.
    >
    > Eseguire la migrazione del dominio per usare DFSR il prima possibile, sia per i vantaggi intrinseci di DFSR che per sbloccare la distribuzione di Azure AD la protezione delle password. Le versioni future del software verranno disabilitate automaticamente durante l'esecuzione in un dominio che sta ancora usando FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Servizio proxy Azure AD Password Protection

I requisiti seguenti si applicano al servizio proxy Azure AD password protection:

* Tutti i computer in cui verrà installato il servizio proxy Azure AD Password Protection devono eseguire Windows Server 2012 R2 o versione successiva.

    > [!NOTE]
    > La distribuzione del servizio proxy Azure AD Password Protection è un requisito obbligatorio per la distribuzione di Azure AD la protezione con password anche se il controller di dominio potrebbe avere connettività Internet diretta in uscita.

* In tutti i computer in cui verrà installato il servizio proxy Azure AD Password Protection deve essere installato .NET 4,7.
    * .NET 4,7 dovrebbe essere già installato in un server Windows completamente aggiornato. Se necessario, scaricare ed eseguire il programma di installazione disponibile nel [programma di installazione di .NET Framework 4,7 offline per Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Tutti i computer che ospitano il servizio proxy Azure AD Password Protection devono essere configurati in modo da concedere ai controller di dominio la possibilità di accedere al servizio proxy. Questa possibilità viene controllata tramite l'assegnazione dei privilegi "accedi al computer dalla rete".
* Tutti i computer che ospitano il servizio proxy Azure AD Password Protection devono essere configurati per consentire il traffico HTTP di TLS 1,2 in uscita.
* Un account amministratore *globale* o *amministratore della sicurezza* per registrare il servizio proxy Azure ad Password Protection e la foresta con Azure ad.
* L'accesso alla rete deve essere abilitato per il set di porte e URL specificati nelle [procedure di configurazione dell'ambiente del proxy di applicazione](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Prerequisiti di Microsoft Azure AD Connect Agent Updater

Il servizio Microsoft Azure AD Connect Agent Updater è installato side-by-side con il servizio proxy Azure AD password protection. È necessaria una configurazione aggiuntiva per consentire al servizio Microsoft Azure AD Connect Agent Updater di funzionare:

* Se l'ambiente usa un server proxy HTTP, seguire le linee guida specificate in [usare i server proxy locali esistenti](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* Il servizio di aggiornamento dell'agente di Microsoft Azure AD Connect richiede anche la procedura TLS 1,2 specificata nei [requisiti TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Azure AD proxy di protezione delle password e Azure AD proxy di applicazione installano versioni diverse del servizio Microsoft Azure AD Connect Agent Updater, motivo per cui le istruzioni fanno riferimento al contenuto del proxy di applicazione. Queste versioni diverse sono incompatibili quando vengono installate side-by-side e questa operazione impedirà al servizio Agent Updater di contattare Azure per gli aggiornamenti software, pertanto è consigliabile non installare mai Azure AD proxy e proxy di applicazione della protezione delle password nello stesso computer.

## <a name="download-required-software"></a>Scaricare il software necessario

Sono disponibili due programmi di installazione necessari per una distribuzione di protezione Azure AD password locale:

* Agente di controller di dominio Azure AD Password Protection ( *AzureADPasswordProtectionDCAgentSetup.msi* )
* Proxy di protezione Azure AD password ( *AzureADPasswordProtectionProxySetup.exe* )

Scaricare entrambi i programmi di installazione dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Installare e configurare il servizio proxy

Il servizio proxy Azure AD Password Protection si trova in genere in un server membro dell'ambiente di servizi di dominio Active Directory locale. Una volta installato, il servizio proxy Azure AD password protection comunica con Azure AD per mantenere una copia degli elenchi di password globali e dei clienti esclusi per il tenant di Azure AD.

Nella sezione successiva si installeranno gli agenti di controller di dominio Azure AD Password Protection nei controller di dominio nell'ambiente di servizi di dominio Active Directory locale. Questi agenti di controller di dominio comunicano con il servizio proxy per ottenere gli elenchi delle password vietate più recenti da usare durante l'elaborazione degli eventi di modifica della password nel dominio.

Scegliere uno o più server per ospitare il servizio proxy Azure AD password protection. Per i server sono valide le considerazioni seguenti:

* Ogni servizio di questo tipo può fornire solo criteri password per una singola foresta. Il computer host deve essere aggiunto a qualsiasi dominio nella foresta.
* Supporta l'installazione del proxy del servizio nei domini radice o figlio o in una combinazione di questi.
* È necessaria la connettività di rete tra almeno un controller di dominio in ogni dominio della foresta e un server proxy per la protezione delle password.
* È possibile eseguire il Azure AD servizio proxy di protezione da password in un controller di dominio per il testing, ma tale controller di dominio richiede la connettività Internet. Questa connettività può costituire un problema di sicurezza. Questa configurazione è consigliata solo per i test.
* Per la ridondanza sono consigliate almeno due Azure AD server proxy per la protezione con password, come indicato nella sezione precedente relativa alle [considerazioni sulla disponibilità elevata](#high-availability-considerations).
* Non è supportata l'esecuzione del servizio proxy Azure AD Password Protection in un controller di dominio di sola lettura.

Per installare il servizio proxy Azure AD Password Protection, attenersi alla procedura seguente:

1. Per installare il servizio proxy Azure AD Password Protection, eseguire il `AzureADPasswordProtectionProxySetup.exe` programma di installazione del software.

    L'installazione del software non richiede un riavvio e può essere automatizzata usando le procedure MSI standard, come nell'esempio seguente:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > È necessario che il servizio Windows Firewall sia in esecuzione prima `AzureADPasswordProtectionProxySetup.exe` di installare il pacchetto per evitare errori di installazione.
    >
    > Se Windows Firewall è configurato per non essere eseguito, la soluzione alternativa consiste nell'abilitare ed eseguire temporaneamente il servizio firewall durante l'installazione. Il software proxy non ha dipendenze specifiche da Windows Firewall dopo l'installazione.
    >
    > Se si usa un firewall di terze parti, è comunque necessario configurarlo per soddisfare i requisiti di distribuzione. Che includono l'accesso in ingresso alla porta 135 e la porta del server proxy RPC. Per ulteriori informazioni, vedere la sezione precedente relativa [ai requisiti di distribuzione](#deployment-requirements).

1. Il software proxy di Azure AD Password Protection include un nuovo modulo di PowerShell, `AzureADPasswordProtection` . I passaggi seguenti eseguono diversi cmdlet da questo modulo di PowerShell.

    Per usare questo modulo, aprire una finestra di PowerShell come amministratore e importare il nuovo modulo come segue:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Per verificare che il servizio proxy Azure AD Password Protection sia in esecuzione, usare il comando PowerShell seguente:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Il risultato dovrebbe mostrare **lo stato** *in esecuzione* .

1. Il servizio proxy è in esecuzione nel computer, ma non ha le credenziali per comunicare con Azure AD. Registrare il server proxy Azure AD Password Protection con Azure AD utilizzando il `Register-AzureADPasswordProtectionProxy` cmdlet.

    Questo cmdlet richiede le credenziali di amministratore *globale* o di *amministratore della sicurezza* per il tenant di Azure. Questo cmdlet deve essere eseguito anche usando un account con privilegi di amministratore locale.

    Quando il comando ha esito positivo una volta per un servizio proxy Azure AD Password Protection, le chiamate aggiuntive hanno esito positivo, ma non sono necessarie.

    Il `Register-AzureADPasswordProtectionProxy` cmdlet supporta le tre modalità di autenticazione seguenti. Le prime due modalità supportano Azure Multi-Factor Authentication ma la terza modalità non lo è.

    > [!TIP]
    > È possibile che si verifichi un ritardo notevole prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi server core. Usare invece una delle modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe avere esito negativo se è abilitata la configurazione sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare il proxy e quindi riabilitarlo.

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando richiesto, seguire il collegamento per aprire un Web browser e immettere il codice di autenticazione.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità ha esito negativo se per l'account è necessario Azure Multi-Factor Authentication. In tal caso, usare una delle due modalità di autenticazione precedenti o usare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È anche possibile vedere l'autenticazione a più fattori necessaria se la registrazione del dispositivo di Azure, che viene usata dietro le quinte da Azure AD la protezione delle password, è stata configurata in modo da richiedere a livello globale Per ovviare a questo requisito, è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è possibile anche disattivare temporaneamente il requisito di autenticazione a più fattori di registrazione dei dispositivi di Azure.
        >
        > Per apportare questa modifica, cercare e selezionare **Azure Active Directory** nel portale di Azure, quindi selezionare **dispositivi > impostazioni dispositivo** . Impostare **Richiedi autenticazione a più fattori per aggiungere dispositivi** a *No* . Assicurarsi di riconfigurare nuovamente questa impostazione su *Sì* al termine della registrazione.
        >
        > È consigliabile ignorare i requisiti dell'autenticazione a più fattori solo a scopo di test.

    Attualmente non è necessario specificare il parametro *-ForestCredential* , riservato per le funzionalità future.

    La registrazione del servizio proxy Azure AD Password Protection è necessaria solo una volta nel corso della durata del servizio. Successivamente, il servizio proxy di protezione Azure AD password eseguirà automaticamente qualsiasi altra manutenzione necessaria.

1. Registrare ora la foresta Active Directory locale con le credenziali necessarie per comunicare con Azure tramite il `Register-AzureADPasswordProtectionForest` cmdlet di PowerShell.

    > [!NOTE]
    > Se nel proprio ambiente sono installati più server proxy di Azure AD Password Protection, non importa quale server proxy usare per registrare la foresta.

    Il cmdlet richiede le credenziali di amministratore *globale* o di *amministratore della sicurezza* per il tenant di Azure. Richiede anche privilegi di amministratore dell'organizzazione Active Directory locale. È inoltre necessario eseguire questo cmdlet utilizzando un account con privilegi di amministratore locale. L'account Azure usato per registrare la foresta può essere diverso dall'account Active Directory locale.
    
    Questo passaggio viene eseguito una volta per ogni foresta.

    Il `Register-AzureADPasswordProtectionForest` cmdlet supporta le tre modalità di autenticazione seguenti. Le prime due modalità supportano Azure Multi-Factor Authentication ma la terza modalità non lo è.

    > [!TIP]
    > È possibile che si verifichi un ritardo notevole prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi server core. Usare invece una delle due modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe avere esito negativo se è abilitata la configurazione sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare la foresta e quindi riabilitarla.  

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando richiesto, seguire il collegamento per aprire un Web browser e immettere il codice di autenticazione.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità ha esito negativo se per l'account è necessario Azure Multi-Factor Authentication. In tal caso, usare una delle due modalità di autenticazione precedenti o usare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È anche possibile vedere l'autenticazione a più fattori necessaria se la registrazione del dispositivo di Azure, che viene usata dietro le quinte da Azure AD la protezione delle password, è stata configurata in modo da richiedere a livello globale Per ovviare a questo requisito, è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è possibile anche disattivare temporaneamente il requisito di autenticazione a più fattori di registrazione dei dispositivi di Azure.
        >
        > Per apportare questa modifica, cercare e selezionare **Azure Active Directory** nel portale di Azure, quindi selezionare **dispositivi > impostazioni dispositivo** . Impostare **Richiedi autenticazione a più fattori per aggiungere dispositivi** a *No* . Assicurarsi di riconfigurare nuovamente questa impostazione su *Sì* al termine della registrazione.
        >
        > È consigliabile ignorare i requisiti dell'autenticazione a più fattori solo a scopo di test.

       Questi esempi hanno esito positivo solo se l'utente attualmente connesso è anche un amministratore di dominio Active Directory per il dominio radice. In caso contrario, è possibile specificare credenziali di dominio alternative tramite il parametro *-ForestCredential* .

    La registrazione della foresta Active Directory è necessaria solo una volta nella durata della foresta. Successivamente, gli agenti del controller di dominio Azure AD Password Protection nella foresta eseguono automaticamente qualsiasi altra manutenzione necessaria. Dopo `Register-AzureADPasswordProtectionForest` l'esecuzione corretta di una foresta, le chiamate aggiuntive del cmdlet hanno esito positivo, ma non sono necessarie.
    
    Per `Register-AzureADPasswordProtectionForest` avere esito positivo, è necessario che almeno un controller di dominio che esegue Windows server 2012 o versione successiva sia disponibile nel dominio del server proxy Azure ad Password Protection. Prima di questo passaggio non è necessario installare il software dell'agente del controller di dominio Azure AD password protection.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configurare il servizio proxy per la comunicazione tramite un proxy HTTP

Se l'ambiente richiede l'uso di un proxy HTTP specifico per comunicare con Azure, attenersi alla procedura seguente per configurare il servizio Azure AD password protection.

Creare un file di *AzureADPasswordProtectionProxy.exe.config* nella `%ProgramFiles%\Azure AD Password Protection Proxy\Service` cartella. Includere il contenuto seguente:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Se il proxy HTTP richiede l'autenticazione, aggiungere il tag *UseDefaultCredentials* :

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

In entrambi i casi, sostituire `http://yourhttpproxy.com:8080` con l'indirizzo e la porta del server proxy HTTP specifico.

Se il proxy HTTP è configurato per l'utilizzo di un criterio di autorizzazione, è necessario concedere l'accesso all'account del computer Active Directory del computer che ospita il servizio proxy per la protezione con password.

Si consiglia di arrestare e riavviare il servizio proxy Azure AD Password Protection dopo aver creato o aggiornato il file di *AzureADPasswordProtectionProxy.exe.config* .

Il servizio proxy non supporta l'utilizzo di credenziali specifiche per la connessione a un proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configurare il servizio proxy per l'ascolto su una porta specifica

Il software dell'agente del controller di dominio Azure AD Password Protection utilizza RPC su TCP per comunicare con il servizio proxy. Per impostazione predefinita, il servizio proxy Azure AD Password Protection è in ascolto su qualsiasi endpoint RPC dinamico disponibile. È possibile configurare il servizio in modo che sia in ascolto su una porta TCP specifica, se necessario, a causa della topologia di rete o dei requisiti del firewall nell'ambiente in uso.

<a id="static" /></a>Per configurare il servizio per l'esecuzione in una porta statica, utilizzare il `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet come segue:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Per rendere effettive le modifiche, è necessario arrestare e riavviare il servizio proxy Azure AD password protection.

Per configurare il servizio per l'esecuzione in una porta dinamica, utilizzare la stessa procedura, ma impostare *StaticPort* di nuovo su zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Per rendere effettive le modifiche, è necessario arrestare e riavviare il servizio proxy Azure AD password protection.

Il servizio proxy Azure AD Password Protection richiede un riavvio manuale dopo qualsiasi modifica nella configurazione della porta. Dopo aver apportato queste modifiche alla configurazione, non è necessario riavviare il servizio Azure AD Password Protection Agent nei controller di dominio.

Per eseguire una query per la configurazione corrente del servizio, usare il `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet come illustrato nell'esempio seguente

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Nell'output di esempio seguente viene illustrato che il servizio proxy Azure AD Password Protection utilizza una porta dinamica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Installare il servizio agente di controller di dominio

Per installare il servizio Azure AD password protection DC Agent, eseguire il `AzureADPasswordProtectionDCAgentSetup.msi` pacchetto.

È possibile automatizzare l'installazione del software usando le procedure MSI standard, come illustrato nell'esempio seguente:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Il `/norestart` flag può essere omesso se si preferisce che il programma di installazione riavvii automaticamente il computer.

Per l'installazione o la disinstallazione del software è necessario riavviare. Questo requisito è dovuto al fatto che le dll del filtro password vengono caricate o scaricate solo da un riavvio.

L'installazione di Azure AD la protezione delle password locale è completa dopo l'installazione del software dell'agente controller di dominio in un controller di dominio e il riavvio del computer. Non è necessario né possibile eseguire altre configurazioni. Gli eventi di modifica della password nei controller di dominio locali usano gli elenchi di password vietate configurate da Azure AD.

Per abilitare la protezione Azure AD password locale dall'portale di Azure o configurare password vietate personalizzate, vedere [enable on-premises Azure ad Password Protection](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> È possibile installare l'agente del controller di dominio Azure AD Password Protection in un computer che non è ancora un controller di dominio. In questo caso, il servizio viene avviato ed eseguito, ma rimane inattivo fino a quando il computer non viene promosso a controller di dominio.

## <a name="upgrading-the-proxy-service"></a>Aggiornamento del servizio proxy

Il servizio proxy Azure AD Password Protection supporta l'aggiornamento automatico. L'aggiornamento automatico usa il servizio Microsoft Azure AD Connect Agent Updater, installato side-by-side con il servizio proxy. L'aggiornamento automatico è attivato per impostazione predefinita e può essere abilitato o disabilitato usando il `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

È possibile eseguire query sull'impostazione corrente usando il `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. È consigliabile che l'impostazione di aggiornamento automatico sia sempre abilitata.

Il `Get-AzureADPasswordProtectionProxy` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti i server proxy di protezione delle Password Azure ad attualmente installati in una foresta.

### <a name="manual-upgrade-process"></a>Processo di aggiornamento manuale

Viene eseguito un aggiornamento manuale eseguendo la versione più recente del `AzureADPasswordProtectionProxySetup.exe` programma di installazione software. La versione più recente del software è disponibile nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del servizio proxy Azure AD password protection: il programma di installazione esegue un aggiornamento sul posto. Quando si aggiorna il servizio proxy, non è necessario riavviare il computer. È possibile automatizzare l'aggiornamento del software utilizzando le procedure MSI standard, ad esempio `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Aggiornamento dell'agente del controller di dominio

Quando è disponibile una versione più recente del software dell'agente del controller di dominio Azure AD Password Protection, l'aggiornamento viene eseguito eseguendo la versione più recente del `AzureADPasswordProtectionDCAgentSetup.msi` pacchetto software. La versione più recente del software è disponibile nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del software dell'agente di controller di dominio: il programma di installazione esegue un aggiornamento sul posto. Quando si aggiorna il software dell'agente controller di dominio, è sempre necessario riavviare il sistema. questo requisito è causato dal comportamento principale di Windows.

È possibile automatizzare l'aggiornamento del software utilizzando le procedure MSI standard, ad esempio `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

È possibile omettere il `/norestart` flag se si preferisce che il programma di installazione riavvii automaticamente il computer.

Il `Get-AzureADPasswordProtectionDCAgent` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti gli agenti di controller di dominio di protezione delle password attualmente Azure ad installati in una foresta.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati installati i servizi necessari per Azure AD la protezione con password nei server locali, [abilitare la protezione Azure ad password locale nel portale di Azure](howto-password-ban-bad-on-premises-operations.md) per completare la distribuzione.