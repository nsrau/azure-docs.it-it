---
title: Distribuire Protezione con password di Azure AD localeDeploy on-premises Azure AD Password Protection
description: Informazioni su come pianificare e distribuire Azure AD Password Protection in un ambiente di Servizi di dominio Active Directory locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671681"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Pianificare e distribuire Azure Active Directory Password Protection locale

Gli utenti spesso creano password che utilizzano parole locali comuni, ad esempio una scuola, una squadra sportiva o una persona famosa. Queste password sono facili da indovinare e deboli contro gli attacchi basati sul dizionario. Per applicare password complesse nell'organizzazione, Azure Active Directory (Azure AD) Password Protection fornisce un elenco di password non crittografate globali e personalizzate. Una richiesta di modifica della password ha esito negativo se è presente una corrispondenza in questi elenco di password non soddisfatte.

Per proteggere l'ambiente Servizi di dominio Active Directory locale, è possibile installare e configurare Azure AD Password Protection per l'utilizzo con il controller di dominio locale. Questo articolo illustra come installare e registrare il servizio proxy di Azure AD Password Protection e l'agente Azure AD Password Protection DC nell'ambiente locale.

Per altre informazioni sul funzionamento di Azure AD Password Protection in un ambiente locale, vedere [Come applicare Azure AD Password Protection per Windows Server Active Directory.](concept-password-ban-bad-on-premises.md)

## <a name="deployment-strategy"></a>Strategia di distribuzione

Il diagramma seguente mostra come i componenti di base di Azure AD Password Protection si uniscono in un ambiente Active Directory locale:The following diagram shows how the basic components of Azure AD Password Protection work together in an on-premises Active Directory environment:

