---
title: Distribuire Azure AD la protezione con password-Azure Active Directory
description: Distribuire Azure AD la protezione con password per vietare le password errate in locale
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
ms.openlocfilehash: 5ad8f24c9d23e9412a4f6e4e5f97692bba2c0c39
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268676"
---
# <a name="deploy-azure-ad-password-protection"></a>Distribuire la protezione delle password di Azure AD

Ora che si è appreso [come applicare Azure ad la protezione con password per Windows Server Active Directory](concept-password-ban-bad-on-premises.md), il passaggio successivo consiste nel pianificare ed eseguire la distribuzione.

## <a name="deployment-strategy"></a>Strategia di distribuzione

Si consiglia di avviare le distribuzioni in modalità di controllo. La modalità di controllo è l'impostazione iniziale predefinita, in cui è possibile continuare a impostare le password. Le password che verrebbero bloccate vengono registrate nel registro eventi. Dopo aver distribuito i server proxy e gli agenti del controller di dominio in modalità di controllo, è necessario monitorare l'impatto che i criteri password avranno sugli utenti e sull'ambiente quando vengono applicati i criteri.

Durante la fase di controllo, molte organizzazioni scoprono che:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Spesso gli utenti usano password non sicure.
* Devono informare gli utenti sulla modifica imminente dell'applicazione della sicurezza, sulla possibile incidenza su di essi e su come scegliere password più sicure.

È anche possibile che la convalida delle password più avanzata influisca sull'automazione della distribuzione del controller di dominio Active Directory esistente. È consigliabile che almeno una promozione del controller di dominio e un'abbassamento di livello del controller di dominio si verifichino durante la valutazione del periodo di controllo, in modo da individuare in anticipo tali problemi.  Per altre informazioni, vedere:

