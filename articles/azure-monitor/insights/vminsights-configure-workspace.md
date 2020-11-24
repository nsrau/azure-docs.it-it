---
title: Configurare l'area di lavoro Log Analytics per Monitoraggio di Azure per le macchine virtuali
description: Viene descritto come creare e configurare l'area di lavoro Log Analytics utilizzata da Monitoraggio di Azure per le macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: d3bedfae88544d0b0ebd07868438559305279fed
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530115"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Configurare l'area di lavoro Log Analytics per Monitoraggio di Azure per le macchine virtuali
Monitoraggio di Azure per le macchine virtuali raccoglie i dati da una o più aree di lavoro Log Analytics in monitoraggio di Azure. Prima di caricare gli agenti, è necessario creare e configurare un'area di lavoro. Questo articolo descrive i requisiti dell'area di lavoro e per configurarlo per Monitoraggio di Azure per le macchine virtuali.

## <a name="overview"></a>Panoramica
Una singola sottoscrizione può usare un numero qualsiasi di aree di lavoro in base alle proprie esigenze. l'unico requisito dell'area di lavoro è che si trova in una posizione supportata e deve essere configurato con la soluzione *VMInsights* .

Una volta configurata l'area di lavoro, è possibile usare una qualsiasi delle opzioni disponibili per installare gli agenti necessari in VM e VMSS e specificare un'area di lavoro per l'invio dei dati. Monitoraggio di Azure per le macchine virtuali raccoglierà i dati da qualsiasi area di lavoro configurata nella propria sottoscrizione.

> [!NOTE]
> Quando si abilita Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale o VMSS usando il portale di Azure, è possibile selezionare un'area di lavoro esistente o crearne una nuova. La soluzione *VMInsights* verrà installata in questa area di lavoro, se non è già presente. È quindi possibile usare questa area di lavoro per altri agenti.


## <a name="create-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

>[!NOTE]
>Le informazioni descritte in questa sezione sono applicabili anche alla [soluzione mapping dei servizi](service-map.md). 

Accedere alle aree di lavoro Log Analytics nel portale di Azure dal menu **log Analytics aree di lavoro** .

[![Registra aree di lavoro anlitica](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

È possibile creare una nuova area di lavoro Log Analytics usando uno dei metodi seguenti. Vedere [progettazione della distribuzione dei log di monitoraggio di Azure](../platform/design-logs-deployment.md) per indicazioni su come determinare il numero di aree di lavoro da usare nel proprio ambiente e come progettare la strategia di accesso.


* [Azure portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Interfaccia della riga di comando di Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Aree supportate

Monitoraggio di Azure per le macchine virtuali supporta Log Analytics aree di lavoro nelle aree seguenti, sebbene sia possibile monitorare le macchine virtuali in qualsiasi area. Le macchine virtuali non sono limitate alle aree supportate dall'area di lavoro Log Analytics.

- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti orientali
- Stati Uniti Orientali 2
- Stati Uniti centrali
- Stati Uniti centro-settentrionali
- US Gov AZ
- US Gov va
- Canada centrale
- Regno Unito meridionale
- Europa settentrionale
- Europa occidentale
- Asia orientale
- Asia sud-orientale
- India centrale
- Giappone orientale
- Australia orientale
- Australia sud-orientale

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure
Per abilitare e accedere alle funzionalità di Monitoraggio di Azure per le macchine virtuali, è necessario avere il [ruolo di collaboratore log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro. Per visualizzare le prestazioni, l'integrità e la mappa dei dati, è necessario avere il [ruolo di lettore di monitoraggio](../platform/roles-permissions-security.md#built-in-monitoring-roles) per la macchina virtuale di Azure. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Aggiungi soluzione VMInsights all'area di lavoro
Prima di poter usare un'area di lavoro Log Analytics con Monitoraggio di Azure per le macchine virtuali, è necessario che sia installata la soluzione *VMInsights* . Le sezioni seguenti illustrano i metodi per la configurazione dell'area di lavoro.

> [!NOTE]
> Quando si aggiunge la soluzione *VMInsights* all'area di lavoro, tutte le macchine virtuali esistenti connesse all'area di lavoro inizieranno a inviare i dati a InsightsMetrics. I dati per gli altri tipi di dati non verranno raccolti fino a quando non si aggiungeranno le Dependency Agent alle macchine virtuali esistenti connesse all'area di lavoro.

### <a name="azure-portal"></a>Portale di Azure
Sono disponibili tre opzioni per la configurazione di un'area di lavoro esistente usando il portale di Azure. Ognuno di essi è descritto di seguito.

Per configurare una singola area di lavoro, selezionare le **altre opzioni di onboarding** e quindi **configurare un'area di lavoro**. Selezionare una sottoscrizione e un'area di lavoro, quindi fare clic su **Configura**.

[![Configurare l'area di lavoro](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Per configurare più aree di lavoro, selezionare la scheda **configurazione area di lavoro** nel menu **macchine virtuali** del menu **monitoraggio** della portale di Azure. Impostare i valori del filtro per visualizzare un elenco delle aree di lavoro esistenti. Selezionare la casella accanto a ogni area di lavoro da abilitare, quindi fare clic su **Configura selezionato** .

[![Configurazione dell'area di lavoro](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Quando si abilita Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale o VMSS usando il portale di Azure, è possibile selezionare un'area di lavoro esistente o crearne una nuova. La soluzione *VMInsights* verrà installata in questa area di lavoro, se non è già presente. È quindi possibile usare questa area di lavoro per altri agenti.

[![Abilitare una singola VM nel portale](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Modello di Resource Manager
I modelli di Azure Resource Manager per Monitoraggio di Azure per le macchine virtuali sono disponibili in un file di archivio (con estensione zip) che è possibile [scaricare dal repository GitHub](https://aka.ms/VmInsightsARMTemplates). Questo include un modello denominato **ConfigureWorkspace** che configura un'area di lavoro di Log Analytics per monitoraggio di Azure per le macchine virtuali. Questo modello viene distribuito usando uno dei metodi standard, inclusi i comandi PowerShell e CLI di esempio seguenti: 

# <a name="cli"></a>[Interfaccia della riga di comando](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Passaggi successivi
- Vedere [onboarding agents to monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md) to Connect agents to monitoraggio di Azure per le macchine virtuali.
- Vedere [targeting Solutions di monitoraggio in monitoraggio di Azure (anteprima)](solution-targeting.md) per limitare la quantità di dati inviati da una soluzione all'area di lavoro.
