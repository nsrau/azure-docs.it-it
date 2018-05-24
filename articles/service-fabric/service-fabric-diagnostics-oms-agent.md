---
title: Azure Service Fabric - Monitoraggio delle prestazioni con Log Analytics | Microsoft Docs
description: Informazioni su come configurare l'agente di OMS per il monitoraggio di contenitori e contatori delle prestazioni per i cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: a3ce72e51477c1eda99461b3910bfeeac207be55
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>Monitoraggio delle prestazioni con Log Analytics

Questo articolo illustra i passaggi necessari per aggiungere l'agente di Log Analytics, anche noto come OMS, come estensione del set di scalabilità di macchine virtuali al cluster e connetterlo all'area di lavoro di Log Analytics di Azure esistente. Ciò consente la raccolta dei dati di diagnostica relativi a contenitori, applicazioni e monitoraggio delle prestazioni. Aggiungendolo come un'estensione alla risorsa del set di scalabilità di macchine virtuali, Azure Resource Manager ne garantisce l'installazione su ogni nodo, anche in caso di ridimensionamento del cluster.

> [!NOTE]
> Questo articolo presuppone che sia già stata configurata un'area di lavoro di Log Analytics di Azure. In caso contrario, vedere [Configurare Log Analytics di Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Aggiungere l'estensione dell'agente tramite l'interfaccia della riga di comando di Azure

Il modo migliore per aggiungere l'agente di OMS al cluster è tramite le API del set di scalabilità di macchine virtuali disponibili con l'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è ancora configurata, passare al portale di Azure e aprire un'istanza [Cloud Shell](../cloud-shell/overview.md) oppure [installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Dopo aver eseguito la richiesta per Cloud Shell, assicurarsi di usare la stessa sottoscrizione della risorsa. A tale scopo, usare `az account show` e assicurarsi che il valore "name" corrisponda a quello della sottoscrizione del cluster.

2. Nel portale passare al gruppo di risorse in cui si trova l'area di lavoro di Log Analytics di Azure. Fare clic sulla risorsa Log Analytics (il tipo della risorsa sarà Log Analytics). Una volta visualizzata la pagina di panoramica della risorsa, fare clic su **Impostazioni avanzate** nella sezione Impostazioni nel menu a sinistra.

    ![Pagina delle proprietà di Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Fare clic su **Server Windows** in caso di creazione di un cluster Windows cluster o su **Server Linux** se si sta creando un cluster Linux. In questa pagina verranno visualizzati `workspace ID` e `workspace key` (elencata come Chiave primaria nel portale). Entrambi serviranno per il passaggio successivo.

4. Eseguire il comando per installare l'agente di OMS sul cluster tramite l'API `vmss extension set` nell'istanza corrente di Cloud Shell:

    Per un cluster Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Per un cluster Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Di seguito è riportato un esempio dell'aggiunta dell'agente di OMS a un cluster Windows.

    ![Comando dell'interfaccia della riga di comando dell'agente di OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. L'operazione di aggiunta dell'agente ai nodi dovrebbe richiedere meno di 15 minuti. Per verificare l'esito dell'aggiunta degli agenti, usare l'API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Aggiungere l'agente tramite il modello di Resource Manager

È disponibile un modello di Resource Manager per [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) che distribuisce un'area di lavoro di Log Analytics di Azure e aggiunge un agente a ogni nodo.

È possibile scaricare e modificare questo modello per distribuire un cluster più adatto alle proprie esigenze.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Visualizzare i contatori delle prestazioni nel portale di Log Analytics

Dopo aver aggiunto l'agente OMS, passare al portale di Log Analytics per scegliere i contatori delle prestazioni da raccogliere. 

1. Nel portale di Azure, passare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<nameOfOMSWorkspace\>** e andare alla relativa pagina di panoramica. Nella parte superiore fare clic sul collegamento per passare al portale di OMS.

2. Nel portale sarà possibile osservare un riquadro sotto forma di grafo per ognuna delle soluzioni abilitate, tra cui uno per Service Fabric. Fare clic su questo riquadro per passare alla soluzione Analisi Service Fabric. 

3. Saranno visualizzati alcuni riquadri con grafi sugli eventi del canale operativo e di Reliable Services. Fare clic sull'icona a forma di ingranaggio sulla destra per passare alla pagina delle impostazioni.

    ![Impostazioni di OMS](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. Nella pagina delle impostazioni fare clic su Dati e scegliere Contatori delle prestazioni di Windows o Contatori delle prestazioni di Linux. È disponibile un elenco di contatori predefiniti che è possibile scegliere di abilitare ed è anche possibile impostare l'intervallo per la raccolta. È anche possibile aggiungere [altri contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) da raccogliere. Il formato corretto è descritto in questo [articolo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

Dopo avere configurato i contatori, tornando alla pagina delle soluzioni sarà possibile osservare il flusso dei dati, visualizzati nei grafi in **Metriche del nodo**. È anche possibile eseguire query sui dati dei contatori delle prestazioni in modo analogo agli eventi cluster e applicare filtri sui nodi, sul nome del contatore delle prestazioni e sui valori usando il linguaggio di query Kusto. 

![Query sui contatori delle prestazioni OMS](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>Passaggi successivi

* Raccogliere i [contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) rilevanti. Per configurare l'agente OMS affinché raccolga contatori di prestazioni specifici, vedere [Configurazione delle origini dati](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Configurare Log Analytics per impostare gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica.
* In alternativa, è possibile raccogliere i contatori delle prestazioni tramite l'[estensione di Diagnostica di Azure e inviarli ad Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)