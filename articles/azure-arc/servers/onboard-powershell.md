---
title: Connettere macchine ibride ad Azure con PowerShell
description: Questo articolo illustra come installare l'agente e connettere un computer ad Azure usando i server abilitati per Azure ARC usando PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375273"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Connettere macchine ibride ad Azure con PowerShell

È possibile abilitare i server abilitati per Azure Arc per uno o un numero ridotto di computer Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. In alternativa, è possibile usare il cmdlet [di PowerShell Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Questo cmdlet esegue le azioni seguenti:

- Configura il computer host per scaricare l'agente Windows dall'area download Microsoft e il pacchetto dell'agente Linux da packages.microsoft.com.
- Installa l'agente del computer connesso.
- Registra il computer con Azure Arc

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale.

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

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

1. Aprire una console di PowerShell come amministratore.

2. Accedere ad Azure eseguendo il comando `Connect-AzAccount` .

3. Per installare l'agente computer connesso, utilizzare `Connect-AzConnectedMachine` con i `-Name` `-ResourceGroupName` parametri, e `-Location` . Usare il `-SubscriptionId` parametro per sostituire la sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso.

    Per installare l'agente del computer connesso nel computer di destinazione che può comunicare direttamente con Azure, eseguire il comando seguente:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Se la macchina di destinazione comunica tramite un server proxy, eseguire il comando seguente:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. In Windows in *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*e in Linux in */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://portal.azure.com).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).