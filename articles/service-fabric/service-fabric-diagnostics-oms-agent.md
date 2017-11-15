---
title: Azure Service Fabric - Configurazione del monitoraggio con l'agente di OMS | Microsoft Docs
description: Informazioni su come configurare l'agente di OMS per il monitoraggio di contenitori e contatori delle prestazioni per i cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Aggiungere l'agente di OMS a un cluster

In questo articolo vengono illustrati i passaggi necessari per aggiungere l'agente di OMS come estensione del set di scalabilità di macchine virtuali al cluster e connetterlo all'area di lavoro di Log Analytics di OMS esistente. Ciò consente la raccolta dei dati di diagnostica relativi a contenitori, applicazioni e monitoraggio delle prestazioni. Aggiungendolo come un'estensione, Azure Resource Manager ne garantisce l'installazione su ogni nodo, anche in caso di ridimensionamento del cluster.

> [!NOTE]
> In questo articolo si presuppone che sia già stata configurata un'area di lavoro di Log Analytics di OMS. In caso contrario, vedere [Configurare Log Analytics di OMS](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Aggiungere l'estensione dell'agente tramite l'interfaccia della riga di comando di Azure

Il modo migliore per aggiungere l'agente di OMS al cluster è tramite le API del set di scalabilità di macchine virtuali disponibili con l'interfaccia della riga di comando di Azure. Se l'interfaccia della riga di comando di Azure non è ancora configurata, passare al portale di Azure e aprire un'istanza [Cloud Shell](../cloud-shell/overview.md) oppure [installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Dopo aver eseguito la richiesta per Cloud Shell, assicurarsi di usare la stessa sottoscrizione della risorsa. A tale scopo, usare `az account show` e assicurarsi che il valore "name" corrisponda a quello della sottoscrizione del cluster.

2. Nel portale passare al gruppo di risorse in cui si trova l'area di lavoro di OMS. Fare clic nella risorsa di Log Analytics (il tipo della risorsa sarà Log Analytics) nel riquadro di spostamento a destra, scorrere verso il basso e fare clic su **Proprietà**.

    ![Pagina delle proprietà di OMS](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Prendere nota del valore di `workspaceId`. 

3. È necessario anche `workspaceKey` per distribuire l'agente. Per ottenere la chiave, fare clic su **Impostazioni avanzate**, sotto la sezione *Impostazioni* nel riquadro di spostamento a sinistra. Fare clic su **Server Windows** in caso di creazione di un cluster Windows cluster o su **Server Linux** se si sta creando un cluster Linux. Per distribuire gli agenti, è necessario usare la *chiave primaria* visualizzata per impostare `workspaceKey`.

4. Eseguire il comando per installare l'agente di OMS sul cluster tramite l'API `vmss extension set` nell'istanza corrente di Cloud Shell:

    Per un cluster Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Per un cluster Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Di seguito è riportato un esempio dell'aggiunta dell'agente di OMS a un cluster Windows.

    ![Comando dell'interfaccia della riga di comando dell'agente di OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    L'operazione di aggiunta dell'agente ai nodi dovrebbe richiedere meno di 15 minuti. Per verificare l'esito dell'aggiunta degli agenti, usare l'API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Aggiungere l'agente tramite il modello di Resource Manager

È disponibile un modello di Resource Manager per [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) che distribuisce un'area di lavoro di Log Analytics di OMS e aggiunge un agente a ogni nodo.

È possibile scaricare e modificare questo modello per distribuire un cluster più adatto alle proprie esigenze.

## <a name="next-steps"></a>Passaggi successivi

* Raccogliere i [contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) rilevanti. Per configurare l'agente di OMS affinché acquisisca contatori delle prestazioni specifici, passare al portale di OMS (il relativo collegamento è disponibile nella parte superiore della risorsa di Log Analytics di OMS). Fare clic su **Home > Impostazioni > Dati > Contatori delle prestazioni di Windows** o **Contatori delle prestazioni di Linux** e scegliere i contatori per i quali raccogliere i dati.
* Configurare OMS per gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica
