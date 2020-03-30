---
title: Configurare i cluster Kubernetes ibridi con Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive come configurare Monitoraggio di Azure per i contenitori per monitorare i cluster Kubernetes ospitati in Azure Stack o in un altro ambiente.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198055"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configurare cluster Kubernetes ibridi con Monitoraggio di Azure per i contenitoriConfigure hybrid Kubernetes clusters with Azure Monitor for containers

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per il servizio Azure Kubernetes (AKS) e il [motore AKS in Azure](https://github.com/Azure/aks-engine), ovvero un cluster Kubernetes autogestito ospitato in Azure.Azure Monitor for containers provides rich monitoring experience for the Azure Kubernetes Service (AKS) and AKS Engine on Azure , which is a self-managed Kubernetes clustered on Azure. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati all'esterno di Azure e ottenere un'esperienza di monitoraggio simile.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Un'area di lavoro Log Analytics.

    Monitoraggio di Azure per i contenitori supporta un'area di lavoro di Log Analytics nelle aree elencate in Prodotti di Azure [per area.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Per creare un'area di lavoro personalizzata, è possibile crearla tramite [Azure Resource Manager,](../platform/template-workspace-configuration.md)Tramite [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nel portale di [Azure.](../learn/quick-create-workspace.md)

    >[!NOTE]
    >Abilitare il monitoraggio di più cluster con lo stesso nome di cluster per la stessa area di lavoro di Log Analytics non è supportato. I nomi dei cluster devono essere univoci.
    >

* Si è membri del ruolo di **collaboratore** di Log Analytics per abilitare il monitoraggio del contenitore. Per altre informazioni su come controllare l'accesso a un'area di lavoro di Log Analytics, vedere [Gestire l'accesso all'area di lavoro e registrare i datiFor](../platform/manage-access.md) more information about how to control access to a Log Analytics workspace, see Manage access to workspace and log data

* [Il client HELM](https://helm.sh/docs/using_helm/) per l'onboarding del grafico di Monitoraggio di Azure per i contenitori per il cluster Kubernetes specificato.

* Le informazioni di configurazione del proxy e del firewall seguenti sono necessarie per la versione in contenitori dell'agente di Log Analytics per consentire a Linux di comunicare con Monitoraggio di Azure:

    |Risorsa agente|Porte |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |.dc.services.visualstudio.com |Porta 443 |

* L'agente in contenitori richiede `cAdvisor secure port: 10250` `unsecure port :10255` Kubelet o aperto in tutti i nodi del cluster per raccogliere le metriche delle prestazioni. Si consiglia `secure port: 10250` di configurare sul cAdvisor di Kubelet se non è già configurato.

* L'agente containerizzato richiede che nel contenitore vengano specificate le seguenti variabili di ambiente per comunicare `KUBERNETES_SERVICE_HOST` con `KUBERNETES_PORT_443_TCP_PORT`il servizio API Kubernetes all'interno del cluster per raccogliere i dati di inventario e .

>[!IMPORTANT]
>La versione minima dell'agente supportata per il monitoraggio dei cluster Kubernetes ibridi è ciprod10182019 o versione successiva.

## <a name="supported-configurations"></a>Configurazioni supportate

Quanto segue è ufficialmente supportato con Monitoraggio di Azure per i contenitori.

- Ambienti: Kubernetes locale, Motore AKS in Azure e Azure Stack. Per altre informazioni, vedere [Motore AKS in Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
- Le versioni di Kubernetes e i criteri di supporto sono le stesse delle versioni di [AKS supportate.](../../aks/supported-kubernetes-versions.md)
- Runtime contenitore: Docker e Moby
- Versione del sistema operativo Linux per nodi master e lavorati: Ubuntu (18.04 LTS e 16.04 LTS)
- Controllo di accesso supportato: Rbac Kubernetes e non-RBACAccess supported: Kubernetes RBAC and non-RBAC

## <a name="enable-monitoring"></a>Abilitare il monitoraggio

L'abilitazione di Monitoraggio di Azure per i contenitori per il cluster Kubernetes ibrido consiste nell'eseguire i passaggi seguenti nell'ordine indicato.

1. Configurare l'area di lavoro di Log Analytics con la soluzione Container Insights.

2. Abilitare il grafico HELM di Monitoraggio di Azure per i contenitori con l'area di lavoro Log Analytics.Enable the Azure Monitor for containers HELM chart with Log Analytics workspace.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Come aggiungere la soluzione Contenitori di Monitoraggio di AzureHow to add the Azure Monitor Containers solution

È possibile distribuire la soluzione con il modello di Azure `New-AzResourceGroupDeployment` Resource Manager fornito usando il cmdlet Di Azure PowerShell o con l'interfaccia della riga di comando di Azure.You can deploy the solution with the provided Azure Resource Manager template by using the Azure PowerShell cmdlet or with Azure CLI.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- **workspaceResourceId** : l'ID risorsa completo dell'area di lavoro di Log Analytics.
- **workspaceRegion:** l'area in cui viene creata l'area di lavoro, detta anche posizione nelle proprietà dell'area di lavoro durante la visualizzazione dal portale di Azure.WorkspaceRegion - the region the workspace is created in, which is also referred to **as Location** in the workspace properties when viewing from the Azure portal.

Per identificare innanzitutto l'ID risorsa completo `workspaceResourceId` dell'area di lavoro di Log Analytics necessaria per il valore del parametro nel file **containerSolutionParams.json,** eseguire i passaggi seguenti e quindi eseguire il cmdlet PowerShell o il comando dell'interfaccia della riga di comando di Azure per aggiungere la soluzione.

1. Elencare tutte le sottoscrizioni a cui si ha accesso utilizzando il comando seguente:

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

2. Passare alla sottoscrizione che ospita l'area di lavoro di Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. L'esempio seguente visualizza l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output individuare il nome dell'area di lavoro e quindi copiare l'ID risorsa completo dell'area di lavoro di Log Analytics sotto **l'ID**del campo .

4. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Salvare il file come containerSolution.json in una cartella locale.

6. Incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Modificare i valori per **workspaceResourceId** usando il valore copiato nel passaggio 3 e per **workspaceRegion** copiare il valore **Region** dopo aver eseguito il comando dell'interfaccia della riga di comando di Azure [az monitor log-analytics workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Salvare il file come containerSolutionParams.json in una cartella locale.

9. A questo punto è possibile distribuire il modello.

   * Per eseguire la distribuzione con Azure PowerShell, usare i comandi seguenti nella cartella che contiene il modello:To deploy with Azure PowerShell, use the following commands in the folder that contains the template:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:To deploy with Azure CLI, run the following commands:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

## <a name="install-the-chart"></a>Installare il grafico

Per abilitare il grafico HELM, eseguire le operazioni seguenti:

1. Aggiungere il repository dei grafici di Azure all'elenco locale eseguendo il comando seguente:Add the Azure charts repository to your local list by running the following command:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installare il grafico eseguendo il comando seguente:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se l'area di lavoro log Analytics si trova in Azure Cina, eseguire il comando seguente:If the Log Analytics workspace is in Azure China, run the following command:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics si trova in Azure US Per enti pubblici, eseguire il comando seguente:If the Log Analytics workspace is in Azure US Government, run the following command:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati dell'agenteConfigure agent data collection

Con la versione 1.0.0 del grafico, le impostazioni di raccolta dati dell'agente sono controllate da ConfigMap. Fare riferimento alla documentazione sulle impostazioni di raccolta dati dell'agente [qui](container-insights-agent-config.md).

Dopo aver distribuito correttamente il grafico, è possibile esaminare i dati per il cluster Kubernetes ibrido in Monitoraggio di Azure per i contenitori dal portale di Azure.After you have successfully deployed the chart, you can review the data for your hybrid Kubernetes cluster in Azure Monitor for containers from the Azure portal.  

>[!NOTE]
>La latenza di inserimento è di circa cinque-dieci minuti dall'agente al commit nell'area di lavoro di Azure Log Analytics.Ingestion latency is you for five to ten minutes from agent to commit in the Azure Log Analytics workspace. Lo stato del cluster mostra il valore Nessun dato o **Sconosciuto** finché tutti i dati di monitoraggio necessari non sono disponibili in Monitoraggio di Azure.Status of the cluster show the value **No data** or Unknown until all the required monitoring data is available in Azure Monitor.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante il tentativo di abilitare il monitoraggio per il cluster Kubernetes ibrido, copiare lo script di PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e salvarlo in una cartella nel computer. Questo script viene fornito per rilevare e risolvere i problemi riscontrati. I problemi che è stato progettato per rilevare e tentare la correzione di sono i seguenti:

* L'area di lavoro di Log Analytics specificata è valida
* L'area di lavoro Log Analytics è configurata con la soluzione Monitoraggio per contenitori di Azure.The Log Analytics workspace is configured with the Azure Monitor for Containers solution. In caso contrario, configurare l'area di lavoro.
* I pod del set di repliche OmsAgent sono in esecuzione
* I baccelli daemonset OmsAgent sono in esecuzione
* Servizio Integrità OmsAgent in esecuzione
* L'ID dell'area di lavoro di Log Analytics e la chiave configurati nella corrispondenza dell'agente in contenitori con l'area di lavoro con cui è configurata la panoramica.
* Convalidare tutti i `kubernetes.io/role=agent` nodi di lavoro Linux hanno etichetta per pianificare rs pod. Se non esiste, aggiungerlo.
* `cAdvisor secure port:10250` Convalidare `unsecure port: 10255` o aperto in tutti i nodi del cluster.

Per eseguire con Azure PowerShell, usare i comandi seguenti nella cartella che contiene lo script:To execute with Azure PowerShell, use the following commands in the folder that contains the script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per raccogliere l'integrità e l'utilizzo delle risorse del cluster Kubernetes ibrido e dei carichi di lavoro in esecuzione su di essi, scopri [come usare](container-insights-analyze.md) Monitoraggio di Azure per i contenitori.
