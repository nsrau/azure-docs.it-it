---
title: Usare l'identità di AAD con il servizio Web
titleSuffix: Azure Machine Learning
description: Usare l'identità AAD con il servizio Web nel servizio Azure Kubernetes per accedere alle risorse cloud durante l'assegnazione dei punteggi.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122844"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Usare Azure AD identità con il servizio Web di machine learning nel servizio Azure Kubernetes

In questa procedura si apprenderà come assegnare un'identità di Azure Active Directory (AAD) al modello di apprendimento automatico distribuito nel servizio Azure Kubernetes. Il progetto di [identità di AAD Pod](https://github.com/Azure/aad-pod-identity) consente alle applicazioni di accedere alle risorse cloud in modo sicuro con AAD usando un' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e le primitive Kubernetes. Ciò consente al servizio Web di accedere in modo sicuro alle risorse di Azure senza dover incorporare le credenziali o gestire i `score.py` token direttamente all'interno dello script. Questo articolo illustra i passaggi per creare e installare un'identità di Azure nel cluster del servizio Kubernetes di Azure e assegnare l'identità al servizio Web distribuito.

## <a name="prerequisites"></a>Prerequisiti

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), l' [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Accesso al cluster AKS tramite il `kubectl` comando. Per altre informazioni, vedere [connettersi al cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Un servizio Web Azure Machine Learning distribuito nel cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Creare e installare un'identità di Azure nel cluster AKS

1. Per determinare se il cluster AKS è abilitato per RBAC, usare il comando seguente:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Questo comando restituisce un valore `true` se RBAC è abilitato. Questo valore determina il comando da usare nel passaggio successivo.

1. Per installare l' [identità pod di AAD](https://github.com/Azure/aad-pod-identity#getting-started) nel cluster AKS, usare uno dei comandi seguenti:

    * Se nel cluster AKS è **abilitato RBAC** , usare il comando seguente:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Se per il cluster AKS **non è abilitato RBAC**, usare il comando seguente:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        L'output del comando è simile al testo seguente:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Creare un'identità di Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) seguendo i passaggi illustrati nella pagina del progetto di identità AAD pod.

1. [Installare l'identità di Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) seguendo i passaggi illustrati nella pagina del progetto di identità AAD pod.

1. [Installare l'associazione di identità di Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) seguendo i passaggi illustrati nella pagina del progetto di identità AAD pod.

1. Se l'identità di Azure creata nel passaggio precedente non si trova nello stesso gruppo di risorse del cluster del servizio contenitore di Azure, seguire le istruzioni [per impostare le autorizzazioni per MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) attenendosi alla procedura illustrata nella pagina del progetto di identità AAD pod.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Assegnare l'identità di Azure al servizio Web di Machine Learning

La procedura seguente usa l'identità di Azure creata nella sezione precedente e la assegna al servizio Web AKS tramite un'etichetta di **selezione**.

Per prima cosa, identificare il nome e lo spazio dei nomi della distribuzione nel cluster AKS a cui si vuole assegnare l'identità di Azure. È possibile ottenere queste informazioni eseguendo il comando seguente. Gli spazi dei nomi devono essere il nome dell'area di lavoro Azure Machine Learning e il nome della distribuzione deve essere il nome dell'endpoint, come illustrato nel portale.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Per aggiungere l'etichetta del selettore di identità di Azure alla distribuzione, modificare la specifica della distribuzione. Il valore del selettore deve essere quello definito nel passaggio 5 di [Install the Azure Identity binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Modificare la distribuzione per aggiungere l'etichetta del selettore di identità di Azure. Passare alla sezione seguente in `/spec/template/metadata/labels`. Verranno visualizzati valori come `isazuremlapp: “true”`. Aggiungere l'etichetta AAD-Pod-Identity come mostrato di seguito.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Per verificare che l'etichetta sia stata aggiunta correttamente, eseguire il comando seguente.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Per visualizzare tutti gli Stati Pod, eseguire il comando seguente.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Una volta che i pod sono operativi, i servizi Web per questa distribuzione saranno ora in grado di accedere alle risorse di Azure tramite l'identità di Azure senza che sia necessario incorporare le credenziali nel codice. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Assegnare i ruoli appropriati all'identità di Azure

[Assegnare l'identità gestita di Azure con i ruoli appropriati](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) per accedere ad altre risorse di Azure. Assicurarsi che i ruoli da assegnare dispongano delle **azioni dati**corrette. Ad esempio, il [ruolo lettore dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) disporrà di autorizzazioni di lettura per il BLOB di archiviazione, mentre il [ruolo lettore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) generico potrebbe non.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Usare Azure Identity con il servizio Web di Machine Learning

Distribuire un modello nel cluster AKS. Lo `score.py` script può contenere operazioni che puntano alle risorse di Azure a cui ha accesso l'identità di Azure. Assicurarsi di aver installato le dipendenze della libreria client necessarie per la risorsa a cui si sta tentando di accedere. Di seguito sono riportati alcuni esempi di come è possibile usare l'identità di Azure per accedere alle diverse risorse di Azure dal servizio.

### <a name="access-key-vault-from-your-web-service"></a>Accedere a Key Vault dal servizio Web

Se è stato assegnato l'accesso in lettura all'identità di Azure a un **Key Vault**segreto all'interno `score.py` di una Key Vault, è possibile accedervi usando il codice seguente.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Accedere al BLOB dal servizio Web

Se è stato concesso l'accesso in lettura all'identità di Azure ai dati all'interno di `score.py` un BLOB di **archiviazione**, è possibile accedervi usando il codice seguente.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come usare la libreria client di Azure Identity per Python, vedere il [repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) in GitHub.
* Per una guida dettagliata sulla distribuzione di modelli nei cluster di servizi Kubernetes di Azure, vedere la [procedura](how-to-deploy-azure-kubernetes-service.md).