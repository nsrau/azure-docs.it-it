---
title: Creare una copia della macchina virtuale Linux con l&quot;interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come creare una copia della macchina virtuale Linux di Azure con l&quot;interfaccia della riga di comando di Azure 1.0 nel modello di distribuzione Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Creare la copia di una macchina virtuale Linux eseguita in Azure con l'interfaccia della riga di comando di Azure 1.0
Questo articolo descrive come creare una copia di una macchina virtuale (VM) di Azure che esegue Linux nel modello di distribuzione Resource Manager. Per prima cosa si esegue la copia dei dischi dati e del sistema operativo in un nuovo contenitore, quindi si configurano le risorse di rete e si crea la nuova macchina virtuale.

È anche possibile [caricare e creare una VM da un'immagine disco personalizzata](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- Interfaccia della riga di comando di Azure 1.0: interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="before-you-begin"></a>Prima di iniziare
Accertarsi che prima di iniziare la procedura siano soddisfatti i prerequisiti seguenti:

* Nel computer è stata scaricata e installata l' [interfaccia della riga di comando di Azure](../../cli-install-nodejs.md) . 
* Sono anche necessarie alcune informazioni sulla VM Linux di Azure esistente:

| Informazioni sulla VM di origine | Informazioni sulla collocazione |
| --- | --- |
| Nome della VM |`azure vm list` |
| Nome del gruppo di risorse |`azure vm list` |
| Location |`azure vm list` |
| Nome dell'account di archiviazione |`azure storage account list -g <resourceGroup>` |
| Nome del contenitore |`azure storage container list -a <sourcestorageaccountname>` |
| Nome del file VHD della VM di origine |`azure storage blob list --container <containerName>` |

* Sarà necessario scegliere alcune informazioni sulla nuova VM:    <br> -Nome contenitore    <br> -Nome della VM    <br> -Dimensioni della VM    <br> -Nome della vNet    <br> -Nome della SubNet    <br> -Nome dell'IP    <br> -Nome NIC

## <a name="login-and-set-your-subscription"></a>Effettuare l'accesso e impostare la sottoscrizione
1. Effettuare l'accesso all'interfaccia della riga di comando.

    ```azurecli
    azure login
    ```
2. Verificare di essere in modalità Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Impostare la sottoscrizione corretta. Per un elenco di tutte le sottoscrizioni, consultare l'elenco degli account Azure.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Arrestare la VM
Arrestare e deallocare la VM di origine. Per un elenco di tutte le VM presenti nella sottoscrizione e dei nomi dei relativi gruppi di risorse, consultare l'elenco delle VM Azure.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Copiare il file VHD
Per copiare il file VHD dall'archiviazione di origine a quella di destinazione, è possibile usare `azure storage blob copy start`. In questo esempio il file VHD viene copiato nello stesso account di archiviazione, ma in un contenitore diverso.

Per copiare il file VHD in un altro contenitore dello stesso account di archiviazione, digitare:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurare la rete virtuale per la nuova VM
Configurare una rete virtuale e una scheda NIC per la nuova VM. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Creare la nuova VM
A questo punto è possibile creare una VM dal disco rigido virtuale caricato [usando un modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) oppure tramite l'interfaccia della riga di comando, specificando l'URI del disco copiato come indicato di seguito:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova macchina virtuale, vedere [Comandi dell'interfaccia della riga di comando Azure per Azure Resource Manager](../azure-cli-arm-commands.md).


