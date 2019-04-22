---
title: Distribuire la protezione delle password di Azure AD - Azure Active Directory
description: Distribuire la protezione di password di Azure AD per vietare password errate in locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c24ec49652cfe9105aa66fd1d5e26c81afcd14
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904628"
---
# <a name="deploy-azure-ad-password-protection"></a>Distribuire la protezione delle password di Azure AD

Ora che abbiamo appreso [come applicare la protezione di password di Azure AD per Windows Server Active Directory](concept-password-ban-bad-on-premises.md), il passaggio successivo consiste per pianificare ed eseguire la distribuzione.

## <a name="deployment-strategy"></a>Strategia di distribuzione

Si consiglia di iniziare le distribuzioni in modalità di controllo. Modalità di controllo è l'impostazione iniziale predefinito, in cui le password possono continuare a essere impostata. Le password che verranno bloccate vengono registrate nel registro eventi. Dopo aver distribuito il server proxy e gli agenti di controller di dominio in modalità di controllo, è necessario monitorare l'impatto che quando i criteri vengono applicati i criteri password avrà sugli utenti e l'ambiente.

Durante la fase di controllo, molte organizzazioni ritengono che:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Gli utenti usano spesso le password non sicure.
* È necessario informare gli utenti le future modifiche nell'applicazione della protezione possibile impatto su di essi e come scegliere le password più sicure.

Dopo la funzionalità di esecuzione in modalità di controllo per un periodo di tempo ragionevole, è possibile cambiare la configurazione dal *Audit* al *Imponi* richiedere password più sicura. Il monitoraggio con lo stato attivo in questa fase è una scelta valida.

## <a name="deployment-requirements"></a>Requisiti di distribuzione

