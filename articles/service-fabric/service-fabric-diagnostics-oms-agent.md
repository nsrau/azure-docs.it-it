---
title: I log di Azure Service Fabric - monitoraggio delle prestazioni con monitoraggio di Azure | Microsoft Docs
description: Informazioni su come configurare l'agente di Log Analytics per il monitoraggio di contenitori e contatori delle prestazioni per i cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 819f6ee4ab079361279a567bceeb74c33fe14186
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662007"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitoraggio delle prestazioni con i log di monitoraggio di Azure

Questo articolo illustra i passaggi necessari per aggiungere l'agente di Log Analytics come estensione del set di scalabilità di macchine virtuali al cluster e connetterlo all'area di lavoro di Log Analytics di Azure esistente. Ciò consente la raccolta dei dati di diagnostica relativi a contenitori, applicazioni e monitoraggio delle prestazioni. Aggiungendolo come un'estensione alla risorsa del set di scalabilità di macchine virtuali, Azure Resource Manager ne garantisce l'installazione su ogni nodo, anche in caso di ridimensionamento del cluster.

> [!NOTE]
> Questo articolo presuppone che sia già stata configurata un'area di lavoro di Log Analytics di Azure. In caso contrario, andare alla [configurare i log di monitoraggio di Azure](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Aggiungere l'estensione dell'agente tramite l'interfaccia della riga di comando di Azure

Il modo migliore per aggiungere l'agente di Log Analytics al cluster è tramite le API del set di scalabilità di macchine virtuali disponibili con l'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è ancora configurata, passare al portale di Azure e aprire un'istanza [Cloud Shell](../cloud-shell/overview.md) oppure [installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Dopo aver eseguito la richiesta per Cloud Shell, assicurarsi di usare la stessa sottoscrizione della risorsa. A tale scopo, usare `az account show` e assicurarsi che il valore "name" corrisponda a quello della sottoscrizione del cluster.

2. Nel portale passare al gruppo di risorse in cui si trova l'area di lavoro di Log Analytics di Azure. Fare clic sulla risorsa log analitica (il tipo della risorsa sia dell'area di lavoro di Log Analitica). Una volta visualizzata la pagina di panoramica della risorsa, fare clic su **Impostazioni avanzate** nella sezione Impostazioni nel menu a sinistra.

    ![Pagina delle proprietà di log analitica](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Fare clic su **Server Windows** in caso di creazione di un cluster Windows cluster o su **Server Linux** se si sta creando un cluster Linux. In questa pagina verranno visualizzati `workspace ID` e `workspace key` (elencata come Chiave primaria nel portale). Entrambi serviranno per il passaggio successivo.

4. Eseguire il comando per installare l'agente di Log Analytics sul cluster tramite l'API `vmss extension set` nell'istanza corrente di Cloud Shell:

    Per un cluster Windows:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Per un cluster Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Di seguito è riportato un esempio dell'aggiunta dell'agente di Log Analytics a un cluster Windows.

    ![Comando dell'interfaccia della riga di comando dell'agente di Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. L'operazione di aggiunta dell'agente ai nodi dovrebbe richiedere meno di 15 minuti. Per verificare l'esito dell'aggiunta degli agenti, usare l'API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Aggiungere l'agente tramite il modello di Resource Manager

È disponibile un modello di Resource Manager per [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) che distribuisce un'area di lavoro di Log Analytics di Azure e aggiunge un agente a ogni nodo.

È possibile scaricare e modificare questo modello per distribuire un cluster più adatto alle proprie esigenze.

## <a name="view-performance-counters"></a>Visualizzare i contatori delle prestazioni

Dopo avere aggiunto l'agente di Log Analytics, passare al portale di Log Analytics per scegliere i contatori delle prestazioni da raccogliere.

1. Nel portale di Azure, passare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Fare clic su **Log Analytics**.

3. Fare clic su **Impostazioni avanzate**.

4. Fare clic su **Dati**, quindi fare clic su **Contatori delle prestazioni di Linux o di Windows**. È disponibile un elenco di contatori predefiniti che è possibile scegliere di abilitare ed è anche possibile impostare l'intervallo per la raccolta. È anche possibile aggiungere [altri contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) da raccogliere. Il formato corretto è descritto in questo [articolo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Fare clic su **Salva**, quindi su **OK**.

6. Chiudere il pannello Impostazioni avanzate.

7. Sotto l'intestazione Generale fare clic su **Riepilogo dell'area di lavoro**.

8. Saranno visualizzati riquadri sotto forma di grafo per ogni soluzione abilitata, tra cui uno per Service Fabric. Fare clic sul grafo **Service Fabric** per passare alla soluzione Analisi Service Fabric.

9. Saranno visualizzati alcuni riquadri con grafi sugli eventi del canale operativo e di Reliable Services. La rappresentazione grafica dei dati trasmessi ai contatori selezionati sarà visualizzata sotto Metriche del nodo.

10. Fare clic su un grafico Metriche del contenitore per visualizzare dettagli aggiuntivi. È anche possibile eseguire query sui dati dei contatori delle prestazioni in modo analogo agli eventi cluster e applicare filtri sui nodi, sul nome del contatore delle prestazioni e sui valori usando il linguaggio di query Kusto.

![Query sui contatori delle prestazioni Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Passaggi successivi

* Raccogliere i [contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) rilevanti. Per configurare l'agente di Log Analytics affinché raccolga contatori di prestazioni specifici, vedere [Configurazione delle origini dati](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Configurare i log di monitoraggio di Azure per configurare [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica
* In alternativa, è possibile raccogliere i contatori delle prestazioni tramite l'[estensione di Diagnostica di Azure e inviarli ad Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
