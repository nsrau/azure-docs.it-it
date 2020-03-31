---
title: Usare l'identità AAD con il servizio WebUse AAD identity with your web service
titleSuffix: Azure Machine Learning
description: Usare l'identità AAD con il servizio Web nel servizio Azure Kubernetes per accedere alle risorse cloud durante il punteggio.
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
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Usare l'identità di Azure AD con il servizio Web di Machine Learning nel servizio Azure KubernetesUse Azure AD identity with your machine learning web service in Azure Kubernetes Service

In this how-to, you learn how to assign an Azure Active Directory (AAD) identity to your deployed machine learning model in Azure Kubernetes Service. Il progetto [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) consente alle applicazioni di accedere alle risorse cloud in modo sicuro con AAD usando [un'identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e primitive Kubernetes. Ciò consente al servizio Web di accedere in modo sicuro alle risorse `score.py` di Azure senza dover incorporare le credenziali o gestire i token direttamente all'interno dello script. Questo articolo illustra i passaggi per creare e installare un'identità di Azure nel cluster del servizio Azure Kubernetes e assegnare l'identità al servizio Web distribuito.

## <a name="prerequisites"></a>Prerequisiti

- [L'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure per il servizio Machine Learning, [l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)di Azure Machine Learning per Python o l'estensione Visual Studio Code di [Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Accesso al cluster AKS `kubectl` tramite il comando. Per altre informazioni, vedere [Connettersi al clusterFor](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster) more information, see Connect to the cluster

- Un servizio Web di Azure Machine Learning distribuito nel cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Creare e installare un'identità di Azure nel cluster AKSCreate and install an Azure Identity in your AKS cluster

1. Per determinare se il cluster AKS è abilitato al controllo degli accessi in base al ruolo, utilizzare il comando seguente:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Questo comando restituisce `true` un valore di se il controllo degli accessi in base al ruolo è abilitato. Questo valore determina il comando da utilizzare nel passaggio successivo.

1. Per installare [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) nel cluster AKS, utilizzare uno dei comandi seguenti:

    * Se il cluster AKS ha **il controllo degli accessi in base** al ruolo abilitato, utilizzare il comando seguente:If your AKS cluster has RBAC enabled use the following command:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Se nel cluster AKS **non è abilitato il controllo degli accessi in base**al ruolo, utilizzare il comando seguente:
    
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

1. [Creare un'identità](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) di Azure seguendo i passaggi illustrati nella pagina del progetto AAD Pod Identity.Create an Azure Identity following the steps shown in AAD Pod Identity project page.

1. [Installare l'identità](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) di Azure seguendo i passaggi illustrati nella pagina del progetto AAD Pod Identity.Install the Azure Identity following the steps shown in AAD Pod Identity project page.

1. [Installare l'associazione di identità](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) di Azure seguendo i passaggi illustrati nella pagina del progetto AAD Pod Identity.Install the Azure Identity Binding following the steps shown in AAD Pod Identity project page.

1. Se l'identità di Azure creata nel passaggio precedente non si trova nello stesso gruppo di risorse del cluster AKS, seguire [Set Permissions for MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) seguendo i passaggi illustrati nella pagina del progetto AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Assegnare l'identità di Azure al servizio Web di Machine LearningAssign Azure Identity to machine learning web service

I passaggi seguenti usano l'identità di Azure creata nella sezione precedente e assegnarla al servizio Web AKS tramite **un'etichetta del selettore**.

Identificare innanzitutto il nome e lo spazio dei nomi della distribuzione nel cluster AKS a cui si vuole assegnare l'identità di Azure.First, identify the name and namespace of your deployment in your AKS cluster that you want to assign the Azure Identity. È possibile ottenere queste informazioni eseguendo il comando seguente. Gli spazi dei nomi devono essere il nome dell'area di lavoro di Azure Machine Learning e il nome della distribuzione deve essere il nome dell'endpoint, come illustrato nel portale.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Aggiungere l'etichetta del selettore Di identità di Azure alla distribuzione modificando la specifica di distribuzione. Il valore del selettore deve essere quello definito nel passaggio 5 di [Installare l'associazione di identità](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)di Azure .

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Modificare la distribuzione per aggiungere l'etichetta del selettore Di identità di Azure.Edit the deployment to add the Azure Identity selector label. Passare alla sezione `/spec/template/metadata/labels`seguente in . Dovrebbero essere visualizzati `isazuremlapp: “true”`valori come . Aggiungi l'etichetta aad-pod-identity come mostrato di seguito.

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

Per visualizzare tutti gli stati dei pod, eseguite il comando seguente.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Una volta che i pod sono operativi, i servizi Web per questa distribuzione saranno ora in grado di accedere alle risorse di Azure tramite l'identità di Azure senza dover incorporare le credenziali nel codice. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Assegnare i ruoli appropriati all'identità di AzureAssign the appropriate roles to your Azure Identity

[Assegnare l'identità gestita di Azure con i ruoli appropriati](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) per accedere ad altre risorse di Azure.Assign your Azure Managed Identity with appropriate roles to access other Azure resources. Assicurarsi che i ruoli che si stanno assegnando dispongano delle **azioni dati**corrette. Ad esempio, il [ruolo Lettore dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) disterrà le autorizzazioni di lettura per il BLOB di archiviazione, mentre il ruolo [lettore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) generico potrebbe non essere.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Usare l'identità di Azure con il servizio Web di Machine LearningUse Azure Identity with your machine learning web service

Distribuire un modello nel cluster AKS. Lo `score.py` script può contenere operazioni che puntano alle risorse di Azure a cui l'identità di Azure ha accesso. Assicurarsi di aver installato le dipendenze della libreria client necessarie per la risorsa a cui si sta tentando di accedere. Di seguito sono riportati alcuni esempi di come è possibile usare l'identità di Azure per accedere a risorse di Azure diverse dal servizio.

### <a name="access-key-vault-from-your-web-service"></a>Accedere all'insieme di credenziali delle chiavi dal servizio Web

Se è stato concesso all'identità di Azure l'accesso in lettura a un segreto all'interno di un insieme di credenziali **delle**chiavi, `score.py` è possibile accedervi usando il codice seguente.

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

### <a name="access-blob-from-your-web-service"></a>Accedere al BLOB dal servizio WebAccess Blob from your web service

Se l'identità di Azure ha concesso l'accesso in lettura ai dati all'interno di un BLOB di **archiviazione,** `score.py` è possibile accedervi usando il codice seguente.

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

* Per altre informazioni su come usare la libreria client Python Azure Identity, vedere il repository in GitHub.For more information on how to use the Python Azure Identity client library, see the [repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) on GitHub.
* Per una guida dettagliata sulla distribuzione di modelli nei cluster del servizio Azure Kubernetes, vedere le [procedure.](how-to-deploy-azure-kubernetes-service.md)