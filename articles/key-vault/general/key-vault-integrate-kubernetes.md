---
title: Integrare Azure Key Vault con Kubernetes
description: Questa esercitazione illustra come accedere e recuperare segreti da Azure Key Vault usando il driver CSI (Container Storage Interface) dell'archivio segreti per poi montarli nei pod Kubernetes.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b7d587f2be5141f7de82e9294b1fdb9fba4a6a41
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488644"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Esercitazione: Configurare ed eseguire il provider di Azure Key Vault per il driver CSI dell'archivio di segreti in Kubernetes

> [!IMPORTANT]
> Il driver CSI è un progetto open source non supportato dal supporto tecnico di Azure. Segnalare eventuali commenti, suggerimenti e problemi correlati all'integrazione di Key Vault del driver CSI nel collegamento GitHub nella parte inferiore della pagina. Questo strumento è disponibile per consentire agli utenti di eseguire l'installazione autonoma nei cluster e di ottenere commenti e suggerimenti dalla community.

Questa esercitazione illustra come accedere e recuperare segreti da Azure Key Vault usando il driver CSI (Container Storage Interface) dell'archivio segreti per poi montarli nei pod Kubernetes.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'entità servizio o usare le identità gestite.
> * Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure.
> * Installare Helm e il driver CSI dell'archivio segreti.
> * Creare un'istanza di Azure Key Vault e impostare i segreti.
> * Creare un oggetto SecretProviderClass personale.
> * Assegnare l'entità servizio o usare le identità gestite.
> * Distribuire il pod con i segreti montati da Key Vault.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Prima di iniziare questa esercitazione, installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Creare un'entità servizio o usare le identità gestite

Se si prevede di usare le identità gestite, è possibile passare alla sezione successiva.

Creare un'entità servizio per controllare le risorse a cui è possibile accedere da Azure Key Vault. L'accesso dell'entità servizio è limitato dai ruoli a essa assegnati. Questa funzionalità consente di controllare il modo in cui l'entità servizio può gestire i segreti. Nell'esempio seguente il nome dell'entità servizio è *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Questa operazione restituisce una serie di coppie chiave-valore:

