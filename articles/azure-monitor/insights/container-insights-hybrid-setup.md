---
title: Configurare cluster Kubernetes ibridi con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come configurare monitoraggio di Azure per i contenitori per monitorare i cluster Kubernetes ospitati in Azure Stack o in un altro ambiente.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.openlocfilehash: 0d6615d832059a8b58c0d5d52533b8c8c962640d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841575"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configurare cluster Kubernetes ibridi con monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per i cluster di Azure Kubernetes Service (AKS) e del motore AKS ospitati in Azure. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati all'esterno di Azure e ottenere un'esperienza di monitoraggio simile.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Un'area di lavoro Log Analytics.

    Il monitoraggio di Azure per i contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in prodotti Azure in [base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Per creare un'area di lavoro personalizzata, è possibile crearla tramite [Azure Resource Manager](../platform/template-workspace-configuration.md), tramite [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nel [portale di Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >L'abilitazione del monitoraggio di più cluster con lo stesso nome del cluster nella stessa area di lavoro Log Analytics non è supportata. I nomi dei cluster devono essere univoci.
    >

* L'utente è membro del **ruolo di collaboratore log Analytics** per abilitare il monitoraggio dei contenitori. Per ulteriori informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [gestire l'accesso all'area di lavoro e ai dati di log](../platform/manage-access.md)

* [Client Helm](https://helm.sh/docs/using_helm/) per caricare il grafico di monitoraggio di Azure per i contenitori per il cluster Kubernetes specificato.

* Le informazioni di configurazione del proxy e del firewall seguenti sono necessarie per la comunicazione tra la versione in contenitori dell'agente di Log Analytics per Linux e il monitoraggio di Azure:

    |Risorsa agente|Porte |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |*. dc.services.visualstudio.com |Porta 443 | 

* Per raccogliere le metriche delle prestazioni, l'agente in contenitori richiede l'apertura di `cAdvisor port: 10255` in tutti i nodi del cluster.

* Per poter comunicare con il servizio API Kubernetes all'interno del cluster per raccogliere dati di inventario, `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT`, l'agente in contenitori richiede che nel contenitore siano specificate le variabili di ambiente seguenti. 

>[!IMPORTANT]
>La versione minima dell'agente supportata per il monitoraggio dei cluster Kubernetes ibridi è ciprod10182019 o successiva. 

## <a name="supported-configurations"></a>Configurazioni supportate

Il codice seguente è ufficialmente supportato con monitoraggio di Azure per i contenitori.

- Ambienti: Kubernetes locale, motore AKS in Azure e Azure Stack. Per altre informazioni, vedere [motore AKS su Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Le versioni di Kubernetes e i criteri di supporto corrispondono alle versioni di [AKS supportate](../../aks/supported-kubernetes-versions.md). 
- Runtime del contenitore: Docker e Moby
- Versione del sistema operativo Linux per i nodi master e lavoro: Ubuntu (18,04 LTS e 16,04 LTS)
- Controllo di accesso supportato: Kubernetes RBAC e non RBAC

## <a name="enable-monitoring"></a>Abilitare il monitoraggio

L'abilitazione di monitoraggio di Azure per i contenitori per il cluster Kubernetes ibrido prevede l'esecuzione dei passaggi seguenti nell'ordine.

1. Configurare l'area di lavoro Log Analytics con la soluzione container Insights.

2. Abilitare il grafico di monitoraggio di Azure per i contenitori HELM con Log Analytics area di lavoro.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Come aggiungere la soluzione contenitori di monitoraggio di Azure

È possibile distribuire la soluzione con il modello di Azure Resource Manager fornito usando il cmdlet Azure PowerShell `New-AzResourceGroupDeployment` o con l'interfaccia della riga di comando di Azure.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- **workspaceResourceId** : l'ID di risorsa completo dell'area di lavoro log Analytics.
- **workspaceRegion** : area in cui viene creata l'area di lavoro, definita anche **percorso** nelle proprietà dell'area di lavoro durante la visualizzazione dalla portale di Azure.

Per identificare prima di tutto l'ID risorsa completo dell'area di lavoro Log Analytics necessaria per il valore del parametro `workspaceResourceId` nel file **containerSolutionParams. JSON** , seguire questa procedura e quindi eseguire il cmdlet di PowerShell o l'interfaccia della riga di comando di Azure per aggiungere la soluzione.

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

5. Salvare il file come containerSolution. JSON in una cartella locale.

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

7. Modificare i valori per **workspaceResourceId** usando il valore copiato nel passaggio 3 e per **workspaceRegion** copiare il valore **Region** dopo l'esecuzione del comando dell'interfaccia della riga di comando di Azure [AZ monitor log-Analytics Workspace Show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Salvare il file come containerSolutionParams. JSON in una cartella locale.

9. A questo punto è possibile distribuire il modello. 

   * Per eseguire la distribuzione con Azure PowerShell, usare i comandi seguenti nella cartella che contiene il modello:

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

   * Per la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:
    
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

Per abilitare il grafico HELM, procedere come segue:

1. Aggiungere il repository dei grafici di Azure all'elenco locale eseguendo il comando seguente:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installare il grafico eseguendo il comando seguente:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics è in Azure Cina, eseguire il comando seguente:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Se l'area di lavoro Log Analytics si trova nel governo degli Stati Uniti di Azure, eseguire il comando seguente:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati agente

Con il grafico versione 1.0.0, le impostazioni di raccolta dei dati dell'agente vengono controllate dal ConfigMap. Vedere la documentazione sulle impostazioni di raccolta dati di Agent [qui](container-insights-agent-config.md). 

Dopo aver distribuito correttamente il grafico, è possibile esaminare i dati per il cluster Kubernetes ibrido in monitoraggio di Azure per i contenitori dalla portale di Azure.  

>[!NOTE]
>La latenza di inserimento è circa tra cinque e dieci minuti dall'agente per eseguire il commit nell'area di lavoro di Azure Log Analytics. Lo stato del cluster Mostra il valore **Nessun dato** o **sconosciuto** fino a quando non sono disponibili tutti i dati di monitoraggio necessari in monitoraggio di Azure. 

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verifica un errore durante il tentativo di abilitare il monitoraggio per il cluster Kubernetes ibrido, copiare lo script di PowerShell [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e salvarlo in una cartella nel computer. Questo script viene fornito per facilitare il rilevamento e la risoluzione dei problemi rilevati. Di seguito sono riportati i problemi che è stato progettato per rilevare e tentare la correzione:

* L'area di lavoro Log Analytics specificata è valida 
* L'area di lavoro Log Analytics è configurata con la soluzione monitoraggio di Azure per contenitori. In caso contrario, configurare l'area di lavoro.
* Il Pod REPLICASET OmsAgent è in esecuzione
* Il Pod daemonset OmsAgent è in esecuzione
* Il servizio integrità OmsAgent è in esecuzione 
* Il Log Analytics ID e la chiave dell'area di lavoro configurati nell'agente in contenitori corrispondono all'area di lavoro con cui è configurata l'analisi.
* Verificare che tutti i nodi di lavoro Linux abbiano `kubernetes.io/role=agent` etichetta per pianificare il Pod RS. Se non esiste, aggiungerlo.
* Validate `cAdvisor port: 10255` viene aperto in tutti i nodi del cluster.

Per eseguire con Azure PowerShell, usare i comandi seguenti nella cartella che contiene lo script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile>
```

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster Kubernetes ibrido e dei carichi di lavoro in esecuzione su di essi, Scopri [come usare](container-insights-analyze.md) monitoraggio di Azure per i contenitori.
