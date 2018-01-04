---
title: Gestire l'agente di Log di Azure Analitica | Documenti Microsoft
description: "In questo articolo vengono descritte le attività di gestione diverse che verranno in genere eseguite durante il ciclo di vita di Microsoft Monitoring Agent (MMA) distribuito in un computer."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: e917b64c32c2f99c9a8b0397e70a32b2dbb2e3cf
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gestione e manutenzione dell'agente di Log Analitica per Windows e Linux

Dopo la distribuzione iniziale dell'agente di Windows o Linux per Log Analitica, è necessario riconfigurare l'agente in base alla situazione o rimuoverlo dal computer, se ha raggiunto la fase di ritiro nel ciclo di vita.  È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce errore operativi e spese.

## <a name="adding-or-removing-a-workspace"></a>Aggiunta o rimozione di un'area di lavoro 

### <a name="windows-agent"></a>Agente di Windows

#### <a name="update-settings-from-control-panel"></a>Aggiornare le impostazioni dal Pannello di controllo

1. Accedere al computer con un account che disponga dei diritti amministrativi.
2. Aprire il **Pannello di controllo**.
3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic su di **Analitica Log di Azure (OMS)** scheda.
4. Se la rimozione di un'area di lavoro, selezionarlo e quindi fare clic su **rimuovere**. Ripetere questo passaggio per qualsiasi altra area di lavoro in cui l'agente per interrompere la segnalazione di.
5. Se l'aggiunta di un'area di lavoro, fare clic su **Aggiungi** e scegliere il **aggiungere un'area di lavoro Analitica Log** la finestra di dialogo, incollare l'ID dell'area di lavoro e chiave dell'area di lavoro (chiave primaria). Se il computer è necessario segnalare all'area di lavoro Log Analitica nel cloud di Azure per enti pubblici, selezionare Azure del governo dall'elenco a discesa Cloud di Azure. 
6. Fare clic su **OK** per salvare le modifiche.

#### <a name="remove-a-workspace-using-powershell"></a>Rimuovere un'area di lavoro tramite PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Aggiungere un'area di lavoro in Azure esterna tramite PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Aggiungere un'area di lavoro in Azure per governo tramite PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se si utilizza la riga di comando o script in precedenza per installare o configurare l'agente, `EnableAzureOperationalInsights` è stato sostituito da `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Aggiornare le impostazioni del proxy 
Per configurare l'agente per comunicare con il servizio tramite un server proxy o [OMS Gateway](log-analytics-oms-gateway.md) dopo la distribuzione, utilizzare uno dei metodi seguenti per completare questa attività.

### <a name="windows-agent"></a>Agente di Windows

#### <a name="update-settings-using-control-panel"></a>Aggiornare le impostazioni dal Pannello di controllo

1. Accedere al computer con un account che disponga dei diritti amministrativi.
2. Aprire il **Pannello di controllo**.
3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic su di **le impostazioni del Proxy** scheda.
4. Fare clic su **utilizza un server proxy** e fornire l'URL e il numero di server proxy o gateway di porta. Se il server proxy o Gateway OMS richiede l'autenticazione, digitare il nome utente e la password per autenticare e quindi fare clic su **OK**. 

#### <a name="update-settings-using-powershell"></a>Aggiornare le impostazioni di utilizzo di PowerShell 

