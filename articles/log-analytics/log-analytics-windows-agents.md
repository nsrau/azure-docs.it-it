---
title: Connettere computer Windows a Log Analytics di Azure | Microsoft Docs
description: Questo articolo illustra i passaggi necessari per connettere i computer Windows dell'infrastruttura locale al servizio Log Analytics usando una versione personalizzata di Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 48a0eaeb10d406d551c9e5870edde06809bd7544
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Connettere computer Windows al servizio Log Analytics in Azure

Questo articolo illustra i passaggi necessari per connettere i computer Windows dell'infrastruttura locale alle aree di lavoro di OMS usando una versione personalizzata di Microsoft Monitoring Agent (MMA). È necessario installare e connettere gli agenti per tutti i computer da caricare affinché possano inviare dati al servizio Log Analytics e visualizzare ed eseguire operazioni su tali dati. Ogni agente può inviare report a più aree di lavoro.

È possibile installare gli agenti tramite il programma di installazione, la riga di comando o con Configurazione dello stato desiderato in Automazione di Azure.  

>[!NOTE]
Per le macchine virtuali eseguite in Azure, è possibile semplificare l'installazione usando l'[estensione macchina virtuale](log-analytics-azure-vm-extension.md).

Nei computer con connettività a Internet, l'agente usa la connessione a Internet per inviare dati a OMS. Per i computer che non hanno connettività Internet, è possibile usare un proxy o il [gateway di OMS](log-analytics-oms-gateway.md).

La connessione dei computer Windows a OMS è semplice e richiede solo tre passaggi:

1. Scaricare il file di installazione dell'agente dal portale di OMS
2. Installare l'agente usando il metodo che scelto
3. Configurare l'agente o aggiungere altre aree di lavoro, se necessario

Il diagramma seguente illustra la relazione tra i computer Windows e OMS dopo l'installazione e la configurazione degli agenti.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Se i criteri di protezione IT non consentono ai computer nella rete di connettersi a Internet, è possibile configurare i computer per connettersi al Gateway OMS. Per altre informazioni e procedure per la configurazione dei server per la comunicazione tramite un gateway OMS con il servizio OMS, vedere [Connettere computer a OMS usando il gateway OMS](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Requisiti di sistema e configurazione richiesta
Prima di installare o distribuire gli agenti, esaminare i dettagli seguenti per assicurarsi che i requisiti siano soddisfatti.

- È possibile installare MMA per OMS solo in computer che eseguono Windows Server 2008 Service Pack 1 o versione successiva oppure Windows 7 SP1 o versione successiva.
- È necessaria una sottoscrizione di Azure.  Per altre informazioni, vedere [Introduzione a Log Analytics](log-analytics-get-started.md).
- Ogni computer Windows deve potersi connettere a Internet tramite il protocollo HTTPS o il gateway di OMS. Questa connessione può essere diretta, tramite un proxy o tramite il gateway di OMS.
- È possibile installare MMA per OMS in computer autonomi, server e macchine virtuali. Per connettere a OMS le macchine virtuali ospitate da Azure, vedere l'articolo [Connettere macchine virtuali di Azure a Log Analytics](log-analytics-azure-vm-extension.md).
- L'agente deve usare la porta TCP 443 per diverse risorse.

### <a name="network"></a>Rete

Per far sì che gli agenti di Windows si connettano e si registrino con il servizio OMS, devono avere accesso alle risorse di rete, compresi gli URL di dominio e i numeri di porta.

- Per i server proxy, è necessario assicurarsi che le risorse del server proxy appropriate siano configurate nelle impostazioni dell'agente.
- Per i firewall che limitano l'accesso a Internet, è necessario che l'utente o gli ingegneri di rete configurino il firewall per consentire l'accesso a OMS. Non è necessaria alcuna azione sulle impostazioni dell'agente.

Nella tabella seguente vengono visualizzate le risorse necessarie per la comunicazione.

>[!NOTE]
>Alcune delle risorse seguenti fanno riferimento a Operational Insights, che era il nome precedente di Log Analytics.

| Risorsa agente | Porte | Ignorare l'analisi HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sì |
| *.oms.opinsights.azure.com | 443 | Sì |
| *.blob.core.windows.net | 443 | Sì |
| *.azure-automation.net | 443 | Sì |



