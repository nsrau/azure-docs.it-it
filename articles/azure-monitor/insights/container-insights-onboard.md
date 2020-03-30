---
title: Come abilitare Monitor di Azure per i contenitori . Documenti Microsoft
description: Questo articolo descrive come abilitare e configurare Monitoraggio di Azure per i contenitori in modo da comprendere le prestazioni del contenitore e quali problemi correlati alle prestazioni sono stati identificati.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275308"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Come abilitare Monitoraggio di Azure per i contenitoriHow to enable Azure Monitor for containers

Questo articolo offre una panoramica delle opzioni disponibili per configurare Monitoraggio di Azure per i contenitori per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes e ospitati in:This article provides an overview of the options available to setup Azure Monitor for containers to monitor the performance of workloads that are deployed to Kubernetes environments and hosted on:

- [Servizio Azure Kubernetes (AKS)Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS)

- Cluster Kubernetes autogestiti ospitati in Azure tramite [il motore AKS](https://github.com/Azure/aks-engine).

- Cluster Kubernetes autogestiti ospitati in [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) o in locale tramite il motore AKS.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Monitoraggio di Azure per i contenitori può essere abilitato per una o più distribuzioni nuove di Kubernetes usando i metodi supportati seguenti:Azure Monitor for containers can be enabled for new, or one or more existing deployments of Kubernetes using the following supported methods:

- Dal portale di Azure, Azure PowerShell o con l'interfaccia della riga di comando di Azure

- Usando [Terraform e il servizio Azure Kubernetes](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

- **Un'area di lavoro di Log Analytics.A Log Analytics workspace.**

    Monitoraggio di Azure per i contenitori supporta un'area di lavoro di Log Analytics nelle aree elencate in Prodotti di Azure [per area.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

    È possibile creare un'area di lavoro quando si abilita il monitoraggio del nuovo cluster AKS o si lascia che l'esperienza di onboarding crei un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster AKS. Se si è scelto di crearla in autonomia, è possibile procedere attraverso [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../learn/quick-create-workspace.md). Per un elenco delle coppie di mapping supportate usate per l'area di lavoro predefinita, vedere [Mapping di aree per Monitoraggio di Azure per i contenitori.](container-insights-region-mapping.md)

- Si è membri del ruolo di **collaboratore** di Log Analytics per abilitare il monitoraggio del contenitore. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../platform/manage-access.md).

- Si è membri del ruolo **[Proprietario](../../role-based-access-control/built-in-roles.md#owner)** nella risorsa cluster AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Le metriche Prometheus non vengono raccolte per impostazione predefinita. Prima di [configurare l'agente](container-insights-prometheus-integration.md) per raccoglierli, è importante consultare la [documentazione](https://prometheus.io/) di Prometheus per comprendere cosa può essere raschiato e i metodi supportati.

## <a name="supported-configurations"></a>Configurazioni supportate

Quanto segue è ufficialmente supportato con Monitoraggio di Azure per i contenitori.

- Ambienti: Azure Red Hat OpenShift, Kubernetes in locale e Motore AKS in Azure e Azure Stack. Per altre informazioni, vedere [Motore AKS in Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
- Le versioni di Kubernetes e i criteri di supporto sono le stesse delle versioni di [AKS supportate.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall necessarie per consentire all'agente contenitore di comunicare con Monitoraggio di Azure per i contenitori. Tutto il traffico di rete dall'agente è in uscita verso Monitoraggio di Azure.All network traffic from the agent is outbound to Azure Monitor.

|Risorsa agente|Porte |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| .monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall per Azure Cina.

|Risorsa agente|Porte |Descrizione | 
|--------------|------|-------------|
| ods.opinsights.azure.cn .ods.opinsights.azure.cn | 443 | Inserimento di dati |
| Oms.opinsights.azure.cn .oms.opinsights.azure.cn | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Utilizzato per il monitoraggio della connettività in uscita. |
| microsoft.com | 80 | Utilizzato per la connettività di rete. Questa operazione è necessaria solo se la versione dell'immagine dell'agente è ciprod09262019 o versioni precedenti. |
| dc.services.visualstudio.com | 443 | For per la telemetria dell'agente usando Azure Public Cloud Application Insights. |

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall per Azure US per enti pubblici.

|Risorsa agente|Porte |Descrizione | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Inserimento di dati |
| *.oms.opinsights.azure.us | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Utilizzato per il monitoraggio della connettività in uscita. |
| microsoft.com | 80 | Utilizzato per la connettività di rete. Questa operazione è necessaria solo se la versione dell'immagine dell'agente è ciprod09262019 o versioni precedenti. |
| dc.services.visualstudio.com | 443 | Per la telemetria dell'agente tramite Azure Public Cloud Application Insights.For agent telemetry using Azure Public Cloud Application Insights. |

## <a name="components"></a>Componenti

La possibilità di monitorare le prestazioni si basa su un agente Log Analytics con contenitori per Linux sviluppato specificamente per Monitoraggio di Azure per i contenitori. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. La versione dell'agente è microsoft/oms:ciprod04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *mmggaaaa*.

>[!NOTE]
>With the preview release of Windows Server support for AKS, an AKS cluster with Windows Server nodes do not have an agent installed to collect data and forward to Azure Monitor. Al contrario, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e inoltra i dati ad Monitoraggio di Azure per conto di tutti i nodi Windows nel cluster.  
>

Quando viene rilasciata una nuova versione dell'agente, questo viene aggiornato automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Per seguire le versioni rilasciate, consultare gli [annunci relativi alla versione dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se è già stato distribuito un cluster servizio Azure Kubernetes, si abilita il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile usare `kubectl` per aggiornare, eliminare, distribuire o ridistribuire l'agente.
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Si abilita Monitoraggio di Azure per i contenitori usando uno dei metodi seguenti descritti nella tabella seguente.

| Stato di distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Nuovo cluster AKS Kubernetes | [Creare un cluster AKS usando l'interfaccia della riga di comando di AzureCreate AKS cluster using Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| You can enable monitoring of a new AKS cluster that you create with Azure CLI. |
| | [Creare un cluster AKS utilizzando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio di un nuovo cluster AKS creato utilizzando lo strumento Open Source Terraform. |
| | [Creare un cluster OpenShift usando un modello di Azure Resource ManagerCreate OpenShift cluster using an Azure Resource Manager template](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio di un nuovo cluster OpenShift creato con un modello di Azure Resource Manager preconfigurato. |
| | [Creare un cluster OpenShift usando l'interfaccia della riga di comando di AzureCreate OpenShift cluster using Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | È possibile abilitare il monitoraggio durante la distribuzione di un nuovo cluster OpenShift usando l'interfaccia della riga di comando di Azure.You can enable monitoring while deploying a new OpenShift cluster using Azure CLI. |
| Cluster AKS Kubernetes esistente | [Abilitare il cluster AKS usando l'interfaccia della riga di comando di AzureEnable for AKS cluster using Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio di un cluster AKS già distribuito usando l'interfaccia della riga di comando di Azure.You can enable monitoring of an AKS cluster already deployed using Azure CLI. |
| |[Abilitare per il cluster AKS tramite TerraformEnable for AKS cluster using Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio di un cluster AKS già distribuito utilizzando lo strumento open source Terraform. |
| | [Abilitare il cluster AKS da Monitoraggio di AzureEnable for AKS cluster from Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio di uno o più cluster AKS già distribuiti dalla pagina multicluster in Monitoraggio di Azure.You can enable monitoring of one or more AKS clusters already deployed from the multi-cluster page in Azure Monitor. |
| | [Abilitare dal cluster AKSEnable from AKS cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster AKS nel portale di Azure.You can enable monitoring directly from an AKS cluster in the Azure portal. |
| | [Abilitare il cluster AKS usando un modello di Azure Resource ManagerEnable for AKS cluster using an Azure Resource Manager template](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio di un cluster AKS con un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per cluster Kubernetes ibridoEnable for hybrid Kubernetes cluster](container-insights-hybrid-setup.md) | È possibile abilitare il monitoraggio di un motore AKS ospitato in Azure Stack o per Kubernetes ospitato in locale. |
| | [Abilitare per il cluster OpenShift usando un modello di Azure Resource ManagerEnable for OpenShift cluster using an Azure Resource Manager template](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio di un cluster OpenShift esistente con un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster OpenShift da Monitoraggio di AzureEnable for OpenShift cluster from Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | È possibile abilitare il monitoraggio di uno o più cluster OpenShift già distribuiti dalla pagina multicluster in Monitoraggio di Azure.You can enable monitoring of one or more OpenShift clusters already deployed from the multi-cluster page in Azure Monitor. |

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato, è possibile iniziare ad analizzare le prestazioni dei cluster Kubernetes ospitati nel servizio Azure Kubernetes (AKS), Azure Stack o in un altro ambiente. Per informazioni su come usare Monitoraggio di Azure per i contenitori, vedere [Visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md).