Copiare il codice di PowerShell di esempio seguente, aggiornarlo con le informazioni specifiche per l'ambiente e salvarlo con un'estensione di file PS1.  Eseguire lo script in ogni computer che si connette direttamente al servizio Registro Analitica.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

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
```  

### <a name="linux-agent"></a>Agente Linux
Se è necessario comunicare attraverso un server proxy o Gateway OMS Log Analitica i computer Linux, eseguire la procedura seguente.  Il valore di configurazione proxy ha la sintassi seguente `[protocol://][user:password@]proxyhost[:port]`.

1. Modificare il file `/etc/opt/microsoft/omsagent/proxy.conf` eseguendo i comandi seguenti e modificare i valori nelle impostazioni specifiche.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Riavviare l'agente eseguendo il comando seguente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Disinstallazione dell'agente
Utilizzare una delle seguenti procedure per disinstallare l'agente di Windows o Linux tramite la procedura guidata da riga di comando o l'installazione.

### <a name="windows-agent"></a>Agente di Windows

#### <a name="uninstall-from-control-panel"></a>Disinstallazione dal Pannello di controllo
1. Accedere al computer con un account che disponga dei diritti amministrativi.  
2. In **Pannello di controllo**, fare clic su **programmi e funzionalità**.
3. In **programmi e funzionalità**, fare clic su **Microsoft Monitoring Agent**, fare clic su **Disinstalla**, quindi fare clic su **Sì**.

>[!NOTE]
>È possibile anche eseguire l'installazione guidata agente facendo doppio clic su **MMASetup -<platform>.exe**, che è disponibile per il download da un'area di lavoro nel portale di Azure.

#### <a name="uninstall-from-the-command-line"></a>Disinstallazione dalla riga di comando
Il file scaricato per l'agente è un pacchetto di installazione autonomo creato con IExpress.  Il programma di installazione per l'agente e i file di supporto sono contenuti nel pacchetto e devono essere estratti per disinstallare correttamente tramite riga di comando mostrata nell'esempio seguente. 

1. Accedere al computer con un account che disponga dei diritti amministrativi.  
2. Per estrarre i file di installazione dell'agente, prompt dei comandi con privilegi elevati eseguire `extract MMASetup-<platform>.exe` e verrà richiesto per il percorso di file da estrarre.  In alternativa, è possibile specificare il percorso, passando gli argomenti `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Per ulteriori informazioni sul swtiches della riga di comando supportati da IExpress, vedere [della riga di comando per IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e quindi aggiornare l'esempio in base alle esigenze.
3. Al prompt dei comandi digitare `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agente Linux
Per rimuovere l'agente, eseguire la procedura seguente.

1. Scaricare lo [script universale](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) dell'agente Linux nel computer.
2. Eseguire file .sh del bundle con l'argomento *--purge* sul computer, che rimuove completamente l'agente e la relativa configurazione.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurare l'agente per fare riferimento a un gruppo di gestione di Operations Manager

### <a name="windows-agent"></a>Agente di Windows
Eseguire la procedura seguente per configurare l'agente OMS per Windows per segnalare a un gruppo di gestione di System Center Operations Manager. 

1. Accedere al computer con un account che disponga dei diritti amministrativi.
2. Aprire il **Pannello di controllo**. 
3. Fare clic su **Microsoft Monitoring Agent** e quindi fare clic su di **Operations Manager** scheda.
4. Se i server di Operations Manager sono configurati per l'integrazione con Active Directory, fare clic su **Aggiorna automaticamente assegnazioni gruppi di gestione da Servizi di dominio Active Directory**.
5. Fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi gruppo di gestione**.
6. In **nome gruppo di gestione** , digitare il nome del gruppo di gestione.
7. Nel **server di gestione primario** , digitare il nome del computer del server di gestione primario.
8. Nel **porta server di gestione** , digitare il numero di porta TCP.
9. In **Account azione agente**scegliere l'account di sistema locale o un account di dominio locale.
10. Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi gruppo di gestione** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente Linux
Seguire questa procedura per configurare l'agente OMS per Linux per inviare segnalazioni a un gruppo di gestione di System Center Operations Manager. 

1. Modificare il file `/etc/opt/omi/conf/omiserver.conf`
2. Verificare che la riga che inizia con `httpsport=` definisca la porta 1270. Ad esempio: `httpsport=1270`
3. Riavviare il server OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passaggi successivi

Revisione [risoluzione dei problemi dell'agente Linux](log-analytics-agent-linux-support.md) se si verificano problemi durante l'installazione o l'agente di gestione.  