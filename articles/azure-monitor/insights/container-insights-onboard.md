---
title: Abilitare monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come abilitare e configurare monitoraggio di Azure per i contenitori in modo che sia possibile comprendere il modo in cui il contenitore sta eseguendo e quali sono stati identificati i problemi relativi alle prestazioni.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 5131d7b8a357075345b5165398d5fa9fc06b5ad8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499036"
---
# <a name="enable-azure-monitor-for-containers"></a>Abilitare monitoraggio di Azure per i contenitori

Questo articolo fornisce una panoramica delle opzioni disponibili per la configurazione di monitoraggio di Azure per i contenitori per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes e ospitati in:

- [Servizio Azure Kubernetes (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versioni 3. x e 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versione 4. x  
- Un [cluster Kubernetes abilitato per Arc](../../azure-arc/kubernetes/overview.md)

È anche possibile monitorare le prestazioni dei carichi di lavoro distribuiti nei cluster Kubernetes autogestiti ospitati in:
- Azure, usando il [motore AKS](https://github.com/Azure/aks-engine)
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) o in locale usando il motore AKS.

È possibile abilitare monitoraggio di Azure per i contenitori per una nuova distribuzione o per una o più distribuzioni esistenti di Kubernetes usando uno dei metodi supportati seguenti:

- Portale di Azure
- Azure PowerShell
- Interfaccia della riga di comando di Azure
- [Bonifica e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

- Si dispone di un'area di lavoro Log Analytics.

   Il monitoraggio di Azure per i contenitori supporta un'area di lavoro Log Analytics nelle aree elencate nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   È possibile creare un'area di lavoro quando si Abilita il monitoraggio per il nuovo cluster AKS oppure è possibile consentire all'esperienza di onboarding di creare un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster AKS. 
   
   Se si sceglie di creare manualmente l'area di lavoro, è possibile crearla tramite: 
   - [Azure Resource Manager](../platform/template-workspace-configuration.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Il portale di Azure](../learn/quick-create-workspace.md) 
   
   Per un elenco delle coppie di mapping supportate da usare per l'area di lavoro predefinita, vedere [mapping delle aree per il monitoraggio di Azure per i contenitori](container-insights-region-mapping.md).

- L'utente è membro del gruppo *log Analytics Contributor* per abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../platform/manage-access.md).

- Si è un membro del gruppo [ *owner* ](../../role-based-access-control/built-in-roles.md#owner) sulla risorsa cluster AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Per visualizzare i dati di monitoraggio, è necessario avere [*log Analytics ruolo Reader*](../platform/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics, configurato con monitoraggio di Azure per i contenitori.

- Le metriche Prometeo non vengono raccolte per impostazione predefinita. Prima di [configurare l'agente](container-insights-prometheus-integration.md) per raccogliere le metriche, è importante esaminare la [documentazione di Prometeo](https://prometheus.io/) per comprendere quali dati possono essere frammentati e quali metodi sono supportati.

## <a name="supported-configurations"></a>Configurazioni supportate

Monitoraggio di Azure per i contenitori supporta ufficialmente le configurazioni seguenti:

- Ambienti: Azure Red Hat OpenShift, Kubernetes locale e il motore AKS in Azure e Azure Stack. Per ulteriori informazioni, vedere [il motore AKS in Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Le versioni di Kubernetes e i criteri di supporto sono identici [a quelli supportati in Azure Kubernetes Service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie per la comunicazione dell'agente in contenitori con monitoraggio di Azure per i contenitori. Tutto il traffico di rete dall'agente è in uscita in monitoraggio di Azure.

|Risorsa dell'agente|Porta |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall per Azure Cina 21Vianet:

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Inserimento di dati |
| `*.oms.opinsights.azure.cn` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per la telemetria dell'agente che usa il cloud pubblico di Azure Application Insights |

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall per il governo degli Stati Uniti di Azure:

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Inserimento di dati |
| `*.oms.opinsights.azure.us` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per la telemetria dell'agente che usa il cloud pubblico di Azure Application Insights |

## <a name="components"></a>Componenti

La possibilità di monitorare le prestazioni si basa su un agente Log Analytics in contenitori per Linux appositamente sviluppato per il monitoraggio di Azure per i contenitori. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. 

La versione dell'agente è Microsoft/OMS: ciprod04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *MMGGAAAA*.

>[!NOTE]
>Con la disponibilità generale del supporto di Windows Server per AKS, un cluster AKS con nodi di Windows Server dispone di un agente di anteprima installato come Pod daemonset in ogni singolo nodo di Windows Server per raccogliere i log e inviarli a Log Analytics. Per le metriche delle prestazioni, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e invia i dati a monitoraggio di Azure per conto di tutti i nodi Windows del cluster.

Quando viene rilasciata una nuova versione dell'agente, questa viene aggiornata automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS). Per tenere traccia delle versioni rilasciate, vedere [annunci sulla versione degli agenti](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Se è già stato distribuito un cluster AKS, è stato abilitato il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile utilizzare `kubectl` per aggiornare, eliminare, ridistribuire o distribuire l'agente.
>
> Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Per abilitare monitoraggio di Azure per i contenitori, usare uno dei metodi descritti nella tabella seguente:

| Stato distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Nuovo cluster Kubernetes | [Creare un cluster AKS usando l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| È possibile abilitare il monitoraggio per un nuovo cluster AKS creato con l'interfaccia della riga di comando di Azure. |
| | [Creare un cluster AKS usando la bonifica](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio per un nuovo cluster AKS creato tramite lo strumento open source bonifica. |
| | [Creare un cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un nuovo cluster OpenShift creato usando un modello di Azure Resource Manager preconfigurato. |
| | [Creare un cluster OpenShift usando l'interfaccia della riga di comando di Azure](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | È possibile abilitare il monitoraggio quando si distribuisce un nuovo cluster OpenShift usando l'interfaccia della riga di comando di Azure. |
| Cluster Kubernetes esistente | [Abilitare il monitoraggio di un cluster AKS usando l'interfaccia della riga di comando di Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio per un cluster AKS già distribuito usando l'interfaccia della riga di comando di Azure. |
| |[Abilitare per il cluster AKS usando la bonifica](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio per un cluster AKS già distribuito usando lo strumento open source bonifica. |
| | [Abilitare per il cluster AKS da monitoraggio di Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio per uno o più cluster AKS già distribuiti dalla pagina multicluster in monitoraggio di Azure. |
| | [Abilita dal cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster AKS nel portale di Azure. |
| | [Abilitare per il cluster AKS usando un modello di Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio per un cluster AKS usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilita per il cluster Kubernetes ibrido](container-insights-hybrid-setup.md) | È possibile abilitare il monitoraggio per il motore AKS ospitato in Azure Stack o per un cluster Kubernetes ospitato in locale. |
| | [Abilitare per il cluster Kubernetes abilitato per Arc](container-insights-enable-arc-enabled-clusters.md). | È possibile abilitare il monitoraggio per i cluster Kubernetes ospitati all'esterno di Azure e abilitati con Azure Arc. |
| | [Abilitare per il cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un cluster OpenShift esistente usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster OpenShift da monitoraggio di Azure](container-insights-azure-redhat-setup.md#from-the-azure-portal) | È possibile abilitare il monitoraggio per uno o più cluster OpenShift già distribuiti dalla pagina multicluster in monitoraggio di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato abilitato il monitoraggio, è possibile iniziare ad analizzare le prestazioni dei cluster Kubernetes ospitati nel servizio Azure Kubernetes (AKS), Azure Stack o in un altro ambiente. Per informazioni su come usare monitoraggio di Azure per i contenitori, vedere [visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md).
