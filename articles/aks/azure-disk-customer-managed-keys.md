---
title: Usare una chiave gestita dal cliente per crittografare i dischi di Azure in Azure Kubernetes Service (AKS)
description: Bring your own key (BYOK) per crittografare i dischi dati e del sistema operativo AKS.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: 6452facc999456c35aa5d1c3bfe6b2f59141b7c5
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252046"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Usare le chiavi personalizzate (BYOK) con i dischi di Azure in Azure Kubernetes Service (AKS)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire [chiavi gestite dal cliente][customer-managed-keys] da usare per la crittografia dei dati inattivi per i dischi del sistema operativo e dei dati per i cluster AKS.

> [!NOTE]
> BYOK Linux e i cluster AKS basati su Windows sono disponibili nelle [aree di Azure][supported-regions] che supportano la crittografia lato server di Azure Managed Disks.

## <a name="before-you-begin"></a>Prima di iniziare

* Questo articolo presuppone che si stia creando un *nuovo cluster AKS*.

* Quando si usa Key Vault per crittografare i dischi gestiti, è necessario abilitare la protezione eliminazione temporanea e ripulitura per *Azure Key Vault* .

* È necessaria l'interfaccia della riga di comando di Azure versione 2.0.79 o successiva e l'estensione AKS-Preview 0.4.26

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto del servizio Azure Kubernetes](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installare la versione di anteprima dell'interfaccia della riga di comando AKS

Per usare le chiavi gestite dal cliente, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.26 o versione successiva. Installare l'estensione *aks-preview* dell'interfaccia della riga di comando di Azure usando il comando [az extension add][az-extension-add], quindi verificare la disponibilità di eventuali aggiornamenti usando il comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Creare un'istanza di Azure Key Vault

Utilizzare un'istanza di Azure Key Vault per archiviare le chiavi.  Facoltativamente, è possibile usare la portale di Azure per [configurare le chiavi gestite dal cliente con Azure Key Vault][byok-azure-portal]

Creare un nuovo *gruppo di risorse*, quindi creare una nuova istanza di *Key Vault* e abilitare l'eliminazione temporanea e ripulire la protezione.  Assicurarsi di usare la stessa area e i nomi dei gruppi di risorse per ogni comando.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Creare un'istanza di un DiskEncryptionSet

Sostituire *myKeyVaultName* con il nome dell'insieme di credenziali delle chiavi.  Per completare i passaggi seguenti sarà necessaria anche una *chiave* archiviata in Azure Key Vault.  Archiviare la chiave esistente nell'Key Vault creata nei passaggi precedenti oppure [generare una nuova chiave][key-vault-generate] e sostituire *myKeyName* con il nome della chiave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Concedere a DiskEncryptionSet l'accesso a Key Vault

Usare i gruppi di risorse e DiskEncryptionSet creati nei passaggi precedenti e concedere alla risorsa DiskEncryptionSet l'accesso alla Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Creare un nuovo cluster AKS e crittografare il disco del sistema operativo

Creare un **nuovo gruppo di risorse** e un cluster AKS, quindi usare la chiave per crittografare il disco del sistema operativo. Le chiavi gestite dal cliente sono supportate solo nelle versioni Kubernetes maggiori di 1,17. 

> [!IMPORTANT]
> Assicurarsi di creare un nuovo gruppo di risorsa per il cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Quando vengono aggiunti nuovi pool di nodi al cluster creato in precedenza, la chiave gestita dal cliente fornita durante la creazione viene usata per crittografare il disco del sistema operativo.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Crittografare il disco dati del cluster AKS (facoltativo)
La chiave di crittografia del disco del sistema operativo verrà usata per crittografare il disco dati se non viene fornita la chiave per il disco dati da v 1.17.2 ed è anche possibile crittografare i dischi dati AKS con le altre chiavi.

> [!IMPORTANT]
> Assicurarsi di disporre delle credenziali AKS appropriate. L'entità servizio dovrà avere accesso come collaboratore al gruppo di risorse in cui viene distribuito il diskencryptionset. In caso contrario, si otterrà un errore che indica che l'entità servizio non ha le autorizzazioni.

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

Creare un file denominato **Byok-Azure-disk. YAML** che contiene le informazioni seguenti.  Sostituire myAzureSubscriptionId, myResourceGroup e myDiskEncrptionSetName con i valori e applicare il YAML.  Assicurarsi di usare il gruppo di risorse in cui è distribuito il DiskEncryptionSet.  Se si usa il Azure Cloud Shell, questo file può essere creato usando vi o nano come se si lavorasse in un sistema fisico o virtuale:

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
Eseguire quindi questa distribuzione nel cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitazioni

* BYOK è attualmente disponibile solo in GA e anteprima in alcune [aree di Azure][supported-regions]
* Crittografia del disco dati supportata con Kubernetes versione 1,17 e successive   
* Disponibile solo nelle aree in cui BYOK è supportato
* La crittografia con chiavi gestite dal cliente è attualmente solo per i nuovi cluster AKS. i cluster esistenti non possono essere aggiornati

## <a name="next-steps"></a>Passaggi successivi

Esaminare le [procedure consigliate per la sicurezza del cluster AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/storage-encryption-keys-portal.md
[customer-managed-keys]: ../virtual-machines/windows/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/windows/disk-encryption.md#supported-regions
