---
title: Monitorare un cluster Azure Kubernetes Service (AKS) distribuito | Microsoft Docs
description: Informazioni su come abilitare il monitoraggio di un cluster Azure Kubernetes Service (AKS) con monitoraggio di Azure per i contenitori già distribuiti nella sottoscrizione.
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
ms.date: 09/12/2019
ms.author: magoedte
ms.openlocfilehash: 0153d39e1307458baa920d8e9107c8931242014e
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996258"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Abilitare il monitoraggio del cluster di Azure Kubernetes Service (AKS) già distribuito

Questo articolo descrive come configurare monitoraggio di Azure per i contenitori per monitorare il cluster Kubernetes gestito ospitato nel [servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/) che è già stato distribuito nella sottoscrizione.

È possibile abilitare il monitoraggio di un cluster AKS già distribuito usando uno dei metodi supportati:

* Interfaccia della riga di comando di Azure
* Terraform
* [Da monitoraggio di Azure](#enable-from-azure-monitor-in-the-portal) o [direttamente dal cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) nel portale di Azure 
* Con il [modello di Azure Resource Manager fornito](#enable-using-an-azure-resource-manager-template) tramite il cmdlet `New-AzResourceGroupDeployment` Azure PowerShell o con l'interfaccia della riga di comando di Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Abilitare tramite l'interfaccia della riga di comando di Azure

La procedura seguente abilita il monitoraggio del cluster servizio Azure Kubernetes usando l'interfaccia della riga di comando di Azure. In questo esempio non è necessario creare o specificare un'area di lavoro esistente. Questo comando semplifica il processo creando un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster servizio Azure Kubernetes se non ne esiste già una nella regione.  L'area di lavoro predefinita creata ricorda il formato di *DefaultWorkspace-\<GUID>-\<Region>* .  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

L'output sarà simile al seguente:

```azurecli
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Eseguire l'integrazione con un'area di lavoro esistente

Se si preferisce eseguire l'integrazione con un'area di lavoro esistente, attenersi alla procedura seguente per identificare prima di tutto l'ID risorsa completo dell'area `--workspace-resource-id` di lavoro di log Analytics necessaria per il parametro, quindi eseguire il comando per abilitare il componente aggiuntivo di monitoraggio rispetto a area di lavoro specificata.  

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiare il valore per **SubscriptionId**.

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito. 

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro, quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics sotto l' **ID**campo.
 
4. Eseguire il comando seguente per abilitare il componente aggiuntivo di monitoraggio, sostituendo il valore per `--workspace-resource-id` il parametro. Il valore della stringa deve essere racchiuso tra virgolette doppie:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id  “/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>”
    ```

    L'output sarà simile al seguente:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Abilitare con Terraform

1. Aggiungere il profilo del componente aggiuntivo **oms_agent** alla [risorsa azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) esistente

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Aggiungere [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) seguendo i passaggi nella documentazione di Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Abilitare da monitoraggio di Azure nel portale 

Per abilitare il monitoraggio del cluster servizio Azure Kubernetes nel portale di Azure da Monitoraggio di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare **Monitoraggio**. 

2. Selezionare **Contenitori** dall'elenco.

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. Nell'elenco dei cluster non monitorati, trovare il contenitore e fare clic su **Abilita**.   

5. Se nella pagina **Onboarding di Monitoraggio di Azure per i contenitori** è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione del cluster, selezionarla nell'elenco a discesa.  
    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuito il contenitore servizio Azure Kubernetes nella sottoscrizione. 

    ![Abilitare il monitoraggio delle informazioni dettagliate sui contenitori servizio Azure Kubernetes](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui viene distribuito il contenitore servizio Azure Kubernetes. 
 
Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Abilitare direttamente dal cluster AKS nel portale

Per abilitare il monitoraggio direttamente da uno dei cluster AKS nell'portale di Azure, seguire questa procedura:

1. Nel portale di Azure fare clic su **Tutti i servizi**. 

2. Nell'elenco delle risorse digitare **Contenitori**.  L'elenco viene filtrato in base all'input. 

3. Selezionare **Servizi Kubernetes**.  

    ![Collegamento ai servizi Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Nell'elenco dei contenitori selezionare un contenitore.

5. Nella pagina di panoramica del contenitore selezionare **Monitora contenitori**.  

6. Se nella pagina **Onboarding di Monitoraggio di Azure per i contenitori** è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione del cluster, selezionarla dall'elenco a discesa.  
    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuito il contenitore servizio Azure Kubernetes nella sottoscrizione. 

    ![Abilitare il monitoraggio dell'integrità dei contenitori servizio Azure Kubernetes](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui viene distribuito il contenitore servizio Azure Kubernetes. 
 
Dopo aver abilitato il monitoraggio, può essere necessario attendere circa 15 minuti prima di poter visualizzare i dati operativi per il cluster. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Abilitare l'uso di un modello di Azure Resource Manager

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

* L'ID risorsa del contenitore servizio Azure Kubernetes. 
* Il gruppo di risorse in cui viene distribuito il cluster.

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.
>

Prima di abilitare il monitoraggio tramite Azure PowerShell o CLI, è necessario creare l'area di lavoro Log Analytics. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Salvare questo file come **existingClusterOnboarding.json** in una cartella locale.

3. Incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Modificare i valori per **aksResourceId** e **aksResourceLocation** usando i valori nella pagina **Panoramica di AKS** per il cluster AKS. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro. 

    Modificare i valori di **aksResourceTagValues** in modo che corrispondano ai valori di tag esistenti specificati per il cluster AKS.

5. Salvare questo file come **existingClusterParam.json** in una cartella locale.

6. A questo punto è possibile distribuire il modello. 

   * Per eseguire la distribuzione con Azure PowerShell, usare i comandi seguenti nella cartella che contiene il modello:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       
       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Per la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
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
