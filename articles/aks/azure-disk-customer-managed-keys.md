---
title: Usare una chiave gestita dal cliente per crittografare i dischi di Azure in Azure Kubernetes Service (AKS)
description: Bring your own key (BYOK) per crittografare i dischi dati e del sistema operativo AKS.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 96e7c401578ca8311bfe0e6b5477a9d8cab1a24e
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912720"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Usare le chiavi personalizzate (BYOK) con i dischi di Azure in Azure Kubernetes Service (AKS)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire [chiavi gestite dal cliente][customer-managed-keys] da usare per la crittografia del sistema operativo e dei dischi dati per i cluster AKS.

> [!NOTE]
> Sono supportati entrambi i cluster AKS basati su Linux e Windows.

## <a name="before-you-begin"></a>Prima di iniziare

* Questo articolo presuppone che si stia creando un *nuovo cluster AKS*.  Sarà inoltre necessario utilizzare o creare un'istanza di Azure Key Vault per archiviare le chiavi di crittografia.

* Quando si usa Key Vault per crittografare i dischi gestiti, è necessario abilitare la protezione eliminazione temporanea e ripulitura per *Azure Key Vault* .

* È necessaria l'interfaccia della riga di comando di Azure versione 2.0.79 o successiva e l'estensione AKS-Preview 0.4.26

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installare la versione di anteprima dell'interfaccia della riga di comando AKS

Per usare le chiavi gestite dal cliente, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.26 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Creare un'istanza di Azure Key Vault per archiviare le chiavi

Facoltativamente, è possibile usare la portale di Azure per [configurare le chiavi gestite dal cliente con Azure Key Vault][byok-azure-portal]

Creare un nuovo *gruppo di risorse*, quindi creare una nuova istanza di *Key Vault* e abilitare l'eliminazione temporanea e ripulire la protezione.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Creare un'istanza di un DiskEncryptionSet

Per completare i passaggi seguenti sarà necessaria una *chiave* archiviata in Azure Key Vault.  Archiviare la chiave esistente nel Key Vault creato oppure [generare una chiave][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>Concedere l'accesso alla risorsa DiskEncryptionSet all'insieme di credenziali delle chiavi

Usare i gruppi di risorse e DiskEncryptionSet creati nei passaggi precedenti e concedere alla risorsa DiskEncryptionSet l'accesso alla Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Creare un nuovo cluster AKS e crittografare il disco del sistema operativo con una chiave Customer-mangd

Creare un nuovo gruppo di risorse e un cluster AKS, quindi usare la chiave per crittografare il disco del sistema operativo. La chiave gestita dal cliente è supportata solo nelle versioni di kubernetes superiori alla 1,17

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0
```

Quando vengono aggiunti nuovi pool di nodi al cluster creato in precedenza, la chiave gestita dal cliente fornita durante la creazione viene usata per crittografare il disco del sistema operativo

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Crittografare il disco dati del cluster AKS con una chiave gestita dal cliente

È anche possibile crittografare i dischi dati AKS con le proprie chiavi.  Sostituire myResourceGroup e myDiskEncryptionSetName con i valori reali e applicare il YAML.

Assicurarsi di disporre delle credenziali AKS appropriate. L'entità servizio dovrà avere accesso come collaboratore al gruppo di risorse in cui è presente diskencryptionset. In caso contrario, si otterrà un errore che indica che l'entità servizio non ha le autorizzazioni.

Creare un file denominato **Byok-Azure-disk. YAML** che contiene le informazioni seguenti.  Sostituire myResourceGroup e myDiskEncrptionSetName con i valori.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Eseguire quindi questa distribuzione nel cluster AKS:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitazioni

* Crittografia del disco del sistema operativo supportata con Kubernetes versione 1,17 e successive   
* Disponibile solo nelle aree in cui BYOK è supportato
* Attualmente solo per i nuovi cluster AKS, i cluster esistenti non possono essere aggiornati
* Il cluster AKS che usa i set di scalabilità di macchine virtuali è obbligatorio, nessun supporto per i set di disponibilità delle macchine virtuali


## <a name="next-steps"></a>Passaggi successivi

Esaminare le [procedure consigliate per la sicurezza del cluster AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
