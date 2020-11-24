---
title: Connettere macchine ibride ad Azure tramite PowerShell
description: Questo articolo illustra come installare l'agente e connettere un computer ad Azure usando i server abilitati per Azure Arc. È possibile farlo con PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0218235179e1a8a883360d0061e685c04079cbf4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492942"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Connettere macchine ibride ad Azure tramite PowerShell

Per i server abilitati con Azure Arc, è possibile eseguire passaggi manuali per abilitarli per uno o più computer Windows o Linux nell'ambiente in uso. In alternativa, è possibile usare il cmdlet di PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) per scaricare l'agente del computer connesso, installare l'agente e registrare il computer con Azure Arc. Il cmdlet Scarica il pacchetto dell'agente Windows (Windows Installer) dall'area download Microsoft e il pacchetto dell'agente Linux dal repository dei pacchetti Microsoft.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale. È possibile completare questo processo in modo interattivo o in remoto in un server Windows usando la [comunicazione remota di PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti. Per informazioni sulle aree supportate e altre considerazioni correlate, vedere [aree di Azure supportate](overview.md#supported-regions).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).

Usare PowerShell per gestire le estensioni delle macchine virtuali nei server ibridi gestiti da server abilitati per Azure Arc. Prima di usare PowerShell, installare il `Az.ConnectedMachine` modulo. Eseguire il comando seguente nel server abilitato con Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Al termine dell'installazione, viene visualizzato il messaggio seguente:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

1. Aprire una console di PowerShell con privilegi elevati.

2. Per accedere ad Azure, eseguire il comando `Connect-AzAccount` .

3. Per installare l'agente computer connesso, utilizzare `Connect-AzConnectedMachine` con i `-Name` `-ResourceGroupName` parametri, e `-Location` . Usare il `-SubscriptionId` parametro per sostituire la sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso. Eseguire uno dei comandi seguenti:

    * Per installare l'agente del computer connesso nel computer di destinazione che può comunicare direttamente con Azure, eseguire:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Per installare l'agente del computer connesso nel computer di destinazione che comunica tramite un server proxy, eseguire:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. In Windows selezionare il file seguente: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. In Linux selezionare il file seguente: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installare e connettersi tramite la comunicazione remota di PowerShell

Di seguito viene illustrato come configurare uno o più server Windows con server abilitati con Azure Arc. È necessario abilitare la comunicazione remota di PowerShell nel computer remoto. A tale scopo, utilizzare il cmdlet `Enable-PSRemoting`.

1. Aprire una console di PowerShell come amministratore.

2. Per accedere ad Azure, eseguire il comando `Connect-AzAccount` .

3. Per installare l'agente computer connesso, utilizzare `Connect-AzConnectedMachine` con i `-ResourceGroupName` parametri, e `-Location` . I nomi delle risorse di Azure utilizzeranno automaticamente il nome host di ogni server. Usare il `-SubscriptionId` parametro per sostituire la sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso.

    * Per installare l'agente del computer connesso nel computer di destinazione che può comunicare direttamente con Azure, eseguire il comando seguente:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Per installare l'agente computer connesso in più computer remoti allo stesso tempo, aggiungere un elenco di nomi di computer remoti separati da una virgola.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Nell'esempio seguente vengono illustrati i risultati del comando destinati a un singolo computer:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato e configurato l'agente per la registrazione con i server abilitati per Azure Arc, passare alla portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare la propria macchina virtuale nel [portale di Azure](https://portal.azure.com).

![Screenshot del dashboard dei server, che mostra una connessione al server riuscita.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

* Se necessario, vedere la [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md). È possibile usare la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificare che il computer stia segnalando l'area di lavoro log Analytics prevista e abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md).

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere i dati di monitoraggio del sistema operativo e del carico di lavoro oppure gestirli tramite manuali operativi di automazione di Azure o funzionalità come Gestione aggiornamenti. Questo agente è necessario anche per usare altri servizi di Azure, ad esempio il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).
