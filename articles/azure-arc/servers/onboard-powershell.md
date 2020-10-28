---
title: Connettere macchine ibride ad Azure con PowerShell
description: Questo articolo illustra come installare l'agente e connettere un computer ad Azure usando i server abilitati per Azure ARC usando PowerShell.
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: bb114ec3e279a7ea696d834af8eb7240cb892dc1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891942"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Connettere macchine ibride ad Azure con PowerShell

È possibile abilitare i server abilitati per Azure Arc per uno o più computer Windows o Linux nell'ambiente eseguendo una serie di passaggi manuali. In alternativa, è possibile usare il cmdlet di PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) per scaricare l'agente del computer connesso, installare l'agente e registrare il computer con Azure Arc. Il cmdlet Scarica il pacchetto di Windows Installer dell'agente di Windows dall'area download Microsoft e il pacchetto dell'agente Linux dal repository dei pacchetti di Microsoft.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale. È possibile completare questo processo in modo interattivo o in remoto in un server Windows usando la [comunicazione remota di PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti. Per informazioni sulle aree supportate e altre considerazioni correlate, vedere [aree di Azure supportate](overview.md#supported-regions).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).

Prima di usare Azure PowerShell per gestire le estensioni di macchina virtuale nel server ibrido gestito da server abilitati per Arc, è necessario installare il `Az.ConnectedMachine` modulo. Eseguire il comando seguente nel server abilitato per Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Al termine dell'installazione, viene restituito il messaggio seguente:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

1. Aprire una console di PowerShell con privilegi elevati.

2. Accedere ad Azure eseguendo il comando `Connect-AzAccount` .

3. Per installare l'agente computer connesso, utilizzare `Connect-AzConnectedMachine` con i `-Name` `-ResourceGroupName` parametri, e `-Location` . Usare il `-SubscriptionId` parametro per sostituire la sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso. Eseguire uno dei comandi seguenti:

    * Per installare l'agente del computer connesso nel computer di destinazione che può comunicare direttamente con Azure, eseguire:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    * Per installare l'agente del computer connesso nel computer di destinazione che comunica tramite un server proxy, eseguire:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. In Windows in *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* e in Linux in */var/opt/azcmagent/log/himds.log* .

## <a name="install-and-connect-using-powershell-remoting"></a>Installare e connettersi tramite la comunicazione remota di PowerShell

Eseguire i passaggi seguenti per configurare il computer o il server Windows di destinazione con i server abilitati per Azure Arc. La comunicazione remota di PowerShell deve essere abilitata nel computer remoto. Usare il cmdlet `Enable-PSRemoting` per abilitare la comunicazione remota di PowerShell.

1. Aprire una console di PowerShell come amministratore.

2. Accedere ad Azure eseguendo il comando `Connect-AzAccount` .

3. Per installare l'agente computer connesso, utilizzare `Connect-AzConnectedMachine` con i `-Name` `-ResourceGroupName` parametri, e `-Location` . Usare il `-SubscriptionId` parametro per sostituire la sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso.

Per installare l'agente del computer connesso nel computer di destinazione che può comunicare direttamente con Azure, eseguire il comando seguente:

```azurepowershell
$session = Connect-PSSession -ComputerName myMachineName
Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
```

Nell'esempio seguente vengono restituiti i risultati del comando:

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

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://portal.azure.com).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).