![Come interagiscono i componenti di Protezione password di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

È consigliabile esaminare il funzionamento del software prima di distribuirlo. Per altre informazioni, vedere [Panoramica concettuale di Azure AD Password Protection](concept-password-ban-bad-on-premises.md).For more information, see Conceptual overview of Azure AD Password Protection .

È consigliabile avviare le distribuzioni in modalità *di controllo.* La modalità di controllo è l'impostazione iniziale predefinita, in cui le password possono continuare a essere impostate. Le password che verrebbero bloccate vengono registrate nel registro eventi. Dopo aver distribuito i server proxy e gli agenti controller di dominio in modalità di controllo, monitorare l'impatto che i criteri password avranno sugli utenti quando il criterio viene applicato.

Durante la fase di controllo, molte organizzazioni ritrovano che si applicano le seguenti situazioni:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Gli utenti spesso utilizzano password non sicure.
* Devono informare gli utenti circa l'imminente modifica dell'applicazione della sicurezza, il possibile impatto su di essi e come scegliere password più sicure.

È anche possibile che una convalida delle password più avanzata influisca sull'automazione della distribuzione del controller di dominio Active Directory esistente. È consigliabile che almeno una promozione controller di dominio e un abbassamento di livello controller di dominio si verifichino durante la valutazione del periodo di controllo per individuare tali problemi. Per altre informazioni, vedere gli articoli seguenti:

* [Ntdsutil.exe non è in grado di impostare una password della modalità di ripristino dei servizi directory debole](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [L'innalzamento di livello della replica del controller di dominio non riesce a causa di una password debole della modalità ripristino servizi directory](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Abbassamento di livello del controller di dominio non riesce a causa di una password di amministratore locale deboleDomain controller demotion fails due to a weak local Administrator password](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Dopo che la funzionalità è stata eseguita in modalità di controllo per un periodo ragionevole, è possibile passare da *Audit* a *Enforce* per richiedere password più sicure. Un monitoraggio aggiuntivo durante questo periodo è una buona idea.

### <a name="multiple-forest-considerations"></a>Considerazioni su più foresteMultiple forest considerations

Non sono previsti requisiti aggiuntivi per distribuire Protezione password di Azure AD in più foreste.

Ogni foresta è configurata in modo indipendente, come descritto nella sezione seguente per [distribuire Azure AD Password Protection](#download-required-software)locale. Ogni proxy di Azure AD Password Protection può supportare solo i controller di dominio della foresta a cui fa parte.

Il software di protezione password di Azure AD in qualsiasi foresta non è a conoscenza del software di protezione con password distribuito in altre foreste, indipendentemente dalle configurazioni di attendibilità di Active Directory.The Azure AD Password Protection software in any forest is inaware of password protection software that's deployed in other forests, regardless of Active Directory trust configurations.

### <a name="read-only-domain-controller-considerations"></a>Considerazioni sui controller di dominio di sola letturaRead-only domain controller considerations

La modifica della password o l'impostazione di eventi non vengono elaborati e resi persistenti nei controller di dominio di sola lettura (RODC). Al contrario, vengono inoltrati a controller di dominio scrivibili. Non è necessario installare il software dell'agente Azure AD Password Protection DC nei controller di dominio di sola lettura.

Inoltre, non è supportato per eseguire il servizio proxy di Azure AD Password Protection in un controller di dominio di sola lettura.

### <a name="high-availability-considerations"></a>Considerazioni sulla disponibilità elevataHigh availability considerations

The main concern for password protection is the availability of Azure AD Password Protection proxy servers when the DCs in a forest try to download new policies or other data from Azure. Ogni agente azure AD Password Protection DC usa un semplice algoritmo di tipo round robin per decidere quale server proxy chiamare. L'agente ignora i server proxy che non rispondono.

Per la maggior parte delle distribuzioni di Active Directory completamente connesse con la replica integra dello stato della directory e della cartella sysvol, due server proxy di Azure AD Password Protection sono sufficienti per garantire la disponibilità. Questa configurazione consente di scaricare tempestivamente nuovi criteri e altri dati. Se lo si desidera, è possibile distribuire altri server proxy di Azure AD Password Protection.You can deploy additional Azure AD Password Protection proxy servers if desired.

La progettazione del software dell'agente azure AD Password Protection DC riduce i problemi abituali associati alla disponibilità elevata. L'agente Azure AD Password Protection DC gestisce una cache locale dei criteri password scaricati più di recente. Anche se tutti i server proxy registrati non sono più disponibili, gli agenti del controller di dominio di Protezione password di Azure AD continuano ad applicare i criteri password memorizzati nella cache.

Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere giorni, non ore o meno. Pertanto, brevi interruzioni dei server proxy non influiscono in modo significativo sulla protezione delle password di Azure AD.

## <a name="deployment-requirements"></a>Requisiti di distribuzione

Per informazioni sulle licenze, vedere Requisiti di licenza di [Azure AD Password Protection](concept-password-ban-bad.md#license-requirements).

Si applicano i seguenti requisiti principali:

* In tutti i computer, inclusi i controller di dominio, in cui sono installati i componenti di Protezione password di Azure AD, è necessario che sia installato il runtime C universale.
    * È possibile ottenere il runtime assicurandosi di disporre di tutti gli aggiornamenti da Windows Update. Oppure puoi ottenerlo in un pacchetto di aggiornamento specifico del sistema operativo. Per ulteriori informazioni, consultate [Aggiornamento per Universal C Runtime in Windows.](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)
* È necessario un account con privilegi di amministratore di dominio di Active Directory nel dominio radice della foresta per registrare la foresta di Windows Server Active Directory con Azure AD.
* Il servizio di distribuzione delle chiavi deve essere abilitato in tutti i controller di dominio nel dominio che esegue Windows Server 2012. Per impostazione predefinita, questo servizio è abilitato tramite l'avvio manuale del trigger.
* La connettività di rete deve esistere tra almeno un controller di dominio in ogni dominio e almeno un server che ospita il servizio proxy per Protezione password di Azure AD.Network connectivity must exist between at least one domain controller in each domain and at least one server that hosts the proxy service for Azure AD Password Protection. Questa connettività deve consentire al controller di dominio di accedere alla porta 135 del mapping degli endpoint RPC e alla porta del server RPC nel servizio proxy.
    * Per impostazione predefinita, la porta del server RPC è una porta RPC dinamica, ma può essere configurata per [l'utilizzo di una porta statica.](#static)
* Tutti i computer in cui verrà installato il servizio Proxy di protezione password di Azure AD devono disporre dell'accesso di rete agli endpoint seguenti:All machines where the Azure AD Password Protection Proxy service will be installed must have network access to the following endpoints:

    |**Endpoint**|**Scopo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Richieste di autenticazione|
    |`https://enterpriseregistration.windows.net`|Funzionalità di Protezione password di Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente del controller di dominio di Azure AD Password ProtectionAzure AD Password Protection DC agent

I requisiti seguenti si applicano all'agente Azure AD Password Protection DC:

* Tutti i computer in cui verrà installato il software dell'agente Azure AD Password Protection controller di dominio devono eseguire Windows Server 2012 o versione successiva.
    * Il dominio o la foresta di Active Directory non deve essere a livello di funzionalità del dominio di Windows Server 2012 (DFL) o livello di funzionalità della foresta (FFL). Come accennato in [Design Principles](concept-password-ban-bad-on-premises.md#design-principles), non è necessario alcun DFL o FFL minimo per l'esecuzione dell'agente controller di dominio o del software proxy.
* In tutti i computer che eseguono l'agente di Azure AD Password Protection del controller di dominio devono essere installati .NET 4.5.All machines that run the Azure AD Password Protection DC agent must have .NET 4.5 installed.
* Qualsiasi dominio di Active Directory che esegue il servizio agente agente di Azure AD Password Protection controller di dominio deve utilizzare Distributed File System Replication (DFSR) per la replica sysvol.
   * Se il dominio non usa già DFSR, è necessario eseguire la migrazione prima di installare Azure AD Password Protection. Per ulteriori informazioni, vedere Guida alla migrazione della [replica SYSVOL: replica da FRS a DFSFor more information, see SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Il software dell'agente Azure AD Password Protection CONTROLLER verrà attualmente installato nei controller di dominio in domini che utilizzano ancora FRS (la tecnologia precedente a DFSR) per la replica sysvol, ma il software NON funzionerà correttamente in questo ambiente.
    >
    > Ulteriori effetti collaterali negativi includono singoli file che non vengono replicati e le procedure di ripristino sysvol sembrano avere esito positivo, ma non riescono a replicare tutti i file.
    >
    > Eseguire la migrazione del dominio per usare DFSR il prima possibile, sia per i vantaggi intrinseci di DFSR che per sbloccare la distribuzione di Azure AD Password Protection. Le versioni future del software verranno disabilitate automaticamente quando vengono eseguite in un dominio che utilizza ancora FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Servizio proxy di Azure AD Password ProtectionAzure AD Password Protection proxy service

I requisiti seguenti si applicano al servizio proxy di Azure AD Password Protection:The following requirements apply to the Azure AD Password Protection proxy service:

* Tutti i computer in cui verrà installato il servizio proxy Di Azure AD Password Protection devono eseguire Windows Server 2012 R2 o versione successiva.

    > [!NOTE]
    > La distribuzione del servizio proxy di Azure AD Password Protection è un requisito obbligatorio per la distribuzione di Azure AD Password Protection anche se il controller di dominio potrebbe disporre di connettività Internet diretta in uscita.

* In tutti i computer in cui verrà installato il servizio proxy di protezione password di Azure AD devono essere installati .NET 4.7.All machines where the Azure AD Password Protection proxy service will be installed must have .NET 4.7 installed.
    * .NET 4.7 deve essere già installato in un Windows Server completamente aggiornato. Se necessario, scaricare ed eseguire il programma di installazione disponibile nel programma di installazione non in linea di [.NET Framework 4.7 per Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Tutti i computer che ospitano il servizio proxy di protezione password di Azure AD devono essere configurati per concedere ai controller di dominio la possibilità di accedere al servizio proxy. Questa capacità è controllata tramite l'assegnazione dei privilegi "Accedi al computer dalla rete".
* Tutti i computer che ospitano il servizio proxy di protezione password di Azure AD devono essere configurati per consentire il traffico HTTP TLS 1.2 in uscita.
* Un account *amministratore globale* per registrare il servizio proxy e la foresta di Azure AD Password Protection con Azure AD.
* L'accesso alla rete deve essere abilitato per il set di porte e URL specificati nelle procedure di [configurazione dell'ambiente del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Prerequisiti di Microsoft Azure AD Connect Agent Updater

Il servizio Microsoft Azure AD Connect Agent Updater viene installato affiancato al servizio Proxy di protezione password di Azure AD. È necessaria una configurazione aggiuntiva affinché il servizio Microsoft Azure AD Connect Agent Updater sia in grado di funzionare:Additional configuration is required in order the Microsoft Azure AD Connect Agent Updater service to be able to function:

* Se l'ambiente utilizza un server proxy HTTP, seguire le linee guida specificate in [Utilizzare server proxy locali esistenti.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)
* Il servizio Microsoft Azure AD Connect Agent Updater richiede anche i passaggi TLS 1.2 specificati in [Requisiti TLS.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)

> [!WARNING]
> Il proxy di Azure AD Password Protection e il proxy di applicazione di Azure AD installano versioni diverse del servizio Microsoft Azure AD Connect Agent Updater, motivo per cui le istruzioni fanno riferimento al contenuto del proxy di applicazione. Queste versioni diverse sono incompatibili quando vengono installate side-by-side, pertanto non è consigliabile installare il proxy di protezione password di Azure AD e il proxy di applicazione nello stesso computer.

## <a name="download-required-software"></a>Scaricare il software necessario

Esistono due programmi di installazione necessari per una distribuzione di Azure AD Password Protection locale:There are two required installers for an on-premises Azure AD Password Protection deployment:

* Agente controller di dominio di Azure AD Password Protection (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Proxy di protezione password di Azure AD (*AzureADPasswordProtectionProxySetup.exe*)

Scaricare entrambi i programmi di [installazione dall'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Installare e configurare il servizio proxy

Il servizio proxy di Azure AD Password Protection si trova in genere in un server membro nell'ambiente locale di Servizi di dominio Active Directory.The Azure AD Password Protection proxy service is typically on a member server in your on-premises AD DS environment. Una volta installato, il servizio proxy di protezione password di Azure AD comunica con Azure AD per mantenere una copia degli elenchi di password non soddisfatte globali e dei clienti per il tenant di Azure AD.

Nella sezione successiva vengono installati gli agenti del controller di dominio di Azure AD Password Protection nei controller di dominio nell'ambiente locale di Servizi di dominio Active Directory. Questi agenti controller di dominio comunicano con il servizio proxy per ottenere gli elenchi di password non aperte più recenti da utilizzare durante l'elaborazione degli eventi di modifica della password all'interno del dominio.

Scegliere uno o più server per ospitare il servizio proxy Di Azure AD Password Protection.Choose one or more servers to host the Azure AD Password Protection proxy service. Per i server si applicano le seguenti considerazioni:

* Ogni servizio di questo tipo può fornire criteri password solo per una singola foresta. Il computer host deve essere aggiunto a un dominio in tale foresta. I domini radice e figlio sono entrambi supportati. È necessaria la connettività di rete tra almeno un controller di dominio in ogni dominio dell'insieme di strutture e il computer di protezione con password.
* È possibile eseguire il servizio proxy Di Azure AD Password Protection in un controller di dominio per il test, ma tale controller di dominio richiede quindi la connettività Internet.You can run the Azure AD Password Protection proxy service on a domain controller for testing, but that domain controller then requires internet connectivity. Questa connettività può essere un problema di sicurezza. Questa configurazione è consigliata solo per il test.
* Per la ridondanza, è consigliabile utilizzare almeno due server proxy di Protezione password di Azure AD, come indicato nella sezione precedente per considerazioni sulla [disponibilità elevata.](#high-availability-considerations)
* Non è supportato per eseguire il servizio proxy Di Azure AD Password Protection in un controller di dominio di sola lettura.

Per installare il servizio proxy di Azure AD Password Protection, completare i passaggi seguenti:To install the Azure AD Password Protection proxy service, complete the following steps:

1. Per installare il servizio proxy di `AzureADPasswordProtectionProxySetup.exe` Azure AD Password Protection, eseguire il programma di installazione del software.

    L'installazione del software non richiede un riavvio e può essere automatizzata utilizzando le procedure MSI standard, come nell'esempio seguente:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Il servizio Windows Firewall deve essere `AzureADPasswordProtectionProxySetup.exe` in esecuzione prima di installare il pacchetto per evitare un errore di installazione.
    >
    > Se Windows Firewall non è configurato per l'esecuzione, la soluzione consiste nell'abilitare ed eseguire temporaneamente il servizio Firewall durante l'installazione. Il software proxy non ha alcuna dipendenza specifica su Windows Firewall dopo l'installazione.
    >
    > Se si utilizza un firewall di terze parti, è comunque necessario configurarlo per soddisfare i requisiti di distribuzione. Tra questi figurano l'accesso in ingresso alla porta 135 e alla porta del server RPC proxy. Per ulteriori informazioni, vedere la sezione precedente sui requisiti di [distribuzione.](#deployment-requirements)

1. Il software proxy Azure AD Password Protection `AzureADPasswordProtection`include un nuovo modulo di PowerShell, . I passaggi seguenti eseguono vari cmdlet da questo modulo di PowerShell.The following steps run various cmdlets from this PowerShell module.

    Per usare questo modulo, aprire una finestra di PowerShell come amministratore e importare il nuovo modulo come segue:To use this module, open a PowerShell window as an administrator and import the new module as follows:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Per verificare che il servizio proxy di Azure AD Password Protection sia in esecuzione, usare il comando di PowerShell seguente:To check that the Azure AD Password Protection proxy service is running, use the following PowerShell command:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Il risultato dovrebbe mostrare **lo stato** *In esecuzione*.

1. Il servizio proxy è in esecuzione nel computer, ma non dispone delle credenziali per comunicare con Azure AD. Registrare il server proxy di Azure `Register-AzureADPasswordProtectionProxy` AD Password Protection con Azure AD usando il cmdlet.

    Questo cmdlet richiede credenziali di amministratore globale per il tenant di Azure.This cmdlet requires global administrator credentials for your Azure tenant. Sono inoltre necessari privilegi di amministratore di dominio di Active Directory locale nel dominio radice della foresta. Questo cmdlet deve essere eseguito anche utilizzando un account con privilegi di amministratore locale:

    Dopo che questo comando ha esito positivo una volta per un servizio proxy di Azure AD Password Protection, le relative chiamate aggiuntive hanno esito positivo, ma non sono necessarie.

    Il `Register-AzureADPasswordProtectionProxy` cmdlet supporta le tre modalità di autenticazione seguenti. Le prime due modalità supportano Azure Multi-Factor Authentication, ma non la terza modalità.

    > [!TIP]
    > Potrebbe verificarsi un notevole ritardo prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi Server Core. Utilizzare invece una delle seguenti modalità di autenticazione. Inoltre, questa modalità potrebbe non riuscire se è attivata la Protezione avanzata di Internet Explorer. La soluzione consiste nel disabilitare tale configurazione, registrare il proxy e quindi riabilitarlo.

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando richiesto, seguire il collegamento per aprire un browser Web e immettere il codice di autenticazione.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità non riesce se per l'account è richiesto Azure Multi-Factor Authentication.This mode fails if Azure Multi-Factor Authentication is required for your account. In tal caso, utilizzare una delle due modalità di autenticazione precedenti o utilizzare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È inoltre possibile vedere l'autenticazione a più fattori necessaria se la registrazione dei dispositivi di Azure (usata sotto le coperte da Azure AD Password Protection) è stata configurata per richiedere globalmente l'autenticazione a più fattori. Per risolvere questo requisito è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è anche possibile ridurre temporaneamente temporaneamente il requisito di Azure Device Registration MFA.
        >
        > Per apportare questa modifica, cercare e selezionare **Azure Active Directory** nel portale di Azure, quindi selezionare Dispositivi > Impostazioni **dispositivo**. Impostare **Richiedi autenticazione a più fattori per unire i dispositivi** su *No*. Assicurarsi di riconfigurare questa impostazione su *Sì* al termine della registrazione.
        >
        > È consigliabile che i requisiti di autenticazione a più fattori vengano ignorati solo a scopo di test.

    Al momento non è necessario specificare il parametro *-ForestCredential,* riservato a funzionalità future.

    La registrazione del servizio proxy di Azure AD Password Protection è necessaria solo una volta nella durata del servizio. Successivamente, il servizio proxy di protezione password di Azure AD eseguirà automaticamente qualsiasi altra manutenzione necessaria.

1. Registrare ora la foresta di Active Directory locale con le credenziali `Register-AzureADPasswordProtectionForest` necessarie per comunicare con Azure usando il cmdlet PowerShell.Now register the on-premises Active Directory forest with the necessary credentials to communicate with Azure by using the PowerShell cmdlet.

    > [!NOTE]
    > Se nell'ambiente sono installati più server proxy Di Azure AD Password Protection, non importa quale server proxy si usa per registrare la foresta.

    Il cmdlet richiede credenziali di amministratore globale per il tenant di Azure.The cmdlet requires global administrator credentials for your Azure tenant. È inoltre necessario eseguire questo cmdlet utilizzando un account con privilegi di amministratore locale. Richiede inoltre privilegi di amministratore dell'organizzazione di Active Directory locale. Questo passaggio viene eseguito una volta per ogni foresta.

    Il `Register-AzureADPasswordProtectionForest` cmdlet supporta le tre modalità di autenticazione seguenti. Le prime due modalità supportano Azure Multi-Factor Authentication, ma non la terza modalità.

    > [!TIP]
    > Potrebbe verificarsi un notevole ritardo prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi Server Core. Utilizzare invece una delle due modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe non riuscire se è attivata la Protezione avanzata di Internet Explorer. La soluzione consiste nel disabilitare tale configurazione, registrare l'insieme di strutture e quindi riabilitarlo.  

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando richiesto, seguire il collegamento per aprire un browser Web e immettere il codice di autenticazione.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità non riesce se per l'account è richiesto Azure Multi-Factor Authentication.This mode fails if Azure Multi-Factor Authentication is required for your account. In tal caso, utilizzare una delle due modalità di autenticazione precedenti o utilizzare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È inoltre possibile vedere l'autenticazione a più fattori necessaria se la registrazione dei dispositivi di Azure (usata sotto le coperte da Azure AD Password Protection) è stata configurata per richiedere globalmente l'autenticazione a più fattori. Per risolvere questo requisito è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è anche possibile ridurre temporaneamente temporaneamente il requisito di Azure Device Registration MFA.
        >
        > Per apportare questa modifica, cercare e selezionare **Azure Active Directory** nel portale di Azure, quindi selezionare Dispositivi > Impostazioni **dispositivo**. Impostare **Richiedi autenticazione a più fattori per unire i dispositivi** su *No*. Assicurarsi di riconfigurare questa impostazione su *Sì* al termine della registrazione.
        >
        > È consigliabile che i requisiti di autenticazione a più fattori vengano ignorati solo a scopo di test.

       Questi esempi hanno esito positivo solo se l'utente attualmente connesso è anche un amministratore di dominio di Active Directory per il dominio radice. In questo caso, è possibile fornire credenziali di dominio alternative tramite il parametro *-ForestCredential.*

    La registrazione della foresta di Active Directory è necessaria una sola volta nella durata dell'insieme di strutture. Successivamente, gli agenti del controller di dominio di protezione password di Azure AD nella foresta eseguono automaticamente qualsiasi altra manutenzione necessaria. Dopo `Register-AzureADPasswordProtectionForest` l'esecuzione corretta per una foresta, le chiamate aggiuntive del cmdlet hanno esito positivo, ma non sono necessarie.
    
    Affinché `Register-AzureADPasswordProtectionForest` abbia esito positivo, almeno un controller di dominio che esegue Windows Server 2012 o versione successiva deve essere disponibile nel dominio del server proxy Di Azure AD Password Protection. Il software dell'agente Azure AD Password Protection CONTROLLER non deve essere installato in alcun controller di dominio prima di questo passaggio.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configurare il servizio proxy per comunicare tramite un proxy HTTPConfigure the proxy service to communicate through an HTTP proxy

Se l'ambiente richiede l'uso di un proxy HTTP specifico per comunicare con Azure, usare la procedura seguente per configurare il servizio di protezione password di Azure AD.

Creare un file *AzureADPasswordProtectionProxy.exe.config* nella `%ProgramFiles%\Azure AD Password Protection Proxy\Service` cartella. Includere il contenuto seguente:

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

Se il proxy HTTP richiede l'autenticazione, aggiungere il tag *useDefaultCredentials:*

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

In entrambi i `http://yourhttpproxy.com:8080` casi, sostituire con l'indirizzo e la porta del server proxy HTTP specifico.

Se il proxy HTTP è configurato per l'utilizzo di un criterio di autorizzazione, è necessario concedere l'accesso all'account computer di Active Directory del computer che ospita il servizio proxy per la protezione con password.

È consigliabile arrestare e riavviare il servizio proxy di Azure AD Password Protection dopo aver creato o aggiornato il file *AzureADPasswordProtectionProxy.exe.config.*

Il servizio proxy non supporta l'utilizzo di credenziali specifiche per la connessione a un proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configurare il servizio proxy per l'ascolto su una porta specifica

Il software dell'agente Azure AD Password Protection DC usa RPC su TCP per comunicare con il servizio proxy. Per impostazione predefinita, il servizio proxy di Azure AD Password Protection è in ascolto su qualsiasi endpoint RPC dinamico disponibile. È possibile configurare il servizio per l'ascolto su una porta TCP specifica, se necessario a causa della topologia di rete o dei requisiti del firewall nell'ambiente.

<a id="static" /></a>Per configurare il servizio per l'esecuzione in una porta statica, utilizzare il `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet come segue:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Per rendere effettive le modifiche, è necessario arrestare e riavviare il servizio proxy Di Azure AD Password Protection.You must stop and restart the Azure AD Password Protection proxy service for these changes to effect.

Per configurare il servizio per l'esecuzione con una porta dinamica, utilizzare la stessa procedura ma impostare StaticPort su zero:To configure the service to run under a dynamic port, use the same procedure but set *StaticPort* back to zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Per rendere effettive le modifiche, è necessario arrestare e riavviare il servizio proxy Di Azure AD Password Protection.You must stop and restart the Azure AD Password Protection proxy service for these changes to effect.

Il servizio proxy di Azure AD Password Protection richiede un riavvio manuale dopo qualsiasi modifica nella configurazione della porta. Non è necessario riavviare il servizio agente di Azure AD Password Protection controller di dominio nei controller di dominio dopo aver apportato queste modifiche alla configurazione.

Per eseguire una query per la `Get-AzureADPasswordProtectionProxyConfiguration` configurazione corrente del servizio, utilizzare il cmdlet come illustrato nell'esempio seguenteTo query for the current configuration of the service, use the cmdlet as shown in the following example

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

L'output di esempio seguente mostra che il servizio proxy Di Azure AD Password Protection usa una porta dinamica:The following example output shows that the Azure AD Password Protection proxy service is using a dynamic port:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Installare il servizio agente controller di dominio

Per installare il servizio agente Azure AD `AzureADPasswordProtectionDCAgentSetup.msi` Password Protection CONTROLLER di dominio, eseguire il pacchetto.

È possibile automatizzare l'installazione del software utilizzando le procedure MSI standard, come illustrato nell'esempio seguente:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Il `/norestart` flag può essere omesso se si preferisce che il programma di installazione riavvii automaticamente la macchina.

L'installazione o la disinstallazione del software richiede un riavvio. Questo requisito è dovuto al fatto che le DLL del filtro password vengono caricate o scaricate solo da un riavvio.

L'installazione di Azure AD Password Protection locale è stata completata dopo l'installazione del software dell'agente controller di dominio in un controller di dominio e il riavvio del computer. Non è necessario né possibile eseguire altre configurazioni. Gli eventi di modifica della password nei controller di dominio puniti usano gli elenchi di password non configurate di Azure AD.

Per abilitare Azure AD Password Protection locale dal portale di Azure o configurare password non crittografate personalizzate, vedere [Abilitare Azure AD Password Protection locale.](howto-password-ban-bad-on-premises-operations.md)

> [!TIP]
> È possibile installare l'agente azure AD Password Protection DC in un computer che non è ancora un controller di dominio. In questo caso, il servizio viene avviato ed eseguito ma rimane inattivo fino a quando il computer non viene promosso a controller di dominio.

## <a name="upgrading-the-proxy-service"></a>Aggiornamento del servizio proxy

Il servizio proxy Di Azure AD Password Protection supporta l'aggiornamento automatico. L'aggiornamento automatico usa il servizio Microsoft Azure AD Connect Agent Updater, installato affiancato al servizio proxy. L'aggiornamento automatico è attivato per impostazione `Set-AzureADPasswordProtectionProxyConfiguration` predefinita e può essere abilitato o disabilitato utilizzando il cmdlet.

È possibile eseguire una query `Get-AzureADPasswordProtectionProxyConfiguration` sull'impostazione corrente utilizzando il cmdlet. È consigliabile abilitare sempre l'impostazione di aggiornamento automatico.

Il `Get-AzureADPasswordProtectionProxy` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti i server proxy di Azure AD Password Protection attualmente installati in una foresta.

### <a name="manual-upgrade-process"></a>Processo di aggiornamento manuale

Un aggiornamento manuale viene eseguito eseguendo `AzureADPasswordProtectionProxySetup.exe` la versione più recente del programma di installazione del software. L'ultima versione del software è disponibile [nell'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del servizio proxy Di Azure AD Password Protection: il programma di installazione esegue un aggiornamento sul posto. Non è necessario riavviare il sistema durante l'aggiornamento del servizio proxy. L'aggiornamento del software può essere automatizzato `AzureADPasswordProtectionProxySetup.exe /quiet`utilizzando le procedure MSI standard, come .

## <a name="upgrading-the-dc-agent"></a>Aggiornamento dell'agente controller di dominio

Quando è disponibile una versione più recente del software dell'agente Azure AD Password Protection `AzureADPasswordProtectionDCAgentSetup.msi` DC, l'aggiornamento viene eseguito eseguendo la versione più recente del pacchetto software. L'ultima versione del software è disponibile [nell'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del software dell'agente di controller di dominio: il programma di installazione esegue un aggiornamento sul posto. Un riavvio è sempre necessario quando si aggiorna il software dell'agente di controller di dominio - questo requisito è causato dal comportamento di base di Windows.

L'aggiornamento del software può essere automatizzato `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`utilizzando le procedure MSI standard, come .

È possibile omettere `/norestart` il flag se si preferisce che il programma di installazione riavvii automaticamente la macchina.

Il `Get-AzureADPasswordProtectionDCAgent` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti gli agenti del controller di dominio di Azure AD Password Protection attualmente installati in una foresta.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato i servizi necessari per Azure AD Password Protection nei server locali, [abilitare Azure AD Password Protection locale nel portale](howto-password-ban-bad-on-premises-operations.md) di Azure per completare la distribuzione.
