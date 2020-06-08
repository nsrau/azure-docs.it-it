---
title: Eseguire l'onboarding di Azure Arc con Monitoraggio di Azure per i contenitori (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Eseguire l'onboarding di Azure Arc con Monitoraggio di Azure per i contenitori
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680739"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Eseguire l'onboarding di Monitoraggio di Azure per i contenitori con Arc (anteprima)

Eseguire l'onboarding dei [contenitori abilitati per Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) in cluster Kubernetes abilitati per Azure Arc.

## <a name="before-you-begin"></a>Prima di iniziare

* [Versioni di Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Distribuzioni Linux per i nodi del cluster (master e lavoro) - Ubuntu (18.04 LTS e 16.04 LTS)
* Almeno autorizzazione al ruolo Controllo degli accessi in base al ruolo di Collaboratore nella sottoscrizione di Azure del cluster Kubernetes abilitato per Azure Arc
* ID risorsa di Azure completo del cluster Kubernetes abilitato per Azure Arc
* Contesto Kubeconfig del cluster Kubernetes
* L'agente di monitoraggio richiede che cAdvisor nel kubelet sia in esecuzione su una porta protetta 10250 o su una porta non protetta 10255 in tutti i nodi per eseguire il pull delle metriche sulle prestazioni   
* È consigliabile configurare la porta cAdvisor del kubelet per proteggere la porta 10250.
* L'agente di monitoraggio richiede le porte in uscita e i domini seguenti per inviare i dati di monitoraggio al back-end di Monitoraggio di Azure (se esiste un blocco tramite proxy/firewall)
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

### <a name="using-helm-chart"></a>Uso del grafico HELM

### <a name="option-1-using-powershell--script"></a>Opzione 1: uso dello script PowerShell

1. Scaricare lo script di onboarding

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Installare [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) nel computer di sviluppo per eseguire lo script di onboarding di PowerShell.

3. Accedere ad Azure

    ```console
    az login --use-device-code
    ```

4. Eseguire lo script seguente con il cluster Azure Arc K8s Cluster ResourceId e il contesto del cluster Kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Opzione 2: uso dello script Bash

> **Suggerimento:** lo script usa le funzionalità di Bash 4. Assicurarsi che la versione Bash sia aggiornata. È possibile controllare la versione corrente con `bash --version`.

1. Scaricare lo script di onboarding

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Eseguire lo script seguente con il cluster Azure Arc K8s Cluster ResourceId e il contesto del cluster Kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati dell'agente

Per impostazione predefinita, l'agente non raccoglie i log stdout e stderr dei contenitori nello spazio dei nomi kube-system.
Vedere https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config per configurare l'agente con le impostazioni di raccolta dati desiderate.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurare lo scorporo delle metriche Prometheus

Monitoraggio di Azure per i contenitori consente di scorporare le metriche Prometheus e di inserirle nel back-end di Monitoraggio di Azure.
Per istruzioni su come configurare lo scorporo delle metriche Prometheus, vedere https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration.

## <a name="user-interface"></a>Interfaccia utente

Passare a https://aka.ms/azmon-containers-azurearc per visualizzare il cluster caricato.

## <a name="disable-monitoring"></a>Disabilitare il monitoraggio

Se per qualche motivo si vuole disabilitare il monitoraggio, è sufficiente eliminare il grafico HELM di Monitoraggio di Azure per i contenitori per arrestare la raccolta e l'inserimento dei dati di monitoraggio nel back-end di Monitoraggio di Azure per i contenitori.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)

