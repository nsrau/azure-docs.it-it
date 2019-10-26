---
title: Gestione dell'agente di Azure Log Analytics | Microsoft Docs
description: Questo articolo descrive le diverse attività di gestione che in genere vengono eseguite durante il ciclo di vita del Log Analytics agente Windows o Linux distribuito in un computer.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.openlocfilehash: 8dec91a3987aed978bb088d1aeab48a6fd0f9fb4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932798"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux

Dopo la distribuzione iniziale dell'agente Log Analytics Windows o Linux in monitoraggio di Azure, potrebbe essere necessario riconfigurare l'agente, aggiornarlo o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita. È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="upgrading-agent"></a>Aggiornamento dell'agente

L'agente di Log Analytics per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base allo scenario di distribuzione e all'ambiente in cui è in esecuzione la macchina virtuale. Per aggiornare l'agente, è possibile utilizzare i metodi seguenti.

| Ambiente | Metodo di installazione | Metodo di aggiornamento |
|--------|----------|-------------|
| Macchina virtuale di Azure | Estensione macchina virtuale agente Log Analytics per Windows/Linux | Per impostazione predefinita, Agent viene aggiornato automaticamente a meno che non sia stato configurato il modello di Azure Resource Manager per rifiutare esplicitamente l'impostazione della proprietà *autoUpgradeMinorVersion* su **false**. |
| Immagini personalizzate di VM di Azure | Installazione manuale dell'agente di Log Analytics per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux.|
| Macchine virtuali non di Azure | Installazione manuale dell'agente di Log Analytics per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux. |

### <a name="upgrade-windows-agent"></a>Aggiornare l'agente Windows 

Per aggiornare l'agente in una macchina virtuale Windows alla versione più recente non installata usando l'estensione Log Analytics VM, è possibile eseguire dal prompt dei comandi, dallo script o da un'altra soluzione di automazione oppure tramite l'installazione guidata della piattaforma MMASetup-\<\>. msi.  

È possibile scaricare la versione più recente dell'agente Windows dall'area di lavoro di Log Analytics, eseguendo i passaggi seguenti.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

3. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro.

4. Nell'area di lavoro Log Analytics selezionare **Impostazioni avanzate**, quindi selezionare **origini connesse**e infine **server Windows**.

5. Dalla pagina **server Windows** selezionare la versione appropriata per scaricare l' **agente Windows** da scaricare, a seconda dell'architettura del processore del sistema operativo Windows.

