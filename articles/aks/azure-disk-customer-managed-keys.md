---
title: Usare una chiave gestita dal cliente per crittografare i dischi di Azure nel servizio Kubernetes di AzureUse a customer-managed key to encrypt Azure disks in Azure Kubernetes Service (AKS)
description: Portare le proprie chiavi (BYOK) per crittografare i dischi di dati e sistema operativo AKS.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596505"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Portare le proprie chiavi (BYOK) con i dischi di Azure nel servizio Azure Kubernetes (AKS)Bring your own keys (BYOK) with Azure disks in Azure Kubernetes Service (AKS)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivi. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire [chiavi gestite dal cliente][customer-managed-keys] da utilizzare per la crittografia inattivi sia per il sistema operativo che per i dischi dati per i cluster AKS.

> [!NOTE]
> I cluster AKS basati su BYOK Linux e Windows sono disponibili nelle [aree][supported-regions] di Azure che supportano la crittografia lato server dei dischi gestiti di Azure.BYOK Linux and Windows based AKS clusters are available in Azure regions that support server side encryption of Azure managed disks.

## <a name="before-you-begin"></a>Prima di iniziare

* In questo articolo si presuppone che si sta creando un *nuovo cluster AKS*.

* È necessario abilitare la protezione di eliminazione temporanea ed eliminazione per *l'insieme* di credenziali delle chiavi di Azure quando si usa l'insieme di credenziali delle chiavi per crittografare i dischi gestiti.

* È necessaria l'estensione 2.0.79 o successiva dell'interfaccia della riga di comando di Azure e l'estensione 0.4.26 di aks-preview

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono l'opt-in self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installare l'estensione di anteprima dell'interfaccia della riga di comando AKS più recenteInstall latest AKS CLI preview

Per usare le chiavi gestite dal cliente, è necessaria l'estensione *aks-preview* CLI versione 0.4.26 o successiva. Installare l'estensione aks-preview di Azure CLI usando il comando [az extension add,][az-extension-add] quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [az extension update:Install][az-extension-update] the *aks-preview* Azure CLI extension using the az extension add command, then check for any available updates using the az extension update command:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Creare un'istanza dell'insieme di credenziali delle chiavi di AzureCreate an Azure Key Vault instance

Usare un'istanza dell'insieme di chiavi di Azure per archiviare le chiavi.  Facoltativamente, è possibile usare il portale di Azure per [configurare le chiavi gestite dal cliente con l'insieme di][byok-azure-portal] credenziali delle chiavi di AzureYou optionally use the Azure portal to Configure customer-managed keys with Azure Key Vault

Creare un nuovo gruppo di *risorse,* quindi creare una nuova istanza *dell'insieme di* credenziali delle chiavi e abilitare la protezione per l'eliminazione temporanea e l'eliminazione temporanea.  Assicurarsi di usare gli stessi nomi di area e gruppo di risorse per ogni comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Creare un'istanza di Un DiskEncryptionSetCreate an instance of a DiskEncryptionSet

Sostituire *myKeyVaultName* con il nome dell'insieme di credenziali delle chiavi.  Per completare i passaggi seguenti è necessaria anche una chiave archiviata nell'insieme di credenziali delle chiavi di Azure.You will also need a *key* stored in Azure Key Vault to complete the following steps.  Archiviare la chiave esistente nell'insieme di credenziali delle chiavi creato nei passaggi precedenti [oppure generare una nuova chiave][key-vault-generate] e sostituire *myKeyName* di seguito con il nome della chiave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Concedere a DiskEncryptionSet l'accesso all'insieme di credenziali delle chiaviGrant the DiskEncryptionSet access to key vault

Usare il DiskEncryptionSet e i gruppi di risorse creati nei passaggi precedenti e concedere la risorsa DiskEncryptionSet all'insieme di credenziali delle chiavi di Azure.Use the DiskEncryptionSet and resource groups you created on the prior steps, and grant the DiskEncryptionSet resource access to the Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Creare un nuovo cluster AKS e crittografare il disco del sistema operativoCreate a new AKS cluster and encrypt the OS disk

Creare un **nuovo gruppo** di risorse e un cluster AKS, quindi usare la chiave per crittografare il disco del sistema operativo. Le chiavi gestite dal cliente sono supportate solo nelle versioni di Kubernetes superiori alla 1.17. 

> [!IMPORTANT]
> Assicurarsi di creare un nuovo gruppo di risorse per il cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Quando vengono aggiunti nuovi pool di nodi al cluster creato in precedenza, la chiave gestita dal cliente fornita durante la creazione viene utilizzata per crittografare il disco del sistema operativo.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Crittografare il disco dati del cluster AKSEncrypt your AKS cluster data disk

È inoltre possibile crittografare i dischi dati AKS con le proprie chiavi.

> [!IMPORTANT]
> Assicurarsi di disporre delle credenziali AKS corrette. L'entità servizio dovrà disporre dell'accesso come collaboratore al gruppo di risorse in cui è distribuito il diskencryptionset. In caso contrario, verrà visualizzato un errore che indica che l'entità servizio non dispone di autorizzazioni.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Creare un file denominato **byok-azure-disk.yaml** contenente le informazioni seguenti.  Sostituire myAzureSubscriptionId, myResourceGroup e myDiskEncrptionSetName con i valori e applicare lo yaml.  Assicurarsi di usare il gruppo di risorse in cui viene distribuito DiskEncryptionSet.Make sure to use the resource group where your DiskEncryptionSet is deployed.  Se si usa Azure Cloud Shell, questo file può essere creato usando vi o nano come se si lavorasse su un sistema virtuale o fisico:If you use the Azure Cloud Shell, this file can be created using vi or nano as if working on a virtual or physical system:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Eseguire quindi questa distribuzione nel cluster AKS:Next, run this deployment in your AKS cluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitazioni

* BYOK è attualmente disponibile solo in GA e Preview in alcune [aree di AzureBY][supported-regions] is only available in GA and Preview in certain Azure regions
* Crittografia disco del sistema operativo supportata con Kubernetes versione 1.17 e successive   
* Disponibile solo nelle aree in cui BYOK è supportato
* La crittografia con chiavi gestite dal cliente attualmente è solo per i nuovi cluster AKS, i cluster esistenti non possono essere aggiornati
* AKS cluster using Virtual Machine Scale Sets are required, no support for Virtual Machine Availability Sets


## <a name="next-steps"></a>Passaggi successivi

Esaminare le procedure consigliate per la [sicurezza dei cluster AKSReview best practices for AKS cluster security][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
