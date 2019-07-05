---
title: Come abilitare il monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive come abilitare e configurare monitoraggio di Azure per contenitori in modo che è possibile comprendere le prestazioni del contenitore e sono stati identificati i problemi relativi alle prestazioni.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: magoedte
ms.openlocfilehash: aff2dcebdab1ad93b8b1958164764b66eb755d1c
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514496"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Come abilitare il monitoraggio di Azure per contenitori

Questo articolo offre una panoramica delle opzioni disponibili per configurare il monitoraggio di Azure per contenitori per monitorare le prestazioni dei carichi di lavoro che vengono distribuiti per gli ambienti Kubernetes e ospitato in [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Monitoraggio di Azure per i contenitori può essere abilitato per le nuove distribuzioni oppure per una o più distribuzioni esistenti del servizio Azure Kubernetes usando i metodi supportati seguenti:

* Dal portale di Azure, Azure PowerShell, o con CLI di Azure
* Usando [Terraform e il servizio Azure Kubernetes](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre degli elementi seguenti:

* **Un'area di lavoro di Log Analitica.**

    Monitoraggio di Azure per contenitori supporta un'area di lavoro di Log Analitica nelle aree elencate in Azure [prodotti in base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), l'area di esclusione **US Gov Virginia**.

    È possibile creare un'area di lavoro quando si abilita il monitoraggio del cluster servizio contenitore di AZURE nuova o consentire l'esperienza di onboarding di creare un'area di lavoro predefinito nel gruppo di risorse predefinito della sottoscrizione del cluster servizio contenitore di AZURE. Se si è scelto di crearla in autonomia, è possibile procedere attraverso [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../learn/quick-create-workspace.md). Per un elenco di coppie di mapping supportati utilizzati per l'area di lavoro predefinita, vedere [mapping dei paesi per monitoraggio di Azure per contenitori](container-insights-region-mapping.md).

* Si è un membro del **ruolo di collaboratore di Log Analitica** per abilitare il monitoraggio di contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../platform/manage-access.md).

* Si è un membro del **[proprietario](../../role-based-access-control/built-in-roles.md#owner)** ruolo sulla risorsa del cluster servizio contenitore di AZURE.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Componenti

La capacità di monitorare le prestazioni si basa su un agente di Log Analytics per Linux incluso in un contenitore sviluppato appositamente per Monitoraggio di Azure per i contenitori. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. La versione dell'agente è microsoft/oms:ciprod04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *mmggaaaa*.

>[!NOTE]
>Con la versione di anteprima del supporto di Windows Server per AKS, un cluster AKS con nodi di Windows Server non è un agente installato per raccogliere i dati e inoltrarli a monitoraggio di Azure. Al contrario, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e inoltra i dati da monitoraggio di Azure per conto di tutti i nodi di Windows nel cluster.  
>

Quando viene rilasciata una nuova versione dell'agente, questo viene aggiornato automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Per seguire le versioni rilasciate, consultare gli [annunci relativi alla versione dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se è già stato distribuito un cluster servizio Azure Kubernetes, si abilita il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile usare `kubectl` per aggiornare, eliminare, distribuire o ridistribuire l'agente.
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Si abilita monitoraggio di Azure per contenitori usando uno dei seguenti metodi descritti nella tabella seguente.

| Stato di distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Nuovo cluster servizio contenitore di AZURE | [Creare cluster usando Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| È possibile abilitare il monitoraggio di un nuovo cluster servizio contenitore di AZURE create con CLI di Azure. |
| | [Creare cluster usando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio di un nuovo cluster servizio contenitore di AZURE che si creano utilizzando lo strumento open source Terraform. |
| Cluster servizio contenitore di AZURE esistente | [Abilitare l'uso della riga di comando di Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio di un cluster del servizio contenitore di AZURE già distribuito tramite la CLI di Azure. |
| |[Abilitare l'uso di Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio di un cluster del servizio contenitore di AZURE già distribuito utilizzando lo strumento open source Terraform. |
| | [Abilitare da monitoraggio di Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio di uno o più cluster servizio contenitore di AZURE già distribuito dalla pagina di multi-cluster servizio contenitore di AZURE in Monitoraggio di Azure. |
| | [Abilitare dal cluster servizio contenitore di AZURE](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster servizio contenitore di AZURE nel portale di Azure. |
| | [Abilitare l'uso di un modello Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio di un cluster del servizio contenitore di AZURE con un modello di Azure Resource Manager preconfigurato. |

## <a name="next-steps"></a>Passaggi successivi

* Con il monitoraggio abilitato per acquisire le metriche di integrità sia per i nodi del cluster servizio Azure Kubernetes che per i pod, queste metriche di integrità sono disponibili nel portale di Azure. Per informazioni su come usare Monitoraggio di Azure per contenitori, vedere [Visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).
