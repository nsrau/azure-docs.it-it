---
title: Integrare Azure Key Vault con Kubernetes
description: In questa esercitazione si accederà ad Azure Key Vault da cui si recupereranno i segreti usando il driver CSI (Container Storage Interface) dell'archivio di segreti per poi montarli nei pod Kubernetes.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636935"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Esercitazione: Configurare ed eseguire il provider Azure Key Vault per il driver CSI dell'archivio di segreti in Kubernetes

In questa esercitazione si accederà ad Azure Key Vault da cui si recupereranno i segreti usando il driver CSI (Container Storage Interface) dell'archivio di segreti per poi montarli nei pod Kubernetes.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'entità servizio
> * Distribuire un cluster del servizio Azure Kubernetes
> * Installare Helm e il driver CSI dell'archivio di segreti
> * Creare un'istanza di Azure Key Vault e impostare i segreti
> * Creare un oggetto SecretProviderClass personale
> * Distribuire il pod con i segreti montati da Key Vault

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Prima di iniziare questa esercitazione, installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Creare un'entità servizio o usare le identità gestite

Se si prevede di usare le identità gestite, è possibile passare alla sezione successiva.

Creare un'entità servizio per controllare le risorse a cui è possibile accedere da Azure Key Vault. L'accesso dell'entità servizio è limitato dai ruoli a essa assegnati. Questa funzionalità consente di controllare il modo in cui l'entità servizio può gestire i segreti. Nell'esempio seguente, il nome dell'entità servizio è **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Questa operazione restituisce una serie di coppie chiave-valore:

![Immagine](../media/kubernetes-key-vault-1.png)

Copiare i valori **appId** e **password**. Queste credenziali saranno necessarie più avanti.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure

Non è necessario usare Azure Cloud Shell, il prompt dei comandi (terminale) con l'interfaccia della riga di comando di Azure installata eseguirà questa operazione. 

Seguire questa [guida](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) e completare le sezioni seguenti: **Creare un gruppo di risorse**, **creare un cluster del servizio Azure Kubernetes** e **connettersi al cluster**.