>[!NOTE]
>Durante l'aggiornamento dell'agente di Log Analytics per Windows, non supporta la configurazione o la riconfigurazione di un'area di lavoro per la segnalazione. Per configurare l'agente, è necessario seguire uno dei metodi supportati elencati in [aggiunta o rimozione di un'area di lavoro](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Per eseguire l'aggiornamento utilizzando l'installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **MMASetup-\<platform\>. exe** per avviare l'installazione guidata.

3. Nella prima pagina dell'installazione guidata, fare clic su **Avanti**.

4. Nella finestra di dialogo **Microsoft Monitoring Agent installazione** **fare clic su Accetto il** contratto di licenza.

5. Nella finestra di dialogo **Microsoft Monitoring Agent installazione** fare clic su **Aggiorna**. Nella pagina stato viene visualizzato lo stato di avanzamento dell'aggiornamento.

6. Quando la **configurazione del Microsoft Monitoring Agent è stata completata correttamente.** viene visualizzata la pagina, fare clic su **fine**.

#### <a name="to-upgrade-from-the-command-line"></a>Per eseguire l'aggiornamento dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Per estrarre i file di installazione dell'agente, da un prompt dei comandi con privilegi elevati eseguire `MMASetup-<platform>.exe /c` e verrà chiesto il percorso in cui estrarre i file. In alternativa, è possibile specificare il percorso passando gli argomenti `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Eseguire il comando seguente, dove D:\ percorso del file di log dell'aggiornamento.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Aggiornare l'agente Linux 

È supportato l'aggiornamento da versioni precedenti (> 1.0.0-47). L'esecuzione dell'installazione con il comando `--upgrade` comporta l'aggiornamento di tutti i componenti dell'agente alla versione più recente.

Eseguire il comando seguente per aggiornare l'agente.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Aggiunta o rimozione di un'area di lavoro

### <a name="windows-agent"></a>Agente Windows
I passaggi descritti in questa sezione sono necessari quando si desidera non solo riconfigurare l'agente Windows per la segnalazione in un'area di lavoro diversa o per rimuovere un'area di lavoro dalla relativa configurazione, ma anche quando si desidera configurare l'agente per la segnalazione a più di un'area di lavoro (in genere definito multihoming. La configurazione dell'agente Windows per la segnalazione a più aree di lavoro può essere eseguita solo dopo la configurazione iniziale dell'agente e l'utilizzo dei metodi descritti di seguito.    

#### <a name="update-settings-from-control-panel"></a>Aggiornare le impostazioni dal Pannello di controllo

1. Accedere al computer con un account con diritti amministrativi.

2. Aprire il **Pannello di controllo**.

3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic sulla scheda **Azure Log Analytics**.

4. Per rimuovere un'area di lavoro, selezionarla e quindi fare clic su **Rimuovi**. Ripetere questo passaggio per ogni altra area di lavoro per cui si vuole che l'agente interrompa l'invio di report.

5. Per aggiungere un'area di lavoro, fare clic su **Aggiungi** e, nella finestra di dialogo **Add a Log Analytics Workspace** (Aggiungere un'area di lavoro Log Analytics), incollare l'ID dell'area di lavoro e la chiave dell'area di lavoro (chiave primaria). Se il computer deve inviare report a un'area di lavoro Log Analytics nel cloud Azure per enti pubblici, selezionare Azure per enti pubblici degli Stati Uniti nell'elenco a discesa Cloud di Azure.

6. Fare clic su **OK** per salvare le modifiche.

#### <a name="remove-a-workspace-using-powershell"></a>Rimuovere un'area di lavoro usando PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Aggiungere un'area di lavoro in Azure Commercial usando PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Aggiungere un'area di lavoro in Azure per enti pubblici degli Stati Uniti usando PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se in precedenza si è scelto di usare la riga di comando o uno script per installare o configurare l'agente, `EnableAzureOperationalInsights` è stato sostituito da `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agente Linux
La procedura seguente illustra come riconfigurare l'agente Linux se si decide di registrarlo con un'area di lavoro diversa o di rimuovere un'area di lavoro dalla relativa configurazione.

1. Per verificare che sia registrato in un'area di lavoro, eseguire il comando seguente:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Dovrebbe essere restituito uno stato simile all'esempio seguente:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    È importante che lo stato indichi anche che l'agente è in esecuzione. In caso contrario, la procedura seguente per la riconfigurazione dell'agente non verrà completata.

2. Se l'agente è già registrato in un'area di lavoro, rimuovere l'area di lavoro registrata eseguendo il comando seguente. Se invece non è ancora stato registrato, procedere con il passaggio successivo.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Per registrare l'agente in un'altra area di lavoro, eseguire il comando seguente:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Per verificare che le modifiche abbiano avuto effetto, eseguire il comando seguente:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Dovrebbe essere restituito uno stato simile all'esempio seguente:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Non è necessario riavviare il servizio agente per rendere effettive le modifiche.

## <a name="update-proxy-settings"></a>Aggiornare le impostazioni proxy
Per configurare l'agente per comunicare con il servizio tramite un server proxy o il [gateway Log Analytics](gateway.md) dopo la distribuzione, usare uno dei modi seguenti per completare questa attività.

### <a name="windows-agent"></a>Agente Windows

#### <a name="update-settings-using-control-panel"></a>Aggiornare le impostazioni usando il Pannello di controllo

1. Accedere al computer con un account con diritti amministrativi.

2. Aprire il **Pannello di controllo**.