![Screenshot che mostra appId e password per contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Copiare le credenziali **appId** e **password** per un uso successivo.

## <a name="flow-for-using-managed-identity"></a>Flusso per l'uso dell'identità gestita

Questo diagramma illustra il flusso di integrazione dell'insieme di credenziali delle chiavi del servizio Azure Kubernetes per l'identità gestita:

![Diagramma che mostra il flusso di integrazione dell'insieme di credenziali delle chiavi del servizio Azure Kubernetes per l'identità gestita](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure

Non è necessario usare Azure Cloud Shell. Sarà sufficiente usare il prompt dei comandi (terminale) con l'interfaccia della riga di comando di Azure installata. 

Completare le sezioni "Creare un gruppo di risorse", "Creare un cluster del servizio Azure Kubernetes" e "Connettersi al cluster" dell'articolo [Distribuire un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Se si prevede di usare un'identità pod invece di un entità servizio, assicurarsi di abilitarla quando si crea il cluster Kubernetes, come illustrato nel comando seguente:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Impostare la variabile di ambiente PATH](https://www.java.com/en/download/help/path.xml) sul file *kubectl.exe* scaricato.
1. Controllare la versione di Kubernetes usando il comando seguente, che restituisce la versione del client e del server. La versione del client è il file *kubectl.exe* installato mentre la versione del server è il servizio Azure Kubernetes in cui è in esecuzione il cluster.
    ```azurecli
    kubectl version
    ```
1. Assicurarsi che la versione di Kubernetes sia 1.16.0 o successiva. Per i cluster Windows assicurarsi che la versione di Kubernetes sia 1.18.0 o successiva. Il comando seguente aggiorna sia il cluster Kubernetes che il pool di nodi. L'esecuzione del comando potrebbe richiedere un paio di minuti. In questo esempio il gruppo di risorse è *contosoResourceGroup* e il cluster Kubernetes è *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Per visualizzare i metadati del cluster del servizio Azure Kubernetes creato, usare il comando seguente. Copiare i valori di **principalId**, **clientId**, **subscriptionId** e **nodeResourceGroup** per un uso successivo. Se il cluster del servizio Azure Kubernetes non è stato creato con le identità gestite abilitate, **principalId** e **clientId** saranno Null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    L'output mostra entrambi i parametri evidenziati:
    
    ![Screenshot dell'interfaccia della riga di comando di Azure con valori di principalId e clientId evidenziati](../media/kubernetes-key-vault-2.png) ![Screenshot dell'interfaccia della riga di comando di Azure con i valori di subscriptionId e nodeResourceGroup evidenziati](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installare Helm e il driver CSI dell'archivio segreti

Per installare il driver CSI dell'archivio segreti, è necessari installare prima [Helm](https://helm.sh/docs/intro/install/).

L'interfaccia del driver [CSI dell'archivio segreti](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) consente di recuperare i segreti archiviati in un'istanza di Azure Key Vault ed è possibile usarla per montare il contenuto dei segreti nei pod Kubernetes.

1. Verificare che la versione di Helm sia v3 o successiva:
    ```azurecli
    helm version
    ```
1. Installare il driver CSI dell'archivio di segreti e il provider Azure Key Vault per il driver:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Creare un'istanza di Azure Key Vault e impostare i segreti

Per creare un'istanza di Key Vault e impostare i segreti, seguire le istruzioni riportate in [Impostare e recuperare un segreto da Azure Key Vault con l'interfaccia della riga di comando di Azure](../secrets/quick-create-cli.md).

> [!NOTE] 
> Non è necessario usare Azure Cloud Shell o creare un nuovo gruppo di risorse. È possibile usare il gruppo di risorse creato in precedenza per il cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Creare un oggetto SecretProviderClass personale

Per creare un oggetto SecretProviderClass personalizzato con parametri specifici del provider per il driver CSI dell'archivio segreti, [usare questo modello](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Questo oggetto fornirà all'identità l'accesso all'istanza di Key Vault.

Nel file YAML SecretProviderClass di esempio compilare i parametri mancanti. I parametri seguenti sono obbligatori:

* **userAssignedIdentityID**: # [OBBLIGATORIO] Se si usa un'entità servizio, usare l'ID client per specificare l'identità gestita assegnata all'utente da usare. Se si usa un'identità assegnata dall'utente come identità gestita della macchina virtuale, specificare l'ID client dell'identità. Se il valore è vuoto, per impostazione predefinita viene usata l'identità assegnata dal sistema nella macchina virtuale 
* **keyvaultName**: il nome dell'istanza di Key Vault
* **objects**: il contenitore per tutto il contenuto dei segreti da montare
    * **objectName**: il nome del contenuto dei segreti
    * **objectType**: il tipo di oggetto (segreto, chiave, certificato)
* **resourceGroup**: il nome del gruppo di risorse # [OBBLIGATORIO per versioni inferiori alla 0.0.4] il gruppo di risorse del KeyVault
* **subscriptionId:** l'ID sottoscrizione del Key Vault # [OBBLIGATORIO per versioni inferiori alla 0.0.4] l'ID sottoscrizione del KeyVault
* **tenantID**: l'ID tenant, ID directory, dell'istanza di Key Vault

La documentazione relativa a tutti i campi obbligatori è disponibile qui: [Collegamento](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Il modello aggiornato è mostrato nel codice seguente. Scaricarlo come file YAML e compilare i campi obbligatori. In questo esempio l'istanza di Key Vault è **contosoKeyVault5**. Contiene due segreti, **secret1** e **secret2**.

> [!NOTE] 
> Se si usano le identità gestite, impostare il valore di **usePodIdentity** su *true* e il valore di **userAssignedIdentityID** su una coppia di virgolette ( **""** ). 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
L'immagine seguente mostra l'output della console per **az keyvault show --name contosoKeyVault5** con i metadati pertinenti evidenziati:

![Screenshot che mostra l'output della console per "az keyvault show --name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Assegnare l'entità servizio o usare le identità gestite

### <a name="assign-a-service-principal"></a>Assegnare un'entità servizio

Se si usa un'entità servizio, assegnarle le autorizzazioni per l'accesso all'istanza di Key Vault e il recupero dei segreti. Assegnare il ruolo *Lettore* e concedere all'entità servizio le autorizzazioni per *recuperare* i segreti dall'insieme di credenziali delle chiavi usando il comando seguente:

1. Assegnare l'entità servizio all'istanza di Key Vault esistente. Il parametro **$AZURE_CLIENT_ID** è il valore di **appId** copiato dopo la creazione dell'entità servizio.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    L'output del comando è mostrato nell'immagine seguente: 

    ![Screenshot che mostra il valore di principalId](../media/kubernetes-key-vault-5.png)

1. Concedere all'entità servizio le autorizzazioni per recuperare i segreti:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. A questo punto l'entità servizio è stata configurata con le autorizzazioni per leggere i segreti dall'istanza di Key Vault. Il parametro **$AZURE_CLIENT_SECRET** è la password dell'entità servizio. Aggiungere le credenziali dell'entità servizio come segreto di Kubernetes accessibile al driver CSI dell'archivio segreti:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Se si distribuisce il pod Kubernetes e si riceve un errore relativo a un ID segreto client non valido, è possibile che l'ID segreto client sia scaduto o che sia stato reimpostato. Per risolvere questo problema, eliminare il segreto *secrets-store-creds* e crearne uno nuovo con l'ID segreto client corrente. Per eliminare *secrets-store-creds*, eseguire il comando seguente:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Se si dimentica l'ID segreto client dell'entità servizio, è possibile reimpostarlo con il comando seguente:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Usare identità gestite

Se si usano le identità gestite, assegnare ruoli specifici al cluster del servizio Azure Kubernetes creato. 

1. Per creare, elencare o leggere un'identità gestita assegnata dall'utente, è necessario assegnare al cluster del servizio Azure Kubernetes il ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Assicurarsi che il valore di **$clientId** corrisponda a quello di clientId del cluster Kubernetes. Per l'ambito, si troverà nel servizio della sottoscrizione di Azure, in particolare nel gruppo di risorse del nodo che è stato creato al momento della creazione del cluster del servizio Azure Kubernetes. Questo ambito garantisce che solo le risorse all'interno di tale gruppo siano interessate dai ruoli assegnati di seguito. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installare l'identità di Azure Active Directory (Azure AD) nel servizio Azure Kubernetes.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Creare un'identità di Azure AD. Nell'output copiare i valori di **clientId** e **principalId** per un uso successivo.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Assegnare il ruolo *Lettore* all'identità di Azure AD creata nel passaggio precedente per l'istanza di Key Vault, quindi concedere all'identità le autorizzazioni per recuperare i segreti dall'istanza di Key Vault. Usare i valori di **clientId** e **principalId** dell'identità di Azure AD.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Distribuire il pod con i segreti montati da Key Vault

Per configurare l'oggetto SecretProviderClass, eseguire il comando seguente:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Usare un’entità servizio

Se si usa un'entità servizio, usare il comando seguente per distribuire i pod Kubernetes con l'oggetto SecretProviderClass e il segreto secrets-store-creds configurato in precedenza. Ecco i modelli di distribuzione:
* Per [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Per [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Usare identità gestite

Se si usano identità gestite, creare un oggetto *AzureIdentity* nel cluster che fa riferimento all'identità creata in precedenza. Quindi, creare un oggetto *AzureIdentityBinding* che fa riferimento all'oggetto AzureIdentity creato. Compilare i parametri nel modello seguente e salvarlo come *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Eseguire il comando seguente per eseguire l'associazione:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Successivamente, distribuire il pod. Il codice seguente corrisponde al file YAML di distribuzione, che usa il binding dell'identità del pod del passaggio precedente. Salvare il file come *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
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
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
```

Eseguire il comando seguente per distribuire il pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Controllare lo stato del pod e il contenuto dei segreti 

Per visualizzare i pod distribuiti, eseguire il comando seguente:
```azurecli
kubectl get pods
```

Per controllare lo stato del pod, eseguire il comando seguente:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Screenshot dell'output dell'interfaccia della riga di comando di Azure che visualizza lo stato "In esecuzione" del pod e mostra tutti gli eventi come "Normale" ](../media/kubernetes-key-vault-6.png)

Nella finestra di output lo stato del pod distribuito dovrà essere *In esecuzione*. Nella sezione **Eventi** in basso tutti i tipi di evento vengono visualizzati come *Normale*.

Dopo aver verificato che il pod è in esecuzione, è possibile verificare che contenga i segreti dell'istanza di Key Vault.

Per visualizzare tutti i segreti contenuti nel pod, eseguire il comando seguente:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Per visualizzare il contenuto di un segreto specifico, eseguire il comando seguente:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verificare che il contenuto del segreto sia visualizzato.

## <a name="next-steps"></a>Passaggi successivi

Per assicurarsi che l'istanza di Key Vault sia recuperabile, vedere:
> [!div class="nextstepaction"]
> [Abilitare l'eliminazione temporanea](./soft-delete-cli.md)
