---
title: Come abilitare monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come abilitare e configurare monitoraggio di Azure per i contenitori, in modo da comprendere il modo in cui il contenitore sta eseguendo e quali sono stati identificati i problemi relativi alle prestazioni.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 43016cfb72b90a74ce1313ad2d2316228d743f5f
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195339"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Come abilitare monitoraggio di Azure per i contenitori

Questo articolo offre una panoramica delle opzioni disponibili per configurare monitoraggio di Azure per i contenitori per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes e ospitati in:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS)

- Motore AKS in [Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) o Kubernetes distribuito in locale.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

È possibile abilitare monitoraggio di Azure per i contenitori per nuove distribuzioni di Kubernetes o per una o più distribuzioni esistenti usando i metodi supportati seguenti:

- Dal portale di Azure, Azure PowerShell o con l'interfaccia della riga di comando di Azure

- Usando [Terraform e il servizio Azure Kubernetes](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

- **Area di lavoro Log Analytics.**

    Il monitoraggio di Azure per i contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in prodotti Azure in [base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    È possibile creare un'area di lavoro quando si Abilita il monitoraggio del nuovo cluster AKS o lasciare che l'esperienza di onboarding crei un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster AKS. Se si è scelto di crearla in autonomia, è possibile procedere attraverso [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../learn/quick-create-workspace.md). Per un elenco delle coppie di mapping supportate usate per l'area di lavoro predefinita, vedere [mapping delle aree per il monitoraggio di Azure per i contenitori](container-insights-region-mapping.md).

- L'utente è membro del **ruolo di collaboratore log Analytics** per abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../platform/manage-access.md).

- Si è un membro del ruolo **[proprietario](../../role-based-access-control/built-in-roles.md#owner)** sulla risorsa cluster AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Le metriche Prometeo non vengono raccolte per impostazione predefinita. Prima di [configurare l'agente](container-insights-prometheus-integration.md) per raccoglierli, è importante esaminare la [documentazione](https://prometheus.io/) di Prometeo per capire cosa può essere frammentato e metodi supportati.

## <a name="supported-configurations"></a>Configurazioni supportate

Il codice seguente è ufficialmente supportato con monitoraggio di Azure per i contenitori.

- Ambienti: Azure Red Hat OpenShift, Kubernetes locale e il motore AKS in Azure e Azure Stack. Per altre informazioni, vedere [motore AKS su Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Le versioni di Kubernetes e i criteri di supporto corrispondono alle versioni di [AKS supportate](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall necessarie per la comunicazione dell'agente in contenitori con monitoraggio di Azure per i contenitori. Tutto il traffico di rete dall'agente è in uscita in monitoraggio di Azure.

|Risorsa agente|Porte |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall per Azure Cina.

|Risorsa agente|Porte |DESCRIZIONE | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Inserimento di dati |
| *. oms.opinsights.azure.cn | 443 | Onboarding di OMS |
| *.blob.core.windows.net | 443 | Usato per il monitoraggio della connettività in uscita. |
| microsoft.com | 80 | Usato per la connettività di rete. Questa operazione è necessaria solo se la versione dell'immagine dell'agente è ciprod09262019 o precedente. |
| dc.services.visualstudio.com | 443 | Per per la telemetria degli agenti che usa il cloud pubblico di Azure Application Insights. |

Le informazioni nella tabella seguente elencano le informazioni di configurazione del proxy e del firewall per il governo degli Stati Uniti di Azure.

|Risorsa agente|Porte |DESCRIZIONE | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Inserimento di dati |
| *.oms.opinsights.azure.us | 443 | Onboarding di OMS |
| *.blob.core.windows.net | 443 | Usato per il monitoraggio della connettività in uscita. |
| microsoft.com | 80 | Usato per la connettività di rete. Questa operazione è necessaria solo se la versione dell'immagine dell'agente è ciprod09262019 o precedente. |
| dc.services.visualstudio.com | 443 | Per la telemetria degli agenti usando il cloud pubblico di Azure Application Insights. |

## <a name="components"></a>Componenti

La possibilità di monitorare le prestazioni si basa su un agente Log Analytics in contenitori per Linux appositamente sviluppato per il monitoraggio di Azure per i contenitori. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. La versione dell'agente è microsoft/oms:ciprod04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *mmggaaaa*.

>[!NOTE]
>Con la versione di anteprima del supporto di Windows Server per AKS, per un cluster AKS con nodi di Windows Server non è installato un agente per la raccolta di dati e l'invio a monitoraggio di Azure. Al contrario, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e invia i dati a monitoraggio di Azure per conto di tutti i nodi Windows del cluster.  
>

Quando viene rilasciata una nuova versione dell'agente, questo viene aggiornato automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Per seguire le versioni rilasciate, consultare gli [annunci relativi alla versione dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se è già stato distribuito un cluster servizio Azure Kubernetes, si abilita il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile usare `kubectl` per aggiornare, eliminare, distribuire o ridistribuire l'agente.
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Per abilitare monitoraggio di Azure per i contenitori, usare uno dei metodi seguenti descritti nella tabella seguente.

| Stato della distribuzione | Metodo | DESCRIZIONE |
|------------------|--------|-------------|
| Nuovo cluster Kubernetes | [Creare cluster AKS con l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| È possibile abilitare il monitoraggio di un nuovo cluster AKS creato con l'interfaccia della riga di comando di Azure. |
| | [Creare un cluster AKS usando la bonifica](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio di un nuovo cluster AKS creato usando lo strumento open source per la bonifica. |
| | [Creare un cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio di un nuovo cluster OpenShift creato con un modello di Azure Resource Manager preconfigurato. |
| Cluster Kubernetes esistente | [Abilitare per il cluster AKS con l'interfaccia della riga di comando](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio di un cluster AKS già distribuito usando l'interfaccia della riga di comando di Azure. |
| |[Abilitare per il cluster AKS usando la bonifica](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio di un cluster AKS già distribuito usando lo strumento open source per la bonifica. |
| | [Abilitare per il cluster AKS da monitoraggio di Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio di uno o più cluster AKS già distribuiti dalla pagina multicluster in monitoraggio di Azure. |
| | [Abilita dal cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster AKS nel portale di Azure. |
| | [Abilitare per il cluster AKS usando un modello di Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio di un cluster AKS con un modello di Azure Resource Manager preconfigurato. |
| | [Abilita per il cluster Kubernetes ibrido](container-insights-hybrid-setup.md) | È possibile abilitare il monitoraggio di un motore AKS ospitato in Azure Stack o per Kubernetes ospitato in locale. |
| | [Abilitare per il cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio di un cluster OpenShift esistente con un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster OpenShift da monitoraggio di Azure](container-insights-azure-redhat-setup.md#from-the-azure-portal) | È possibile abilitare il monitoraggio di uno o più cluster OpenShift già distribuiti dalla pagina multicluster in monitoraggio di Azure. |

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato, è possibile iniziare ad analizzare le prestazioni dei cluster Kubernetes ospitati in Azure Kubernetes Service (AKS), Azure Stack o in un altro ambiente. Per informazioni su come usare monitoraggio di Azure per i contenitori, vedere [visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md).