3. Selezionare **Microsoft Monitoring Agent** e quindi fare clic sulla scheda **Impostazioni proxy**.

4. Fare clic su **Usa un server proxy** e specificare URL e numero di porta del server proxy o del gateway. Se il server proxy o il gateway Log Analytics richiede l'autenticazione, digitare il nome utente e la password per l'autenticazione e quindi fare clic su **OK**.

#### <a name="update-settings-using-powershell"></a>Aggiornare le impostazioni usando PowerShell

Copiare il codice di PowerShell di esempio seguente, aggiornarlo con le informazioni specifiche per l'ambiente e salvarlo con un'estensione PS1. Eseguire lo script in ogni computer che si connette direttamente all'area di lavoro Log Analytics in monitoraggio di Azure.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
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
Se i computer Linux devono comunicare tramite un server proxy o un gateway Log Analytics, eseguire la procedura seguente. Il valore di configurazione proxy ha la sintassi seguente `[protocol://][user:password@]proxyhost[:port]`. La proprietà *proxyhost* accetta un nome di dominio completo o l'indirizzo IP del server proxy.

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
Utilizzare una delle seguenti procedure per disinstallare l'agente Windows o Linux utilizzando la riga di comando o l'installazione guidata.

### <a name="windows-agent"></a>Agente Windows

#### <a name="uninstall-from-control-panel"></a>Eseguire la disinstallazione dal Pannello di controllo
1. Accedere al computer con un account con diritti amministrativi.

2. Nel **Pannello di controllo** fare clic su **Programmi e funzionalità**.

3. In **Programmi e funzionalità** fare clic su **Microsoft Monitoring Agent**, quindi su **Disinstalla** e infine su **Sì**.

>[!NOTE]
>È possibile eseguire l'installazione guidata dell'agente anche facendo doppio clic su **MMASetup-\<piattaforma\>.exe**, che è disponibile per il download da un'area di lavoro nel portale di Azure.

#### <a name="uninstall-from-the-command-line"></a>Eseguire la disinstallazione dalla riga di comando
Il file scaricato per l'agente è un pacchetto di installazione autonomo creato con IExpress. Il programma di installazione per l'agente e i file di supporto sono contenuti nel pacchetto e devono essere estratti per eseguire correttamente la disinstallazione tramite la riga di comando mostrata nell'esempio seguente.

1. Accedere al computer con un account con diritti amministrativi.

2. Per estrarre i file di installazione dell'agente, da un prompt dei comandi con privilegi elevati eseguire `extract MMASetup-<platform>.exe` e verrà chiesto il percorso in cui estrarre i file. In alternativa, è possibile specificare il percorso passando gli argomenti `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Per altre informazioni sulle opzioni della riga di comando supportate da IExpress, vedere [Opzioni della riga di comando per i pacchetti di aggiornamento software IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e aggiornare l'esempio in base alle esigenze.

3. Al prompt digitare `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agente Linux
Per rimuovere l'agente, eseguire il comando seguente nel computer Linux. L'argomento *--purge* rimuove completamente l'agente e la rispettiva configurazione.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurare l'agente per l'invio di report a un gruppo di gestione di Operations Manager

### <a name="windows-agent"></a>Agente Windows
Seguire questa procedura per configurare l'agente di Log Analytics per Windows per l'invio di report a un gruppo di gestione di System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

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
Seguire questa procedura per configurare l'agente di Log Analytics per Linux per l'invio di report a un gruppo di gestione di System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Modificare il file `/etc/opt/omi/conf/omiserver.conf`

2. Assicurarsi che la riga che inizia con `httpsport=` definisca la porta 1270. Ad esempio: `httpsport=1270`

3. Riavviare il server OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [la risoluzione dei problemi dell'agente Linux](agent-linux-troubleshoot.md) se si verificano problemi durante l'installazione o la gestione dell'agente Linux.

- Esaminare [la risoluzione dei problemi dell'agente Windows](agent-windows-troubleshoot.md) se si verificano problemi durante l'installazione o la gestione dell'agente di Windows.
