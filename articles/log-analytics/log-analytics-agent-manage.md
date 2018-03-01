---
title: Gestione dell'agente di Azure Log Analytics | Microsoft Docs
description: "Questo articolo descrive le diverse attività di gestione che si eseguono in genere durante il ciclo di vita di Microsoft Monitoring Agent (MMA) distribuito in un computer."
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
ms.date: 02/09/2018
ms.author: magoedte
ms.openlocfilehash: 2e4daebf18d5edeba92bc14d5a4f699fbd2d94ce
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux

Dopo la distribuzione iniziale dell'agente Windows o Linux per Log Analytics, può essere necessario riconfigurare l'agente o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita.  È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="adding-or-removing-a-workspace"></a>Aggiunta o rimozione di un'area di lavoro 

### <a name="windows-agent"></a>Agente Windows

#### <a name="update-settings-from-control-panel"></a>Aggiornare le impostazioni dal Pannello di controllo

1. Accedere al computer con un account con diritti amministrativi.
2. Aprire il **Pannello di controllo**.
3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic sulla scheda **Azure Log Analytics (OMS)**.
4. Per rimuovere un'area di lavoro, selezionarla e quindi fare clic su **Rimuovi**. Ripetere questo passaggio per ogni altra area di lavoro per cui si vuole che l'agente interrompa l'invio di report.
5. Per aggiungere un'area di lavoro, fare clic su **Aggiungi** e, nella finestra di dialogo **Add a Log Analytics Workspace** (Aggiungere un'area di lavoro di Log Analytics), incollare l'ID dell'area di lavoro e la chiave dell'area di lavoro (chiave primaria). Se il computer deve inviare report a un'area di lavoro di Log Analytics nel cloud Azure per enti pubblici, selezionare Azure per enti pubblici degli Stati Uniti nell'elenco a discesa Cloud di Azure. 
6. Fare clic su **OK** per salvare le modifiche.

#### <a name="remove-a-workspace-using-powershell"></a>Rimuovere un'area di lavoro usando PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Aggiungere un'area di lavoro in Azure Commercial usando PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Aggiungere un'area di lavoro in Azure per enti pubblici degli Stati Uniti usando PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se in precedenza si è scelto di usare la riga di comando o uno script per installare o configurare l'agente, `EnableAzureOperationalInsights` è stato sostituito da `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Aggiornare le impostazioni proxy 
Per configurare l'agente per comunicare con il servizio tramite un server proxy o il [gateway OMS](log-analytics-oms-gateway.md) dopo la distribuzione, usare uno dei metodi seguenti per completare questa attività.

### <a name="windows-agent"></a>Agente Windows

#### <a name="update-settings-using-control-panel"></a>Aggiornare le impostazioni usando il Pannello di controllo

1. Accedere al computer con un account con diritti amministrativi.
2. Aprire il **Pannello di controllo**.
3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic sulla scheda **Impostazioni proxy**.
4. Fare clic su **Usa un server proxy** e specificare URL e numero di porta del server proxy o del gateway. Se il server proxy o il gateway OMS richiede l'autenticazione, digitare nome utente e password per l'autenticazione e quindi fare clic su **OK**. 

#### <a name="update-settings-using-powershell"></a>Aggiornare le impostazioni usando PowerShell 

Copiare il codice di PowerShell di esempio seguente, aggiornarlo con le informazioni specifiche per l'ambiente e salvarlo con un'estensione PS1.  Eseguire lo script in ogni computer che si connette direttamente al servizio Log Analytics.

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
Se i computer Linux devono comunicare tramite un server proxy o un gateway OMS con Log Analytics, eseguire la procedura seguente.  Il valore di configurazione proxy ha la sintassi seguente `[protocol://][user:password@]proxyhost[:port]`.  La proprietà *proxyhost* accetta un nome di dominio completo o l'indirizzo IP del server proxy.

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

## <a name="uninstall-agent"></a>Disinstallare l'agente
Usare una delle procedure seguenti per disinstallare l'agente Windows o Linux tramite la riga di comando o l'installazione guidata.

### <a name="windows-agent"></a>Agente Windows

#### <a name="uninstall-from-control-panel"></a>Eseguire la disinstallazione dal Pannello di controllo
1. Accedere al computer con un account con diritti amministrativi.  
2. Nel **Pannello di controllo** fare clic su **Programmi e funzionalità**.
3. In **Programmi e funzionalità** fare clic su **Microsoft Monitoring Agent**, quindi su **Disinstalla** e infine su **Sì**.

>[!NOTE]
>È possibile anche eseguire l'installazione guidata dell'agente facendo doppio clic su **MMASetup-<platform>.exe**, che è disponibile per il download da un'area di lavoro nel portale di Azure.

#### <a name="uninstall-from-the-command-line"></a>Eseguire la disinstallazione dalla riga di comando
Il file scaricato per l'agente è un pacchetto di installazione autonomo creato con IExpress.  Il programma di installazione per l'agente e i file di supporto sono contenuti nel pacchetto e devono essere estratti per eseguire correttamente la disinstallazione tramite la riga di comando mostrata nell'esempio seguente. 

1. Accedere al computer con un account con diritti amministrativi.  
2. Per estrarre i file di installazione dell'agente, da un prompt dei comandi con privilegi elevati eseguire `extract MMASetup-<platform>.exe` e verrà chiesto il percorso in cui estrarre i file.  In alternativa, è possibile specificare il percorso passando gli argomenti `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Per altre informazioni sulle opzioni della riga di comando supportate da IExpress, vedere [Command-line switches for IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) (Opzioni della riga di comando di IExpress) e quindi aggiornare l'esempio in base alle esigenze.
3. Al prompt digitare `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agente Linux
Per rimuovere l'agente, eseguire il comando seguente nel computer Linux.  L'argomento *--purge* rimuove completamente l'agente e la rispettiva configurazione.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurare l'agente per l'invio di report a un gruppo di gestione di Operations Manager

### <a name="windows-agent"></a>Agente Windows
Seguire questa procedura per configurare l'agente OMS per Windows per l'invio di report a un gruppo di gestione di System Center Operations Manager. 

1. Accedere al computer con un account con diritti amministrativi.
2. Aprire il **Pannello di controllo**. 
3. Aprire **Microsoft Monitoring Agent** e quindi fare clic sulla scheda **Operations Manager**.
4. Se i server di Operations Manager sono configurati per l'integrazione con Active Directory, fare clic su **Aggiorna automaticamente assegnazioni gruppi di gestione da Servizi di dominio Active Directory**.
5. Fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi gruppo di gestione**.
6. Nel campo **Nome gruppo di gestione** digitare il nome del gruppo di gestione.
7. Nel campo **Server di gestione primario** digitare il nome computer del server di gestione primario.
8. Nel campo **Porta server di gestione** digitare il numero di porta TCP.
9. In **Account azione agente**scegliere l'account di sistema locale o un account di dominio locale.
10. Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi gruppo di gestione** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente Linux
Seguire questa procedura per configurare l'agente OMS per Linux per inviare segnalazioni a un gruppo di gestione di System Center Operations Manager. 

1. Modificare il file `/etc/opt/omi/conf/omiserver.conf`
2. Assicurarsi che la riga che inizia con `httpsport=` definisca la porta 1270. Ad esempio: `httpsport=1270`
3. Riavviare il server OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi di installazione o gestione dell'agente, vedere l'articolo relativo alla [risoluzione dei problemi dell'agente Linux](log-analytics-agent-linux-support.md).  