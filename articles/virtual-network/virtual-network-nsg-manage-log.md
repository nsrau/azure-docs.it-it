---
title: Log di diagnostica di Azure per gli eventi e il contatore regole dei gruppi di sicurezza di rete | Microsoft Docs
description: Informazioni su come abilitare i log di diagnostica per eventi e contatore regole per un gruppo di sicurezza di rete di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757060"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Registrazione diagnostica per un gruppo di sicurezza di rete

Un gruppo di sicurezza di rete (NSG) include regole che consentono o negano il traffico verso una subnet della rete virtuale, un'interfaccia di rete o entrambe. Quando si abilita la registrazione diagnostica per un gruppo di sicurezza di rete, è possibile registrare le categorie di informazioni seguenti:

* **Evento:** vengono registrate voci relative alle regole dei gruppi di sicurezza di rete applicate alle macchine virtuali in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi.
* **Contenitore di regole:** contiene voci per sapere quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico.

I log di diagnostica sono disponibili solo per i gruppi di sicurezza di rete distribuiti nel modello di distribuzione di Azure Resource Manager. Non è possibile abilitare la registrazione diagnostica per i gruppi di sicurezza di rete distribuiti tramite il modello di distribuzione classico. Per altre informazioni sui due modelli, vedere le [informazioni sui modelli di distribuzione di Azure](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

La registrazione diagnostica viene abilitata separatamente per *ogni* gruppo di sicurezza di rete per cui si vogliono raccogliere dati di diagnostica. Se è interessati invece ai log operativi, o log attività, vedere le informazioni sulla [registrazione delle attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Abilitazione della registrazione

È possibile usare il [portale di Azure](#azure-portal) o [PowerShell](#powershell) per abilitare la registrazione diagnostica.

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi digitare *gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
3. Selezionare il gruppo di sicurezza di rete per cui si vuole abilitare l'accesso.
4. In **Monitoraggio** selezionare **Log di diagnostica** e quindi selezionare **Abilita diagnostica**, come illustrato nell'immagine seguente:
 
    ![Attivare la diagnostica](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. In **Impostazioni di diagnostica** immettere o selezionare le informazioni seguenti e quindi selezionare **Salva**:

    | Impostazione                                                                                     | Valore                                                          |
    | ---------                                                                                   |---------                                                       |
    | NOME                                                                                        | Un nome di propria scelta.  Ad esempio: *DiagnosticaGsr*      |
    | **Archivia in un account di archiviazione**, **Streaming in un hub eventi** e **Invia a Log Analytics** | È possibile selezionare tutte le destinazioni desiderate. Per altre informazioni su ognuna, vedere [Destinazioni dei log](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Selezionare una o entrambe le categorie di log. Per altre informazioni su dati registrati per ogni categoria, vedere [Categorie di log](#log-categories).                                                                                                                                             |
6. Visualizzare e analizzare i log. Per altre informazioni, vedere [Visualizzare e analizzare i log](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure eseguendo PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo *AzureRM* di PowerShell versione 6.1.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è necessario eseguire anche `Login-AzureRmAccount` per accedere ad Azure con un account con le [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Per abilitare la registrazione diagnostica, è necessario l'ID di un gruppo di sicurezza di rete esistente. Se non è disponibile un gruppo di sicurezza di rete esistente, è possibile crearne uno con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Recuperare il gruppo di sicurezza di rete per cui si vuole abilitare la registrazione diagnostica con [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Ad esempio, per recuperare un gruppo di sicurezza di rete denominato *myNsg* esistente in un gruppo di risorse denominato *myResourceGroup*, immettere il comando seguente:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

È possibile scrivere i log di diagnostica in tre tipi di destinazione. Per altre informazioni, vedere [Destinazioni dei log](#log-destinations). In questo articolo, i log vengono inviati alla destinazione *Log Analytics*, a titolo di esempio. Recuperare un'area di lavoro di Log Analytics esistente con [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Ad esempio, per recuperare un'area di lavoro esistente denominata *myLaWorkspace* in un gruppo di risorse denominato *LaWorkspaces*, immettere il comando seguente:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Se non è disponibile un'area di lavoro esistente, è possibile crearne una con [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Esistono due categorie di registrazione per cui è possibile abilitare i log. Per altre informazioni, vedere [Categorie di log](#log-categories). Abilitare la registrazione diagnostica per il gruppo di sicurezza di rete con [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). L'esempio seguente registra sia i dati della categoria evento che quelli della categoria contatore nell'area di lavoro per un gruppo sicurezza di rete, usando gli ID per il gruppo sicurezza di rete e l'area di lavoro recuperati in precedenza:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se si vogliono registrare i dati solo per una categoria o l'altra, anziché entrambe, aggiungere l'opzione `-Categories` al comando precedente, seguita da *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Se si vuole accedere a una [destinazione](#log-destinations) diversa rispetto a un'area di lavoro di Log Analytics, usare i parametri appropriati per un [account di archiviazione](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure oppure per [Hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Visualizzare e analizzare i log. Per altre informazioni, vedere [Visualizzare e analizzare i log](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinazioni dei log

I dati di diagnostica possono essere:
- [Scritti in un account di archiviazione di Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite le impostazioni di diagnostica delle risorse.
- [Trasmessi in streaming a un hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
- [Scritti in Azure Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Categorie di log

Per le categorie di log seguenti vengono scritti dati in formato JSON:

### <a name="event"></a>Event

Il registro eventi contiene informazioni su quali regole del gruppo di sicurezza di rete vengono applicate alle macchine virtuali, in base all'indirizzo MAC. I dati nell'esempio seguente vengono registrati per ogni evento:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Contatore regole

Il log contatore regole contiene informazioni su ogni regola applicata alle risorse. I dati nell'esempio seguente vengono registrati ogni volta che viene applicata una regola:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> L'indirizzo IP di origine per la comunicazione non viene registrato. È comunque possibile abilitare la [registrazione dei flussi per i gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md) per un gruppo di sicurezza di rete, che registra tutte le informazioni sul contatore regole, oltre all'indirizzo IP di origine che ha avviato la comunicazione. I dati del log del flusso di NSG vengono scritti in un account di archiviazione di Azure. È possibile analizzare i dati con la funzionalità di [analisi del traffico](../network-watcher/traffic-analytics.md) di Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visualizzare e analizzare i log

Per informazioni su come visualizzare i dati dei log di diagnostica, vedere [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se si inviano i dati di diagnostica a:
- **Log Analytics**: è possibile usare la soluzione di [analisi del gruppo di sicurezza di rete](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) per ottenere ulteriori informazioni dettagliate. La soluzione offre visualizzazioni per le regole dei gruppi di sicurezza di rete che consentono o negano il traffico, in base all'indirizzo MAC, dell'interfaccia di rete in una macchina virtuale.
- **Account di archiviazione Azure**: i dati vengono scritti in un file PT1H.json. È possibile trovare il:
    - Registro eventi nel percorso seguente: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Log contatore regole nel percorso seguente: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passaggi successivi

- Scoprire di più sulla [registrazione delle attività](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), precedentemente nota come log di controllo o operativi. La registrazione delle attività è abilitata per impostazione predefinita per i gruppi di sicurezza di rete creati tramite qualsiasi modello di distribuzione di Azure. Per determinare quali operazioni sono state completate nei NGS nel log attività, cercare le voci che contengono i tipi di risorsa seguenti:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Per informazioni su come registrare le informazioni di diagnostica, per includere l'indirizzo IP di origine per ogni flusso, vedere [NSG flow logging](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Registrazione dei flussi per il gruppo di sicurezza di rete).