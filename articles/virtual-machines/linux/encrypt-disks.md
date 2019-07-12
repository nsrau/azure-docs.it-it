---
title: Crittografare i dischi di una macchina virtuale Linux | Documentazione Microsoft
description: Come crittografare i dischi virtuali in una VM di Linux per una maggiore sicurezza tramite l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: f38dab21d44e5ed4a01d841a515e8c50e1c713a6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667902"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Come crittografare una macchina virtuale Linux in Azure

Per migliorare gli aspetti di sicurezza e conformità delle macchine virtuali, i dischi virtuali e la macchina virtuale stessa possono essere crittografati. Le macchine virtuali vengono crittografate usando chiavi di crittografia protette in Azure Key Vault. È possibile controllare queste chiavi di crittografia e il loro uso. Questo articolo descrive come crittografare i dischi virtuali in una macchina virtuale di Linux tramite l'interfaccia della riga di comando di Azure. 

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Panoramica della crittografia dei dischi
I dischi virtuali delle VM Linux vengono crittografati a riposo mediante [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. 

Il processo per la crittografia di una VM si articola nel modo seguente:

1. Creare una chiave di crittografia in un insieme di credenziali delle chiavi di Azure.
1. Configurare la chiave di crittografia in modo da renderla utilizzabile per la crittografia dei dischi.
1. Abilitare la crittografia del disco per i dischi virtuali.
1. Le chiavi di crittografia necessarie sono richieste da Azure Key Vault.
1. I dischi virtuali vengono crittografati tramite la chiave di crittografia fornita.


## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
Requisiti relativi alla crittografia dei dischi e scenari supportati:

* I seguenti SKU dei server Linux: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
* Tutte le risorse, come l'insieme di credenziali delle chiavi, l'account di archiviazione e la VM, devono risiedere nella stessa area e nella stessa sottoscrizione di Azure.
* Macchine virtuali standard delle serie A, D, DS, G, GS e così via.
* Aggiornare le chiavi di crittografia in una VM Linux già crittografata.

La crittografia del disco non è attualmente supportata negli scenari seguenti:

* VM di base.
* VM create con il modello di distribuzione classica.
* Disabilitare la crittografia del disco del sistema operativo nelle VM Linux.
* Uso di immagini personalizzate di Linux.

Per altre informazioni su scenari supportati e limitazioni, vedere [Crittografia dischi di Azure per le macchine virtuali IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Creare le chiavi e un Azure Key Vault
Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myKey* e *myVM*.

Il primo passaggio consiste nel creare un insieme di credenziali delle chiavi di Azure in cui archiviare le chiavi di crittografia. L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Per la crittografia del disco virtuale, usare l'insieme di credenziali delle chiavi per archiviare una chiave di crittografia usata per crittografare o decrittografare i dischi virtuali.

Abilitare il provider dell'insieme di credenziali delle chiavi di Azure nella sottoscrizione di Azure con [az provider register](/cli/azure/provider#az-provider-register) e creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un nome del gruppo di risorse *myResourceGroup* nella posizione `eastus`:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

L'insieme di credenziali delle chiavi di Azure contenente le chiavi di crittografia e le risorse di calcolo associate, come l'archiviazione e la VM stessa, devono risiedere nella stessa area. Creare un insieme di credenziali delle chiavi di Azure con [az keyvault create](/cli/azure/keyvault#az-keyvault-create) e abilitare l'insieme di credenziali delle chiavi per l'utilizzo con crittografia del disco. Specificare un nome univoco di Key Vault per *keyvault_name* come indicato di seguito:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

È possibile archiviare le chiavi di crittografia usando il software o il modulo di protezione hardware. L'uso di un modulo di protezione hardware richiede un insieme di credenziali delle chiavi premium. Esiste un costo aggiuntivo per creare un insieme di credenziali delle chiavi premium, anziché standard, in cui archiviare le chiavi protette tramite software. Per creare un insieme di credenziali delle chiavi premium, aggiungere `--sku Premium` al comando del passaggio precedente. L'esempio seguente usa chiavi protette tramite software, poiché è stato creato un insieme di credenziali delle chiavi standard.

Per entrambi i modelli di protezione, è necessario consentire alla piattaforma Azure di accedere all'avvio della VM per richiedere le chiavi di crittografia con cui decrittografare i dischi virtuali. Creare una chiave di crittografia nell'insieme di credenziali delle chiavi con [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). Nell'esempio seguente viene creata una chiave denominata *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Creare una macchina virtuale con [az vm create](/cli/azure/vm#az-vm-create) e collegare un disco dati di 5 Gb. Solo determinate immagini di marketplace supportano la crittografia del disco. Nell'esempio seguente viene creata una VM denominata *myVM* tramite un'immagine di *Ubuntu 16.04 LTS*:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Stabilire una connessione alla macchina virtuale con SSH usando il valore *publicIpAddress* indicato nell'output dal comando precedente. Creare una partizione e un file system, quindi montare il disco dati. Per ulteriori informazioni, vedere [Connect to a Linux VM to mount the new disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk) (Connettersi a una VM Linux per montare il nuovo disco). Chiudere la sessione SSH.


## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale


Crittografare la macchina virtuale con [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Il completamento del processo di crittografia del disco richiede qualche istante. Monitorare lo stato del processo con [z vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Al termine, l'output sarà simile all'esempio seguente:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Aggiungere ulteriori dischi dati
Dopo aver crittografato i dischi dati, è possibile aggiungere alla VM ulteriori dischi virtuali e crittografarli. 

Dopo aver aggiunto il disco dati alla macchina virtuale, eseguire nuovamente il comando per crittografare i dischi virtuali come indicato di seguito:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione dell'insieme di credenziali delle chiavi di Azure, tra cui come eliminare chiavi crittografiche e insiemi di credenziali, vedere [Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../../key-vault/key-vault-manage-with-cli2.md).
* Per altre informazioni sulla crittografia del disco, tra cui come preparare una VM personalizzata con crittografia da caricare in Azure, vedere [Crittografia dischi di Azure](../../security/azure-security-disk-encryption.md).
