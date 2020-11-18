---
title: Usare Azure AD identità con il servizio Web
titleSuffix: Azure Machine Learning
description: Usare Azure AD identità con il servizio Web nel servizio Azure Kubernetes per accedere alle risorse cloud durante l'assegnazione dei punteggi.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7b76c81a78bfd3eb57a54f1d23ba1b154b09b3e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660152"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Usare l'identità Azure AD con il servizio Web di Machine Learning nel servizio Azure Kubernetes

In questa procedura si apprenderà come assegnare un'identità Azure Active Directory (Azure AD) al modello di machine learning distribuito nel servizio Azure Kubernetes. Il progetto di [identità Azure ad pod](https://github.com/Azure/aad-pod-identity) consente alle applicazioni di accedere in modo sicuro alle risorse cloud con Azure ad usando un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) e le primitive Kubernetes. Ciò consente al servizio Web di accedere in modo sicuro alle risorse di Azure senza dover incorporare le credenziali o gestire i token direttamente all'interno `score.py` dello script. Questo articolo illustra i passaggi per creare e installare un'identità di Azure nel cluster del servizio Kubernetes di Azure e assegnare l'identità al servizio Web distribuito.

## <a name="prerequisites"></a>Prerequisiti

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), l' [SDK Azure Machine Learning per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Accesso al cluster AKS tramite il `kubectl` comando. Per altre informazioni, vedere [connettersi al cluster](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- Un servizio Web Azure Machine Learning distribuito nel cluster AKS.

## <a name="create-and-install-an-azure-identity"></a>Creare e installare un'identità di Azure

1. Per determinare se il cluster AKS è abilitato per RBAC, usare il comando seguente:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Questo comando restituisce un valore `true` se RBAC è abilitato. Questo valore determina il comando da usare nel passaggio successivo.

1. Installare [Azure ad identità Pod](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) nel cluster AKS.

1. [Creare un'identità in Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) seguendo i passaggi illustrati nella pagina del progetto Azure ad pod Identity.

1. [Distribuire AzureIdentity](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) seguendo i passaggi illustrati nella pagina del progetto Azure ad pod Identity.

1. [Distribuire AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) seguendo i passaggi illustrati nella pagina del progetto Azure ad pod Identity.

1. Se l'identità di Azure creata nel passaggio precedente non si trova nello stesso gruppo di risorse nodo per il cluster del servizio contenitore di Azure, seguire la procedura di [assegnazione di ruolo](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) illustrata nella pagina del progetto Azure ad pod Identity.

## <a name="assign-azure-identity-to-web-service"></a>Assegnare l'identità di Azure al servizio Web

La procedura seguente usa l'identità di Azure creata nella sezione precedente e la assegna al servizio Web AKS tramite un'etichetta di **selezione**.

Per prima cosa, identificare il nome e lo spazio dei nomi della distribuzione nel cluster AKS a cui si vuole assegnare l'identità di Azure. È possibile ottenere queste informazioni eseguendo il comando seguente. Gli spazi dei nomi devono essere il nome dell'area di lavoro Azure Machine Learning e il nome della distribuzione deve essere il nome dell'endpoint, come illustrato nel portale.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Per aggiungere l'etichetta del selettore di identità di Azure alla distribuzione, modificare la specifica della distribuzione. Il valore del selettore deve essere quello definito nel passaggio 5 di [deploy AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Modificare la distribuzione per aggiungere l'etichetta del selettore di identità di Azure. Passare alla sezione seguente in `/spec/template/metadata/labels` . Verranno visualizzati valori come `isazuremlapp: “true”` . Aggiungere l'etichetta AAD-Pod-Identity come mostrato di seguito.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Per verificare che l'etichetta sia stata aggiunta correttamente, eseguire il comando seguente. Dovrebbero essere visualizzati anche gli Stati dei Pod appena creati.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Una volta che i pod sono operativi, i servizi Web per questa distribuzione saranno ora in grado di accedere alle risorse di Azure tramite l'identità di Azure senza che sia necessario incorporare le credenziali nel codice.

## <a name="assign-roles-to-your-azure-identity"></a>Assegnare i ruoli all'identità di Azure

[Assegnare l'identità gestita di Azure con i ruoli appropriati](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) per accedere ad altre risorse di Azure. Assicurarsi che i ruoli da assegnare dispongano delle **azioni dati** corrette. Ad esempio, il [ruolo lettore dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) disporrà di autorizzazioni di lettura per il BLOB di archiviazione, mentre il [ruolo lettore](../role-based-access-control/built-in-roles.md#reader) generico potrebbe non.

## <a name="use-azure-identity-with-your-web-service"></a>Usare l'identità di Azure con il servizio Web

Distribuire un modello nel cluster AKS. Lo `score.py` script può contenere operazioni che puntano alle risorse di Azure a cui ha accesso l'identità di Azure. Assicurarsi di aver installato le dipendenze della libreria client necessarie per la risorsa a cui si sta tentando di accedere. Di seguito sono riportati alcuni esempi di come è possibile usare l'identità di Azure per accedere alle diverse risorse di Azure dal servizio.

### <a name="access-key-vault-from-your-web-service"></a>Accedere a Key Vault dal servizio Web

Se è stato assegnato l'accesso in lettura all'identità di Azure a un segreto all'interno di una **Key Vault**, `score.py` è possibile accedervi usando il codice seguente.

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

> [!IMPORTANT]
> Questo esempio USA DefaultAzureCredential. Per concedere l'accesso alle identità usando criteri di accesso specifici, vedere [assegnare un criterio di accesso key Vault usando l'interfaccia della](../key-vault/general/assign-access-policy-cli.md)riga di comando di Azure.

### <a name="access-blob-from-your-web-service"></a>Accedere al BLOB dal servizio Web

Se è stato concesso l'accesso in lettura all'identità di Azure ai dati all'interno di un **BLOB di archiviazione**, `score.py` è possibile accedervi usando il codice seguente.

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