## <a name="download-the-agent-setup-file-from-oms"></a>Per scaricare il file di installazione dell'agente da OMS
1. Nella pagina **Panoramica** del portale di OMS fare clic sul riquadro **Impostazioni**.  Scegliere la scheda **Connected Sources** nella parte superiore.  
    ![Scheda Origini connesse](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Fare clic su **Server Windows** e quindi fare clic su **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
3. A destra di **ID area di lavoro**fare clic sull'icona di copia e incollare l'ID nel Blocco note.
4. A destra di **Chiave primaria**fare clic sull'icona di copia e incollare la chiave nel Blocco note.     

## <a name="install-the-agent-using-setup"></a>Installare gli agenti con il programma di installazione
1. Eseguire il programma di installazione per installare l'agente in un computer da gestire.
2. Nella pagina di benvenuto fare clic su **Avanti**.
3. Nella pagina Condizioni di licenza leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina della cartella di destinazione modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina delle opzioni di installazione dell'agente è possibile scegliere di connettere l'agente ad Azure Log Analytics (OMS) o Operations Manager oppure di lasciare vuote le opzioni se si vuole configurare l'agente in un secondo momento. Fare clic su **Avanti**.   
    - Se si sceglie di connettersi ad Azure Log Analytics (OMS), incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note durante la procedura precedente e quindi fare clic su **Avanti**.  
        ![incollare ID area di lavoro e chiave primaria](./media/log-analytics-windows-agents/connect-workspace.png)
    - Se si sceglie di connettersi a Operations Manager, digitare il **nome del gruppo di gestione**, il nome del **server di gestione** e la **porta del server di gestione** e quindi fare clic su **Avanti**. Nella pagina Account azione agente scegliere l'Account di sistema locale o un account di dominio locale e quindi fare clic su **Avanti**.  
        ![configurazione del gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![account azione agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Nella pagina Pronto per l'installazione rivedere le scelte effettuate e quindi fare clic su **Installa**.
7. Nella pagina Configurazione completata fare clic su **Fine**.
8. Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso a Operational Insights (OMS). Quando si è connessi a OMS, l'agente visualizza un messaggio nel quale è indicato che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite.**

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

È possibile usare la procedura seguente per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite il Pannello di controllo. È necessario ripetere la procedura per ogni server. Se è necessario configurare molti server, può risultare più semplice usare uno script per automatizzare il processo. In questo caso, vedere la procedura seguente [Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite uno script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite il Pannello di controllo
1. Aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent**.
3. Fare clic sulla scheda **Impostazioni proxy**.  
    ![Scheda Impostazioni proxy](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Selezionare **Usa server proxy** e digitare l'URL e il numero di porta, se necessario, in modo analogo all'esempio illustrato. Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per accedere al server proxy.


### <a name="verify-agent-connectivity-to-oms"></a>Verificare la connettività dell'agente a OMS

È possibile verificare facilmente se gli agenti comunicano con OMS tramite la procedura seguente:

1.  Nel computer con l'agente di Windows, aprire il pannello di controllo.
2.  Aprire Microsoft Monitoring Agent.
3.  Fare clic sulla scheda Azure Log Analytics (OMS).
4.  Nella colonna dello stato, si dovrebbe vedere che l'agente si è connesso correttamente al servizio Operations Management Suite.

![agente connesso](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite uno script
Copiare l'esempio seguente, aggiornarlo con le informazioni specifiche per l'ambiente, salvarlo con un'estensione di file PS1 ed eseguire lo script in ogni computer che si connette direttamente al servizio OMS.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>Installare l'agente usando la riga di comando
- Modificare e usare il seguente esempio per installare l'agente mediante la riga di comando. L'esempio esegue un'installazione completamente automatica.

    >[!NOTE]
    Per aggiornare un agente è necessario usare l'API di scripting di Log Analytics. La procedura per aggiornare un agente è descritta nella sezione successiva.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

L'agente usa IExpress come programma di autoestrazione tramite il comando `/c`. È possibile vedere le opzioni della riga di comando in [Command-line switches for IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) (Opzioni della riga di comando di IExpress) e quindi aggiornare l'esempio in base alle esigenze.

|Opzioni specifiche di MMA                   |Note         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configura l'agente per fare riferimento a un'area di lavoro                |
|OPINSIGHTS_WORKSPACE_ID                | Id dell'area di lavoro (guid) per l'area di lavoro da aggiungere                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chiave dell'area di lavoro usata per autenticarsi inizialmente nell'area di lavoro |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Specificare l'ambiente cloud in cui si trova l'area di lavoro <br> 0 = Cloud commerciale di Azure (impostazione predefinita) <br> 1 = Azure per enti pubblici |
|OPINSIGHTS_PROXY_URL               | URI per il proxy da usare |
|OPINSIGHTS_PROXY_USERNAME               | Nome utente per accedere a un proxy autenticato |
|OPINSIGHTS_PROXY_PASSWORD               | Password per accedere a un proxy autenticato |

>[!NOTE]
Per evitare di raggiungere il limite di lunghezza della riga di comando di IExpress, installare l'agente con nessuna area di lavoro configurata e quindi usare uno script per impostare la configurazione per l'area di lavoro.

>[!NOTE]
Se viene visualizzato un `Command line option syntax error.` quando si usa il parametro `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE`, è possibile usare la seguente soluzione alternativa:
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Aggiungere un'area di lavoro usando uno script
Aggiornare un'area di lavoro usando l'API di scripting di Log Analytics con l'esempio seguente:

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Per aggiungere un'area di lavoro in Azure per enti pubblici statunitensi, usare il seguente script di esempio:
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Se in precedenza sono stati usati la riga di comando o uno script per installare o configurare l'agente, `EnableAzureOperationalInsights` è stato sostituito da `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installare l'agente usando DSC in Automazione di Azure

È possibile utilizzare lo script di esempio riportato di seguito per installare l'agente utilizzando DSC in Automazione di Azure. Nell'esempio viene installato l'agente a 64 bit, identificato dal valore `URI`. È possibile utilizzare anche la versione a 32 bit sostituendo il valore dell'URI. Gli URI per entrambe le versioni sono:

- Agente di Windows a 64 bit - https://go.microsoft.com/fwlink/?LinkId=828603
- Agente di Windows a 32 bit - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
La procedura e lo script di esempio riportati di seguito non determinano l'aggiornamento di un agente esistente.

1. Importare il modulo xPSDesiredStateConfiguration da [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Automazione di Azure.  
2.  Creare gli asset variabili di Automazione di Azure per *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Impostare *OPSINSIGHTS_WS_ID* sull'ID dell'area di lavoro di Log Analytics di OMS e impostare *OPSINSIGHTS_WS_KEY* sulla chiave primaria dell'area di lavoro.
3.  Usare lo script seguente e salvarlo come MMAgent.ps1
4.  Modificare e quindi usare l'esempio seguente per installare l'agente usando DSC in Automazione di Azure. Importare MMAgent.ps1 in Automazione di Azure tramite l'interfaccia di Automazione di Azure o il cmdlet.
5.  Assegnare un nodo alla configurazione. Entro 15 minuti il nodo controlla la configurazione e viene effettuato il push di MMA al nodo.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Ottenere il valore di ProductId più recente

Il `ProductId value` nello script MMAgent.ps1 di è univoco per ogni versione dell'agente. Quando viene pubblicata una versione aggiornata di ogni agente, il valore di ProductId cambia. Pertanto, quando il ProductId viene modificato in un secondo momento, è possibile individuare la versione dell'agente utilizzando un semplice script. Dopo aver installato la versione più recente dell'agente in un server di prova, è possibile utilizzare lo script seguente per ottenere il valore di ProductId installato. Utilizzando il valore di ProductId più recente, è possibile aggiornare il valore nello script MMAgent.ps1.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurare manualmente un agente o aggiungere nuove aree di lavoro
Se gli agenti sono stati installati ma non configurati o se si vuole che l'agente invii report a più aree di lavoro, è possibile usare le informazioni seguenti per abilitare un agente o riconfigurarlo. Dopo aver configurato l'agente, questo verrà registrato con il servizio agente e otterrà le informazioni di configurazione necessarie nonché i Management Pack contenenti informazioni sulla soluzione.

1. Dopo avere installato Microsoft Monitoring Agent, aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Azure Log Analytics (OMS)**.   
3. Fare clic su **Aggiungi** per aprire la casella **Aggiungi un'area di lavoro di Log Analytics**.
4. Incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)**, copiati nel Blocco note durante una procedura precedente relativa all'area di lavoro da aggiungere e quindi fare clic su **OK**.  
    ![configurazione di Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

Al termine della raccolta dei dati dai computer monitorati dall'agente, il numero di computer monitorati da OMS verrà visualizzato nel portale di OMS nella scheda **Origini connesse** della sezione **Impostazioni** come **server connessi**.


## <a name="to-disable-an-agent"></a>Per disabilitare l'agente
1. Dopo aver installato l'agente, aprire il **Pannello di controllo**.
2. Aprire Microsoft Monitoring Agent e fare clic sulla scheda **Azure Log Analytics (OMS)** .
3. Selezionare un'area di lavoro e quindi fare clic su **Rimuovi**. Ripetere questo passaggio per tutte le altre aree di lavoro.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Configurare facoltativamente gli agenti per l'invio di report a un gruppo di gestione di Operations Manager

Se si usa Operations Manager nell'infrastruttura IT, è anche possibile usare l'agente MMA come agente di Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Per configurare gli agenti MMA per l'invio di report a un gruppo di gestione di Operations Manager
1.  Nel computer in cui è installato l'agente aprire **Pannello di controllo**.  
2.  Aprire **Microsoft Monitoring Agent** e fare clic sulla scheda **Operations Manager**.  
    ![Microsoft Monitoring Agent scheda Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3.  Se i server di Operations Manager sono configurati per l'integrazione con Active Directory, fare clic su **Aggiorna automaticamente assegnazioni gruppi di gestione da Servizi di dominio Active Directory**.
4.  Fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi gruppo di gestione**.  
    ![Microsoft Monitoring Agent Aggiungi gruppo di gestione](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  In **Nome gruppo di gestione** digitare il nome del gruppo di gestione.
6.  Nella casella **Server di gestione primario** digitare il nome computer del server di gestione primario.
7.  Nella casella **Porta server di gestione** digitare il numero di porta TCP.
8.  In **Account azione agente**scegliere l'account di sistema locale o un account di dominio locale.
9.  Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi gruppo di gestione** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di Microsoft Monitoring Agent**.


## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità e raccogliere i dati.

