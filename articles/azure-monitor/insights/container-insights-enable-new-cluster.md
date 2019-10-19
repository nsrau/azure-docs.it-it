---
title: Monitorare un nuovo cluster Azure Kubernetes Service (AKS) | Microsoft Docs
description: Informazioni su come abilitare il monitoraggio per un nuovo cluster Azure Kubernetes Service (AKS) con monitoraggio di Azure per la sottoscrizione di contenitori.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/25/2019
ms.openlocfilehash: 06f4fd56ba5d0152b7e5d3fbfaa31dc4d6c4482d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554114"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Abilitare il monitoraggio di un nuovo cluster Azure Kubernetes Service (AKS)

Questo articolo descrive come configurare monitoraggio di Azure per i contenitori per monitorare il cluster Kubernetes gestito ospitato nel [servizio Kubernetes di Azure](https://docs.microsoft.com/azure/aks/) che si sta preparando per la distribuzione nella sottoscrizione.

È possibile abilitare il monitoraggio di un cluster AKS usando uno dei metodi supportati:

* Interfaccia della riga di comando di Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Abilitare tramite l'interfaccia della riga di comando di Azure

Per abilitare il monitoraggio di un nuovo cluster servizio Azure Kubernetes creato con l'interfaccia della riga di comando di Azure, seguire la procedura nell'articolo introduttivo nella sezione [Creare il cluster servizio Azure Kubernetes](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Se è stata installata l'estensione dell'interfaccia della riga di comando AKS-Preview versione 0.4.12 o successiva, rimuovere tutte le modifiche apportate per abilitare un'estensione di anteprima perché può sostituire il comportamento predefinito dell'interfaccia della riga di comando di Azure perché le funzionalità di anteprima di AKS non sono disponibili nel cloud DOD di Azure

## <a name="enable-using-terraform"></a>Abilitare con Terraform

Se si [distribuisce un cluster del servizio Azure Kubernetes con Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), occorre specificare gli argomenti richiesti nel profilo [per creare un'area di lavoro Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se non si è scelto di specificarne una esistente. 

>[!NOTE]
>Se si sceglie di usare Terraform, è necessario eseguire il provider per Azure RM di Terraform versione 1.17.0 o successiva.

Per aggiungere Monitoraggio di Azure per i contenitori all'area di lavoro, vedere [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e completare il profilo includendo l'argomento [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e specificando **oms_agent**. 

Dopo aver abilitato il monitoraggio e completato tutte le attività di configurazione, è possibile monitorare le prestazioni del cluster in uno dei due modi seguenti:

* Direttamente dal cluster servizio Azure Kubernetes, selezionando **Integrità** nel riquadro sinistro.
* Facendo clic sul riquadro delle **informazioni dettagliate di Monitora Contenitori** nella pagina relativa al cluster servizio Azure Kubernetes selezionato. In Monitoraggio di Azure selezionare **Integrità** nel riquadro sinistro. 

  ![Opzioni per la selezione di Monitoraggio di Azure per contenitori in servizio Azure Kubernetes](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificare la distribuzione dell'agente e della soluzione
Con la versione dell'agente *06072018* o successiva è possibile verificare che l'agente e la soluzione siano stati distribuiti correttamente. Con le versioni precedenti è possibile verificare solo la distribuzione dell'agente.

### <a name="agent-version-06072018-or-later"></a>Versione dell'agente 06072018 o successiva
Eseguire il comando seguente per verificare che l'agente sia distribuito correttamente. 

```
kubectl get ds omsagent --namespace=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Per verificare la distribuzione della soluzione, eseguire questo comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agente con versione precedente alla 06072018

Per verificare che la versione dell'agente Log Analytics rilasciata prima della *06072018* sia distribuita correttamente, eseguire il comando seguente:  

```
kubectl get ds omsagent --namespace=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visualizzare la configurazione con l'interfaccia della riga di comando di Azure
Usare il comando `aks show` per ottenere dettagli, ad esempio se la soluzione è abilitata o meno, qual è l'ID risorsa dell'area di lavoro Log Analytics e dati di riepilogo sul cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Dopo alcuni minuti, il comando viene completato e vengono restituite informazioni in formato JSON sulla soluzione.  I risultati del comando dovrebbero mostrare il profilo del componente aggiuntivo di monitoraggio ed essere simili al seguente output di esempio:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Passaggi successivi

* Se si verificano problemi durante il tentativo di onboarding della soluzione, esaminare la [guida risoluzione dei problemi](container-insights-troubleshoot.md).

* Con il monitoraggio abilitato per acquisire le metriche di integrità sia per i nodi del cluster servizio Azure Kubernetes che per i pod, queste metriche di integrità sono disponibili nel portale di Azure. Per informazioni su come usare Monitoraggio di Azure per contenitori, vedere [Visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).