* Tutti i controller di dominio che ottiene l'agente controller di dominio del servizio per la protezione con password Azure AD installata deve eseguire Windows Server 2012 o versione successiva. Questo requisito non implica che il dominio di Active Directory o un insieme di strutture devono essere anche a livello funzionale di Windows Server 2012 dominio o foresta. Come indicato nella [principi di progettazione](concept-password-ban-bad-on-premises.md#design-principles), non sono funzionalità del dominio minimo o FFL necessaria per entrambi i controller di dominio agente o un proxy software per l'esecuzione.
* Tutti i computer che ottengono installato il servizio agente controller di dominio devono avere installato .NET 4.5.
* Tutte le macchine che ottengono il proxy di servizio per la protezione con password Azure AD installata deve eseguire Windows Server 2012 R2 o versione successiva.
* Tutti i computer in cui verrà installato il servizio Proxy di protezione delle Password di Azure AD è necessario .NET 4.7 installato.
  .NET 4.7 deve già essere installato in un Server di Windows completamente aggiornato. Se ciò non avviene, scaricare ed eseguire il programma di installazione, vedere [programma di installazione offline di .NET Framework 4.7 per Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Tutte le macchine, tra cui i controller di dominio, che ottengono installati i componenti di protezione di Azure AD password deve avere installato il Runtime C universali. È possibile ottenere il runtime, garantendo che si dispone di tutti gli aggiornamenti da Windows Update. Oppure è possibile crearlo in un pacchetto di aggiornamento del sistema operativo specifico. Per altre informazioni, vedere [aggiornamento di Universal C Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Connettività di rete tra deve esistere almeno un controller di dominio in ogni dominio e almeno un server che ospita il servizio proxy per la protezione con password. Tale connettività deve consentire il controller di dominio accedere a RPC endpoint mapper la porta 135 e la porta del server RPC del servizio proxy. Per impostazione predefinita, la porta del server RPC è una porta dinamica RPC, ma può essere configurato per [usare una porta statica](#static).
* Tutti i computer che ospitano il servizio proxy devono avere accesso alla rete agli endpoint seguenti:

    |**Endpoint**|**Scopo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Richieste di autenticazione|
    |`https://enterpriseregistration.windows.net`|Funzionalità di protezione password di Azure AD|

* Tutti i computer che ospitano il servizio proxy per la protezione con password devono essere configurati per consentire il traffico TLS 1.2 HTTP in uscita.
* Un account di amministratore globale per registrare il servizio proxy per la protezione con password e la foresta in Azure AD.
* Un account con privilegi di amministratore di dominio Active Directory nel dominio radice della foresta per registrare la foresta di Windows Server Active Directory con Azure AD.
* Qualsiasi dominio di Active Directory che viene eseguito il software di servizio dell'agente DC deve utilizzare DFSR Distributed File System Replication () per la replica di sysvol.
* Il servizio distribuzione chiavi deve essere abilitato su tutti i controller di dominio nel dominio che eseguono Windows Server 2012. Per impostazione predefinita, questo servizio viene abilitato tramite avvio manuale del trigger.

## <a name="single-forest-deployment"></a>Distribuzione a foresta singola

Il diagramma seguente illustra come i componenti di base di protezione di Azure AD password interagiscono in un ambiente Active Directory locale.

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

È consigliabile esaminare il funzionamento di software prima della distribuzione. Visualizzare [panoramica concettuale della protezione tramite password di Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Scaricare il software

Sono disponibili due programmi di installazione necessari per la protezione di password di Azure AD. Sono disponibili dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installare e configurare il servizio proxy per la protezione con password

1. Scegliere uno o più server per ospitare il servizio proxy per la protezione con password.
   * Ogni servizio di questo tipo possa fornire solo i criteri password per una singola foresta. Il computer host deve appartenere a un dominio in tale foresta. Domini radice e figlio sono entrambi supportati. È necessaria la connettività di rete tra il computer di protezione di password e almeno un controller di dominio in ogni dominio della foresta.
   * È possibile eseguire il servizio proxy in un controller di dominio per il test. Ma il controller di dominio richiede quindi la connettività internet, che può essere una questione di sicurezza. Si consiglia questa configurazione solo per i test.
   * È consigliabile almeno due server proxy per la ridondanza. Visualizzare [disponibilità elevata](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installare Azure AD Password Protection Proxy servizio utilizzando il `AzureADPasswordProtectionProxySetup.exe` programma di installazione software.
   * Non è necessario riavviare dopo l'installazione del software. L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Il servizio Windows Firewall deve essere in esecuzione prima di installare il pacchetto AzureADPasswordProtectionProxySetup.msi per evitare un errore di installazione. Se Windows Firewall è configurato per non eseguire, la soluzione alternativa è temporaneamente abilitare ed eseguire il servizio Firewall durante l'installazione. Il software proxy non ha alcuna dipendenza specifica nel Firewall di Windows dopo l'installazione. Se si usa un firewall di terze parti, deve ancora essere configurato per soddisfare i requisiti di distribuzione. Ad esempio che consente l'accesso in ingresso alla porta 135 e il porta del server RPC proxy. Visualizzare [requisiti di distribuzione](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Aprire una finestra di PowerShell come amministratore.
   * Il software di proxy password protection include il nuovo modulo PowerShell *AzureADPasswordProtection*. I passaggi seguenti eseguire diversi cmdlet da questo modulo di PowerShell. Importare il nuovo modulo come segue:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Per verificare che il servizio sia in esecuzione, usare il comando PowerShell seguente:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Il risultato dovrebbe visualizzare una **stato** "Running".

1. Registrare il proxy.
   * Dopo aver completato il passaggio 3, il servizio proxy è in esecuzione nel computer. Ma il servizio non ha ancora le credenziali necessarie per comunicare con Azure AD. È necessaria la registrazione con Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     Questo cmdlet richiede le credenziali di amministratore globale del tenant di Azure. È anche necessario privilegi di amministratore di dominio locali Active Directory nel dominio radice della foresta. Dopo questo comando viene eseguito una sola volta per un servizio di proxy, le chiamate aggiuntive di esso avrà esito positivo ma non sono necessarie.

      Il `Register-AzureADPasswordProtectionProxy` cmdlet supporta i seguenti tre modalità di autenticazione.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi Server Core. Usare invece una delle modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe non riuscire se è abilitata la funzionalità sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare il proxy e quindi riabilitarla.

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        È quindi possibile completare l'autenticazione, seguire le istruzioni visualizzate in un altro dispositivo.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità non riesce se è necessaria l'autenticazione a più fattori di Azure. In tal caso, usare una delle modalità di autenticazione a due precedenti.

       Non è attualmente necessario specificare il *- ForestCredential* parametro, che è riservato per le funzionalità future.

   Registrazione del servizio proxy per la protezione con password è necessaria una sola volta nel ciclo di vita del servizio. Successivamente, il servizio proxy eseguirà automaticamente le altre operazioni di manutenzione necessari.

   > [!TIP]
   > Potrebbe esserci un ritardo notevole prima del completamento, la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non viene segnalato un errore, non preoccuparsi di questo ritardo.

1. Registrare la foresta.
   * È necessario inizializzare la foresta di Active Directory locale con le credenziali necessarie per comunicare con Azure usando il `Register-AzureADPasswordProtectionForest` cmdlet di PowerShell. Il cmdlet richiede le credenziali di amministratore globale del tenant di Azure. Richiede anche privilegi di amministratore di dominio locali Active Directory nel dominio radice della foresta. Questo passaggio viene eseguito una volta per ogni foresta.

      Il `Register-AzureADPasswordProtectionForest` cmdlet supporta i seguenti tre modalità di autenticazione.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funzionerà nei sistemi operativi Server Core. Usare invece una delle seguenti modalità di autenticazione a due. Inoltre, questa modalità potrebbe non riuscire se è abilitata la funzionalità sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare il proxy e quindi riabilitarla.  

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        È quindi possibile completare l'autenticazione, seguire le istruzioni visualizzate in un altro dispositivo.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità non riesce se è necessaria l'autenticazione a più fattori di Azure. In tal caso, usare una delle modalità di autenticazione a due precedenti.

       Questi esempi hanno esito positivo solo se l'utente attualmente connesso è anche un amministratore di dominio Active Directory per il dominio radice. Se ciò non avviene, è possibile fornire le credenziali di dominio alternativo tramite il *- ForestCredential* parametro.

   > [!NOTE]
   > Se sono installati più server proxy nell'ambiente in uso, non importa quale server proxy utilizzato per registrare l'insieme di strutture.
   >
   > [!TIP]
   > Potrebbe esserci un ritardo notevole prima del completamento, la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non viene segnalato un errore, non preoccuparsi di questo ritardo.

   Registrazione dell'insieme di strutture Active Directory è necessaria una sola volta nel ciclo di vita della foresta. Successivamente, gli agenti di Controller di dominio nella foresta eseguirà automaticamente le altre operazioni di manutenzione necessari. Dopo aver `Register-AzureADPasswordProtectionForest` viene eseguita correttamente per una foresta, le chiamate aggiuntive del cmdlet abbia esito positivo, ma non sono necessarie.

   Per `Register-AzureADPasswordProtectionForest` riesca, almeno un controller di dominio che esegue Windows Server 2012 o versione successiva deve essere disponibile nel dominio del server proxy. Tuttavia, il software dell'agente controller di dominio non deve essere installato su controller di dominio prima di questo passaggio.

1. Configurare il servizio proxy per la protezione con password per la comunicazione tramite un proxy HTTP.

   Se l'ambiente richiede l'uso di un proxy HTTP specifico per comunicare con Azure, usare questo metodo: Creare un *AzureADPasswordProtectionProxy.exe.config* file nella cartella Password Protection Proxy\Service %ProgramFiles%\Azure Active Directory. Includere il contenuto seguente:

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

   Se il proxy HTTP richiede l'autenticazione, aggiungere il *useDefaultCredentials* tag:

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

   In entrambi i casi, sostituire `http://yourhttpproxy.com:8080` con l'indirizzo e porta del server proxy HTTP specifico.

   Se il proxy HTTP è configurato per noi un criterio di autorizzazione, è necessario concedere l'accesso all'account del computer di Active Directory del computer che ospita il servizio proxy per la protezione con password.

   È consigliabile arrestare e riavviare il servizio proxy dopo che crea o aggiorna il *AzureADPasswordProtectionProxy.exe.config* file.

   Il servizio proxy non supporta l'uso di credenziali specifiche per la connessione a un proxy HTTP.

1. Facoltativo: Configurare il servizio proxy per la protezione con password per l'ascolto su una porta specifica.
   * Il software dell'agente controller di dominio per la protezione con password nei controller di dominio utilizza RPC su TCP per comunicare con il servizio proxy. Per impostazione predefinita, il servizio proxy è in attesa su qualsiasi endpoint RPC dinamico disponibile. Ma è possibile configurare il servizio per l'ascolto su una porta TCP specifica, se è necessario a causa di topologia di rete o i requisiti del firewall nell'ambiente in uso.
      * <a id="static" /></a>Per configurare il servizio venga eseguito con una porta statica, usare il `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

      * Per configurare il servizio venga eseguito con una porta dinamica, usare la stessa procedura, ma impostare *StaticPort* su zero:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

   > [!NOTE]
   > Il servizio proxy per la protezione con password richiede un riavvio manuale dopo ogni modifica nella configurazione della porta. Ma non è necessario riavviare il software del servizio agente controller di dominio nel controller di dominio dopo aver apportato queste modifiche alla configurazione.

   * Per eseguire una query per la configurazione corrente del servizio, usare il `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installare il servizio agente controller di dominio

   Installare il servizio agente controller di dominio per la protezione con password utilizzando la `AzureADPasswordProtectionDCAgentSetup.msi` pacchetto.

   Installazione di software o la disinstallazione, è necessario riavviare. Questo avviene perché le DLL di filtro password sono solo caricato o scaricato da un riavvio.

   È possibile installare il servizio agente controller di dominio in un computer che non è ancora un controller di dominio. In questo caso, il servizio verrà avviare ed eseguire ma rimangono inattivo fino a quando il computer viene promosso da un controller di dominio.

   È possibile automatizzare l'installazione del software tramite le procedure standard di identità del servizio gestito. Ad esempio: 

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > Il comando msiexec di esempio seguente provoca un riavvio immediato. Per evitare questo problema, usare il `/norestart` flag.

L'installazione è stata completata dopo che il software dell'agente controller di dominio viene installato in un controller di dominio e tale computer viene riavviato. Non è necessario né possibile eseguire altre configurazioni.

## <a name="multiple-forest-deployments"></a>Distribuzioni di più foreste

Non sono previsti requisiti aggiuntivi per distribuire la password di protezione di Azure Active Directory in più foreste. Ogni foresta è configurata in modo indipendente come descritto nella sezione "a foresta singola distribuzione". Ogni proxy di password di protezione può supportare solo i controller di dominio dalla foresta aggiunto a. Il software di protezione di password in qualsiasi foresta è a conoscenza del software di protezione di password che viene distribuito nelle altre foreste, indipendentemente dalle configurazioni di trust di Active Directory.

## <a name="read-only-domain-controllers"></a>Controller di dominio di sola lettura

Le modifiche o imposta la password non vengono elaborati e resi persistenti nei controller di dominio di sola lettura (RODC). Essi vengono inoltrate al controller di dominio scrivibili. Pertanto, non devi installare il software dell'agente controller di dominio nel controller.

## <a name="high-availability"></a>Disponibilità elevata

La preoccupazione principale disponibilità per la protezione con password è la disponibilità dei server proxy quando i controller di dominio in una foresta tentano di scaricare i nuovi criteri o altri dati da Azure. Ogni agente controller di dominio utilizza un algoritmo round-robin-style semplice quando si decide quale server proxy per chiamare. L'agente Ignora server proxy che non risponde. Per distribuzioni di Active Directory sempre più connesse che la replica di integrità dello stato cartella sia directory e sysvol, due server proxy è sufficiente per garantire la disponibilità. Di conseguenza il download tempestivo di nuovi criteri e altri dati. Ma è possibile distribuire ulteriori server proxy.

La progettazione del software agente controller di dominio è possibile ridurre i soliti problemi associati a disponibilità elevata. L'agente controller di dominio mantiene una cache locale dei criteri password scaricato più recentemente. Anche se tutti i server proxy non saranno più disponibili la registrazione, gli agenti di DC continuare ad applicare i criteri di password memorizzate nella cache. Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere *giorni*, non in ore o minore. Pertanto, brevi interruzioni dei server proxy in modo significativo non influiscono sulla protezione tramite password di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato installato i servizi necessari per la protezione con password Azure AD nei server in locale, [eseguire la configurazione dopo l'installazione e raccogliere informazioni di reporting](howto-password-ban-bad-on-premises-operations.md) per completare la distribuzione.

[Panoramica dei concetti relativi alla protezione password di Azure AD](concept-password-ban-bad-on-premises.md)