**Nota:** se si prevede di usare l'identità del pod anziché un'entità servizio, assicurarsi di abilitarla quando si crea il cluster Kubernetes, come illustrato di seguito:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Impostare la variabile di ambiente PATH](https://www.java.com/en/download/help/path.xml) sul file "kubectl.exe" scaricato.
1. Controllare la versione di Kubernetes usando il comando seguente. Questo comando restituirà la versione del client e del server. La versione del client è il file "kubectl.exe" installato mentre la versione del server è il servizio Azure Kubernetes in cui è in esecuzione il cluster.
    ```azurecli
    kubectl version
    ```
1. Verificare che la versione di Kubernetes sia **v1.16.0** o una versione successiva. Questo comando aggiornerà sia il cluster Kubernetes che il pool di nodi. L'esecuzione del comando potrebbe richiedere alcuni minuti. In questo esempio il gruppo di risorse è **contosoResourceGroup** e il cluster Kubernetes è **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Visualizzare i metadati del cluster del servizio Azure Kubernetes creati usando il comando seguente. Copiare i valori **principalId**, **clientId**, **subscriptionId** e **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Di seguito è riportato l'output con entrambi i parametri evidenziati.
    
    ![Immagine](../media/kubernetes-key-vault-5.png) ![Immagine](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Installare Helm e il driver CSI dell'archivio di segreti

È necessario installare [Helm](https://helm.sh/docs/intro/install/) per installare il driver CSI dell'archivio di segreti.

L'interfaccia del driver [CSI dell'archivio di segreti](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) consente di recuperare il contenuto dei segreti archiviati in un'istanza di Azure Key Vault e di usare l'interfaccia del driver per montare il contenuto dei segreti nei pod Kubernetes.

1. Controllare la versione di Helm e assicurarsi che sia v3 o superiore:
    ```azurecli
    helm version
    ```
1. Installare il driver CSI dell'archivio di segreti e il provider Azure Key Vault per il driver:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Creare un'istanza di Azure Key Vault e impostare i segreti

Seguire questa [guida](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) per creare il proprio Key Vault e impostare i segreti.

**Nota:** non è necessario usare Azure Cloud Shell o creare un nuovo gruppo di risorse. È possibile usare il gruppo di risorse creato in precedenza per il cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Creare un oggetto SecretProviderClass personale

Usare questo [modello](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) fornito per creare il proprio oggetto SecretProviderClass personalizzato per fornire i parametri specifici del provider per il driver CSI dell'archivio di segreti. Questo oggetto fornirà all'identità l'accesso all'istanza di Key Vault.

Viene usato il file YAML SecretProviderClass di esempio fornito. Verranno compilati i parametri mancanti. I parametri seguenti sono obbligatori:

1.  **userAssignedIdentityID:** ID client dell'entità servizio
1.  **keyvaultName:** nome dell'istanza di Key Vault
1.  **objects:** questo oggetto conterrà tutto il contenuto del segreto che si vuole montare
    1.  **objectName:** nome del contenuto del segreto
    1.  **objectType:** tipo di oggetto (segreto, chiave, certificato)
1.  **resourceGroup:** nome del gruppo di risorse
1.  **subscriptionId:** ID sottoscrizione dell'istanza di Key Vault
1.  **tenantID:** ID tenant (ovvero ID directory) dell'istanza di Key Vault

Di seguito è riportato il modello aggiornato, scaricarlo come file con estensione YAML e compilare i campi obbligatori corrispondenti. In questo esempio, l'istanza di Key Vault è **contosoKeyVault5** con due segreti, **secret1** e **secret2**.

**Nota:** se si usano le identità gestite, il campo **usePodIdentity** deve essere **true** e immettere nel campo **userAssignedIdentityID** solo le virgolette **""** . 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Di seguito è riportato l'output della console per "az keyvault show --name contosoKeyVault5" con i metadati pertinenti evidenziati:

![Immagine](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Assegnare l'entità servizio o usare le identità gestite

### <a name="using-service-principal"></a>Uso dell'entità servizio

Se si usa un'entità servizio, è necessario concedere all'entità servizio l'autorizzazione per accedere all'istanza di Key Vault e recuperare i segreti. Assegnare il ruolo di **"Lettore"** e concedere all'entità servizio l'autorizzazione per **"recuperare"** i segreti dall'istanza di Key Vault completando i passaggi seguenti:

1. Assegnare l'entità servizio all'istanza di Key Vault esistente. Il parametro **$AZURE_CLIENT_ID** è il valore **appId** copiato dopo la creazione dell'entità servizio.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Di seguito è riportato l'output del comando: 

    ![Immagine](../media/kubernetes-key-vault-3.png)

1. Concedere all'entità servizio l'autorizzazione per recuperare i segreti:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. A questo punto l'entità servizio è stata configurata in modo da avere l'autorizzazione per leggere i segreti dall'istanza di Key Vault. Il parametro **$AZURE_CLIENT_SECRET** è il valore **password** dell'entità servizio. Aggiungere le credenziali dell'entità servizio come segreto Kubernetes accessibile dal driver CSI dell'archivio di segreti:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Nota:** se in seguito si riceve un errore durante la distribuzione del pod Kubernetes relativo a un ID segreto client non valido, è possibile che si disponga di un ID segreto client meno recente che è scaduto o che deve essere ripristinato. Per risolvere questo problema, eliminare i segreti "secrets-store-creds" e crearne di nuovi con l'ID segreto client corrente. Per eliminare i segreti "secrets-store-creds", eseguire il comando seguente:
```azurecli
kubectl delete secrets secrets-store-creds
```

Se si dimentica l'ID segreto client dell'entità servizio, è possibile ripristinarlo usando il comando seguente:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Uso di identità gestite

Se si usano le identità gestite, assegnare ruoli specifici al cluster del servizio Azure Kubernetes creato. 
1. Per creare/elencare/leggere un'identità gestita assegnata dall'utente, è necessario assegnare al cluster del servizio Azure Kubernetes il ruolo [Collaboratore di identità gestite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor). Assicurarsi che il valore **$clientId** sia quello del cluster Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installare l'identità di Azure Active Directory (Azure AD) nel servizio Azure Kubernetes.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Creare un'identità di Azure AD. Copiare i valori **clientId** e **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Assegnare il ruolo di lettore all'identità di Azure AD appena creata per l'istanza di Key Vault. Assegnare quindi all'identità l'autorizzazione per recuperare i segreti dall'istanza di Key Vault. Si useranno i valori **clientId** e **principalId** dell'identità di Azure appena creata.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Distribuire il pod con i segreti montati da Key Vault

Il comando seguente consente di configurare l'oggetto SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Uso dell'entità servizio

Se si usa un'entità servizio, il comando seguente distribuirà i pod Kubernetes con l'oggetto SecretProviderClass e i segreti secrets-store-creds configurati. Di seguito è riportato il modello per la distribuzione [linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) e [windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml).
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Uso di identità gestite

Se si usano le identità gestite, si creerà un oggetto **AzureIdentity** nel cluster che farà riferimento all'identità creata in precedenza. Quindi, creare un oggetto **AzureIdentityBinding** che faccia riferimento all'oggetto **AzureIdentity** creato. Usare il modello seguente, compilare i parametri corrispondenti e salvarlo come **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Eseguire il comando seguente per eseguire l'associazione:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Il passaggio successivo è la distribuzione effettiva del pod. Di seguito è riportata la distribuzione del file YAML che userà l'associazione dell'identità del pod dell'ultimo passaggio. Salvare il file come **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Eseguire il comando seguente per distribuire il pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Controllare lo stato e il contenuto del segreto 
Per visualizzare i pod distribuiti:
```azurecli
kubectl get pods
```

Per verificare lo stato del pod, usare il comando seguente:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Immagine](../media/kubernetes-key-vault-4.png)

Il pod distribuito deve trovarsi nello stato "In esecuzione". Nella sezione "Eventi" in basso tutti i tipi di eventi a sinistra sono classificati come "Normali".
Dopo aver verificato che il pod è in esecuzione, è possibile verificare che il pod includa i segreti dell'istanza di Key Vault.

Per visualizzare tutti i segreti contenuti nel pod:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Per ottenere il contenuto di un segreto specifico:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verificare il contenuto del segreto visualizzato.

## <a name="next-steps"></a>Passaggi successivi

Assicurarsi che l'istanza di Key Vault sia recuperabile:
> [!div class="nextstepaction"]
> [Abilitare l'eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