* [Ntdsutil. exe non è in grado di impostare una password per la modalità di ripristino di servizi directory debole](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [La promozione della replica del controller di dominio non riesce a causa di una password della modalità ripristino servizi directory debole](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [L'abbassamento di livello del controller di dominio non riesce a causa di una password amministratore locale](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Dopo che la funzionalità è stata eseguita in modalità di controllo per un periodo di tempo ragionevole, è possibile cambiare la configurazione da *controllo* a Imponi per richiedere password più sicure. Il monitoraggio con lo stato attivo in questa fase è una scelta valida.

## <a name="deployment-requirements"></a>Requisiti di distribuzione

* I requisiti di licenza per la protezione Azure AD password sono disponibili nell'articolo [eliminare le password non valide nell'organizzazione](concept-password-ban-bad.md#license-requirements).
* In tutti i computer in cui verrà installato il software dell'agente di Azure AD password protection controller di dominio deve essere eseguito Windows Server 2012 o versione successiva. Questo requisito non implica che il dominio o la foresta di Active Directory deve essere anche al livello di funzionalità del dominio o della foresta di Windows Server 2012. Come indicato nei [principi di progettazione](concept-password-ban-bad-on-premises.md#design-principles), non è necessario un valore minimo di DFL o FFL per l'esecuzione del software proxy o dell'agente DC.
* In tutti i computer in cui è installato il servizio agente controller di dominio deve essere installato .NET 4,5.
* Tutti i computer in cui verrà installato il servizio proxy Azure AD Password Protection devono eseguire Windows Server 2012 R2 o versione successiva.
   > [!NOTE]
   > La distribuzione del servizio proxy è un requisito obbligatorio per la distribuzione di Azure AD la protezione delle password anche se il controller di dominio potrebbe avere connettività Internet diretta in uscita. 
   >
* In tutti i computer in cui verrà installato il servizio proxy Azure AD Password Protection deve essere installato .NET 4,7.
  .NET 4,7 dovrebbe essere già installato in un server Windows completamente aggiornato. In caso contrario, scaricare ed eseguire il programma di installazione disponibile nel [programma di installazione di .NET Framework 4,7 offline per Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Per tutti i computer, inclusi i controller di dominio, in cui sono installati Azure AD componenti di protezione delle password è necessario che sia installato il runtime di C universale. È possibile ottenere il runtime assicurandosi di avere tutti gli aggiornamenti da Windows Update. In alternativa, è possibile ottenerlo in un pacchetto di aggiornamento specifico del sistema operativo. Per ulteriori informazioni, vedere [aggiornamento per Universal C Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* La connettività di rete deve esistere tra almeno un controller di dominio in ogni dominio e almeno un server che ospita il servizio proxy per la protezione con password. Questa connettività deve consentire al controller di dominio di accedere alla porta di mapping degli endpoint RPC 135 e alla porta del server RPC nel servizio proxy. Per impostazione predefinita, la porta del server RPC è una porta RPC dinamica, ma può essere configurata per l' [utilizzo di una porta statica](#static).
* Tutti i computer in cui verrà installato il servizio proxy Azure AD password protection dovranno avere accesso di rete agli endpoint seguenti:

    |**Endpoint**|**Scopo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Richieste di autenticazione|
    |`https://enterpriseregistration.windows.net`|Funzionalità di protezione password di Azure AD|

  È necessario abilitare anche l'accesso alla rete per il set di porte e URL specificati nelle [procedure di configurazione dell'ambiente del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment). Questi passaggi di configurazione sono necessari per consentire il funzionamento del servizio Microsoft Azure AD Connect Agent Updater (questo servizio è installato side-by-side con il servizio proxy). Non è consigliabile installare side-by-side Azure AD proxy di protezione con password e proxy di applicazione nello stesso computer, a causa di incompatibilità tra le versioni del software di Microsoft Azure AD Connect Agent Updater.
* Tutti i computer che ospitano il servizio proxy per la protezione con password devono essere configurati in modo da concedere ai controller di dominio la possibilità di accedere al servizio proxy. Questa operazione viene controllata tramite l'assegnazione dei privilegi "accedi al computer dalla rete".
* Tutti i computer che ospitano il servizio proxy per la protezione con password devono essere configurati per consentire il traffico HTTP di TLS 1,2 in uscita.
* Un account amministratore globale per registrare il servizio proxy per la protezione con password e la foresta con Azure AD.
* Un account con privilegi di amministratore di dominio Active Directory nel dominio radice della foresta per registrare la foresta di Windows Server Active Directory con Azure AD.
* Qualsiasi dominio Active Directory che esegue il software del servizio agente di controller di dominio deve utilizzare la replica di file system distribuito (DFSR) per la replica SYSVOL.

  Se il dominio non sta già usando DFSR, è necessario eseguirne la migrazione per usare DFSR prima di installare Azure AD la protezione delle password. Per ulteriori informazioni, vedere il collegamento seguente:

  [Guida alla migrazione della replica SYSVOL: Da FRS a Replica DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Il software dell'agente del controller di dominio Azure AD Password Protection verrà attualmente installato nei controller di dominio in domini che usano ancora FRS (la tecnologia predecessore per DFSR) per la replica SYSVOL, ma il software non funzionerà correttamente in questo ambiente. Gli effetti collaterali negativi aggiuntivi includono i singoli file che non riescono a eseguire la replica e le procedure di ripristino di SYSVOL sembrano avere esito positivo ma non riescono a replicare tutti i file. È consigliabile eseguire la migrazione del dominio per usare DFSR il prima possibile, sia per i vantaggi intrinseci di DFSR che per sbloccare la distribuzione di Azure AD la protezione delle password. Le versioni future del software verranno disabilitate automaticamente durante l'esecuzione in un dominio che continua a usare il servizio Replica file.

* Il servizio di distribuzione delle chiavi deve essere abilitato in tutti i controller di dominio nel dominio che esegue Windows Server 2012. Per impostazione predefinita, questo servizio viene abilitato tramite l'avvio manuale del trigger.

## <a name="single-forest-deployment"></a>Distribuzione a foresta singola

Il diagramma seguente illustra il modo in cui i componenti di base di Azure AD la protezione delle password interagiscono in un ambiente Active Directory locale.

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

È consigliabile esaminare il funzionamento del software prima di distribuirlo. Vedere [panoramica concettuale di Azure ad Password Protection](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Scaricare il software

Per la protezione Azure AD password sono disponibili due programmi di installazione necessari. Sono disponibili nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installare e configurare il servizio proxy per la protezione con password

1. Scegliere uno o più server per ospitare il servizio proxy per la protezione con password.
   * Ogni servizio di questo tipo può fornire solo criteri password per una singola foresta. Il computer host deve essere aggiunto a un dominio nella foresta. Sono supportati entrambi i domini radice e figlio. È necessaria la connettività di rete tra almeno un controller di dominio in ogni dominio della foresta e il computer di protezione con password.
   * È possibile eseguire il servizio proxy in un controller di dominio per il test. Il controller di dominio richiede quindi la connettività Internet, che può costituire un problema di sicurezza. Questa configurazione è consigliata solo per i test.
   * Per la ridondanza sono consigliate almeno due server proxy. Vedere [disponibilità elevata](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installare il servizio proxy Azure ad Password Protection usando il `AzureADPasswordProtectionProxySetup.exe` programma di installazione del software.
   * Non è necessario riavviare dopo l'installazione del software. L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Per evitare un errore di installazione, è necessario che il servizio Windows Firewall sia in esecuzione prima di installare il pacchetto AzureADPasswordProtectionProxySetup. msi. Se Windows Firewall è configurato per non essere eseguito, la soluzione alternativa consiste nell'abilitare ed eseguire temporaneamente il servizio firewall durante l'installazione. Il software proxy non ha dipendenze specifiche da Windows Firewall dopo l'installazione. Se si usa un firewall di terze parti, è comunque necessario configurarlo per soddisfare i requisiti di distribuzione. Che includono l'accesso in ingresso alla porta 135 e la porta del server proxy RPC. Vedere [requisiti di distribuzione](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Aprire una finestra di PowerShell come amministratore.
   * Il software proxy per la protezione con password include un nuovo modulo di PowerShell, *AzureADPasswordProtection*. I passaggi seguenti eseguono diversi cmdlet da questo modulo di PowerShell. Importare il nuovo modulo come segue:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Per verificare che il servizio sia in esecuzione, usare il comando di PowerShell seguente:

      `Get-Service AzureADPasswordProtectionProxy | fl` (Indici per tabelle con ottimizzazione per la memoria).

     Il risultato dovrebbe mostrare **lo stato** "Running".

1. Registrare il proxy.
   * Al termine del passaggio 3, il servizio proxy è in esecuzione nel computer. Ma il servizio non ha ancora le credenziali necessarie per comunicare con Azure AD. La registrazione con Azure AD è obbligatoria:

     `Register-AzureADPasswordProtectionProxy`

     Questo cmdlet richiede le credenziali di amministratore globale per il tenant di Azure. Sono necessari anche i privilegi di amministratore di dominio Active Directory locali nel dominio radice della foresta. Dopo che il comando ha avuto esito positivo una volta per un servizio proxy, le chiamate aggiuntive verranno riuscite, ma non sono necessarie.

      Il `Register-AzureADPasswordProtectionProxy` cmdlet supporta le tre modalità di autenticazione seguenti.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi server core. Usare invece una delle modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe avere esito negativo se è abilitata la configurazione sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare il proxy e quindi riabilitarlo.

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Per completare l'autenticazione, seguire le istruzioni visualizzate in un altro dispositivo.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità ha esito negativo se per l'account è necessario Azure Multi-Factor Authentication. In tal caso, usare una delle due modalità di autenticazione precedenti o usare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È anche possibile vedere l'autenticazione a più fattori necessaria se la registrazione del dispositivo di Azure, che viene usata dietro le quinte da Azure AD la protezione delle password, è stata configurata in modo da richiedere a livello globale Per risolvere questo problema, è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è possibile anche disattivare temporaneamente il requisito di autenticazione a più fattori per la registrazione dei dispositivi di Azure. A tale scopo, passare al portale di gestione di Azure, passare a Azure Active Directory, quindi dispositivi, impostazioni dispositivo, quindi impostare "Richiedi autenticazione a più fattori per aggiungere dispositivi" a No. Assicurarsi di riconfigurare nuovamente questa impostazione su Sì al termine della registrazione.
        >
        > È consigliabile ignorare i requisiti dell'autenticazione a più fattori solo a scopo di test.

       Attualmente non è necessario specificare il parametro *-ForestCredential* , riservato per le funzionalità future.

   La registrazione del servizio proxy per la protezione con password è necessaria solo una volta nel corso della durata del servizio. Successivamente, il servizio proxy eseguirà automaticamente qualsiasi altra manutenzione necessaria.

   > [!TIP]
   > È possibile che si verifichi un ritardo notevole prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

1. Registrare la foresta.
   * È necessario inizializzare la foresta Active Directory locale con le credenziali necessarie per comunicare con Azure tramite il `Register-AzureADPasswordProtectionForest` cmdlet di PowerShell. Il cmdlet richiede le credenziali di amministratore globale per il tenant di Azure. Richiede anche privilegi di amministratore dell'organizzazione Active Directory locale. Questo passaggio viene eseguito una volta per ogni foresta.

      Il `Register-AzureADPasswordProtectionForest` cmdlet supporta le tre modalità di autenticazione seguenti.

     * Modalità di autenticazione interattiva:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Questa modalità non funziona nei sistemi operativi server core. Usare invece una delle due modalità di autenticazione seguenti. Inoltre, questa modalità potrebbe avere esito negativo se è abilitata la configurazione sicurezza avanzata di Internet Explorer. La soluzione alternativa consiste nel disabilitare tale configurazione, registrare la foresta e quindi riabilitarla.  

     * Modalità di autenticazione basata sul codice del dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Per completare l'autenticazione, seguire le istruzioni visualizzate in un altro dispositivo.

     * Modalità di autenticazione automatica (basata su password):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Questa modalità ha esito negativo se per l'account è necessario Azure Multi-Factor Authentication. In tal caso, usare una delle due modalità di autenticazione precedenti o usare invece un account diverso che non richiede l'autenticazione a più fattori.
        >
        > È anche possibile vedere l'autenticazione a più fattori necessaria se la registrazione del dispositivo di Azure, che viene usata dietro le quinte da Azure AD la protezione delle password, è stata configurata in modo da richiedere a livello globale Per risolvere questo problema, è possibile usare un account diverso che supporta l'autenticazione a più fattori con una delle due modalità di autenticazione precedenti oppure è possibile anche disattivare temporaneamente il requisito di autenticazione a più fattori per la registrazione dei dispositivi di Azure. A tale scopo, passare al portale di gestione di Azure, passare a Azure Active Directory, quindi dispositivi, impostazioni dispositivo, quindi impostare "Richiedi autenticazione a più fattori per aggiungere dispositivi" a No. Assicurarsi di riconfigurare nuovamente questa impostazione su Sì al termine della registrazione.
        >
        > È consigliabile ignorare i requisiti dell'autenticazione a più fattori solo a scopo di test.

       Questi esempi hanno esito positivo solo se l'utente attualmente connesso è anche un amministratore di dominio Active Directory per il dominio radice. In caso contrario, è possibile specificare credenziali di dominio alternative tramite il parametro *-ForestCredential* .

   > [!NOTE]
   > Se nell'ambiente in uso sono installati più server proxy, non è importante il server proxy usato per registrare la foresta.
   >
   > [!TIP]
   > È possibile che si verifichi un ritardo notevole prima del completamento la prima volta che questo cmdlet viene eseguito per un tenant di Azure specifico. A meno che non venga segnalato un errore, non preoccuparti di questo ritardo.

   La registrazione della foresta Active Directory è necessaria solo una volta nella durata della foresta. Successivamente, gli agenti del controller di dominio nella foresta eseguiranno automaticamente qualsiasi altra manutenzione necessaria. Dopo `Register-AzureADPasswordProtectionForest` l'esecuzione corretta di una foresta, le chiamate aggiuntive del cmdlet hanno esito positivo, ma non sono necessarie.

   Per `Register-AzureADPasswordProtectionForest` avere esito positivo, almeno un controller di dominio che esegue Windows Server 2012 o versioni successive deve essere disponibile nel dominio del server proxy. Il software dell'agente controller di dominio, tuttavia, non deve essere installato nei controller di dominio prima di questo passaggio.

1. Configurare il servizio proxy per la comunicazione con la password tramite un proxy HTTP.

   Se l'ambiente richiede l'uso di un proxy HTTP specifico per comunicare con Azure, usare questo metodo: Creare un file *AzureADPasswordProtectionProxy. exe. config* nella cartella%ProgramFiles%\Azure ad Password Protection Proxy\Service Includere il contenuto seguente:

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

   In entrambi i casi, `http://yourhttpproxy.com:8080` sostituire con l'indirizzo e la porta del server proxy HTTP specifico.

   Se il proxy HTTP è configurato per l'utilizzo di un criterio di autorizzazione, è necessario concedere l'accesso all'account del computer Active Directory del computer che ospita il servizio proxy per la protezione con password.

   Si consiglia di arrestare e riavviare il servizio proxy dopo la creazione o l'aggiornamento del file *AzureADPasswordProtectionProxy. exe. config* .

   Il servizio proxy non supporta l'utilizzo di credenziali specifiche per la connessione a un proxy HTTP.

1. Facoltativo: Configurare il servizio proxy per la protezione delle password in modo che sia in ascolto su una porta specifica.
   * Il software dell'agente DC per la protezione delle password nei controller di dominio utilizza RPC su TCP per comunicare con il servizio proxy. Per impostazione predefinita, il servizio proxy è in ascolto su tutti gli endpoint RPC dinamici disponibili. Tuttavia, è possibile configurare il servizio in modo che sia in ascolto su una porta TCP specifica, se necessario, a causa della topologia di rete o dei requisiti del firewall nell'ambiente in uso.
      * <a id="static" /></a>Per configurare il servizio per l'esecuzione in una porta statica, utilizzare `Set-AzureADPasswordProtectionProxyConfiguration` il cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

      * Per configurare il servizio per l'esecuzione in una porta dinamica, utilizzare la stessa procedura, ma impostare *StaticPort* di nuovo su zero:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

   > [!NOTE]
   > Il servizio proxy per la protezione con password richiede un riavvio manuale dopo qualsiasi modifica nella configurazione della porta. Tuttavia, dopo aver apportato queste modifiche alla configurazione, non è necessario riavviare il software del servizio agente di controller di dominio nei controller di dominio.

   * Per eseguire una query per la configurazione corrente del servizio, usare `Get-AzureADPasswordProtectionProxyConfiguration` il cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installare il servizio agente di controller di dominio

   Installare il servizio agente di controller di dominio per la protezione `AzureADPasswordProtectionDCAgentSetup.msi` con password utilizzando il pacchetto.

   Per l'installazione del software o per l'annullamento dell'installazione è necessario riavviare. Questo perché le dll del filtro password vengono caricate o scaricate solo da un riavvio.

   È possibile installare il servizio DC Agent in un computer che non è ancora un controller di dominio. In questo caso, il servizio verrà avviato ed eseguito ma rimarrà inattivo fino a quando il computer non viene promosso a controller di dominio.

   È possibile automatizzare l'installazione del software utilizzando le procedure MSI standard. Esempio:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   È possibile omettere `/norestart` il flag se si preferisce che il programma di installazione riavvii automaticamente il computer.

L'installazione è stata completata dopo l'installazione del software dell'agente controller di dominio in un controller di dominio e il riavvio del computer. Non è necessario né possibile eseguire altre configurazioni.

## <a name="upgrading-the-proxy-agent"></a>Aggiornamento dell'agente proxy

Quando è disponibile una versione più recente del software proxy Azure ad Password Protection, l'aggiornamento viene eseguito eseguendo la versione più recente del programma `AzureADPasswordProtectionProxySetup.exe` di installazione software. La versione più recente del software è disponibile nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del software proxy. il programma di installazione eseguirà un aggiornamento sul posto. Quando si aggiorna il software proxy, non è necessario riavviare il computer. È possibile automatizzare l'aggiornamento del software utilizzando le procedure MSI standard, `AzureADPasswordProtectionProxySetup.exe /quiet`ad esempio:.

L'agente proxy supporta l'aggiornamento automatico. L'aggiornamento automatico usa il servizio Microsoft Azure AD Connect Agent Updater, installato side-by-side con il servizio proxy. L'aggiornamento automatico è attivato per impostazione predefinita e può essere abilitato o disabilitato `Set-AzureADPasswordProtectionProxyConfiguration` usando il cmdlet. È possibile eseguire query sull'impostazione corrente usando il `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. Microsoft consiglia di lasciare abilitata l'aggiornamento automatico.

Il `Get-AzureADPasswordProtectionProxy` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti gli agenti proxy attualmente installati in una foresta.

## <a name="upgrading-the-dc-agent"></a>Aggiornamento dell'agente del controller di dominio

Quando è disponibile una versione più recente del software dell'agente del controller di dominio Azure ad Password Protection, l'aggiornamento viene eseguito eseguendo la versione `AzureADPasswordProtectionDCAgentSetup.msi` più recente del pacchetto software. La versione più recente del software è disponibile nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Non è necessario disinstallare la versione corrente del software dell'agente del controller di dominio. il programma di installazione eseguirà un aggiornamento sul posto. Quando si aggiorna il software dell'agente del controller di dominio, è sempre necessario riavviare il sistema. questa situazione è causata dal comportamento principale di Windows. 

È possibile automatizzare l'aggiornamento del software utilizzando le procedure MSI standard, `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`ad esempio:.

È possibile omettere `/norestart` il flag se si preferisce che il programma di installazione riavvii automaticamente il computer.

Il `Get-AzureADPasswordProtectionDCAgent` cmdlet può essere utilizzato per eseguire una query sulla versione software di tutti gli agenti DC attualmente installati in una foresta.

## <a name="multiple-forest-deployments"></a>Distribuzioni di più foreste

Non sono previsti requisiti aggiuntivi per distribuire la password di protezione di Azure Active Directory in più foreste. Ogni foresta è configurata in modo indipendente come descritto nella sezione "distribuzione a foresta singola". Ogni proxy di protezione con password può supportare solo i controller di dominio della foresta a cui è aggiunto. Il software di protezione delle password in qualsiasi foresta non è a conoscenza del software di protezione delle password distribuito in altre foreste, indipendentemente dalle configurazioni di trust Active Directory.

## <a name="read-only-domain-controllers"></a>Controller di dominio di sola lettura

Le modifiche/set di password non vengono elaborate e rese permanente nei controller di dominio di sola lettura (RODC). Vengono quindi trasmessi ai controller di dominio scrivibili. Quindi, non è necessario installare il software dell'agente controller di dominio in RODC.

## <a name="high-availability"></a>Disponibilità elevata

Il problema di disponibilità principale per la protezione delle password è la disponibilità dei server proxy quando i controller di dominio in una foresta tentano di scaricare nuovi criteri o altri dati da Azure. Ogni agente del controller di dominio usa un semplice algoritmo di tipo Round Robin per decidere quale server proxy chiamare. L'agente ignora i server proxy che non rispondono. Per la maggior parte delle distribuzioni di Active Directory completamente connesse con una replica corretta dello stato di directory e di cartella SYSVOL, due server proxy sono sufficienti per garantire la disponibilità. Ciò comporta il download tempestivo dei nuovi criteri e di altri dati. Ma è possibile distribuire altri server proxy.

La progettazione del software dell'agente di controller di dominio attenua i normali problemi associati alla disponibilità elevata. L'agente del controller di dominio gestisce una cache locale dei criteri password scaricati più di recente. Anche se tutti i server proxy registrati diventano non disponibili, gli agenti del controller di dominio continuano a applicare i criteri password memorizzati nella cache. Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere di giorni, non di ore o meno. Quindi, le brevi interruzioni dei server proxy non influiscono in modo significativo Azure AD la protezione delle password.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati installati i servizi necessari per Azure AD la protezione con password nei server locali, [eseguire la configurazione post-installazione e raccogliere le informazioni di report](howto-password-ban-bad-on-premises-operations.md) per completare la distribuzione.

[Panoramica dei concetti relativi alla protezione password di Azure AD](concept-password-ban-bad-on-premises.md)
