---
title: Creare una VM Linux usando l&quot;interfaccia della riga di comando di Azure 2.0 | Microsoft Azure
description: Creare una VM Linux usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0
Questo articolo illustra come distribuire rapidamente una macchina virtuale (VM) Linux in Azure con il comando [az vm create](/cli/azure/vm#create) usando l'interfaccia della riga di comando di Azure 2.0 con i dischi gestiti e i dischi disponibili negli account di archiviazione nativi. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per creare una VM, è necessario: 

* Un account Azure (è possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2)
* Essere connessi all'account Azure (digitare [az login](/cli/azure/#login))

È anche possibile distribuire una macchina virtuale Linux usando il [portale di Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L'esempio seguente illustra come distribuire una macchina virtuale Debian e come connettersi a tale VM usando la chiave SSH (Secure Shell). È possibile che gli argomenti siano diversi. Se si vuole un'immagine diversa, [è possibile cercarne una](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Uso di Managed Disks

Per usare Azure Managed Disks, è necessario usare un'area che li supporta. Digitare prima di tutto [az group create](/cli/azure/group#create) per creare un gruppo di risorse contenente tutte le risorse distribuite:

```azurecli
 az group create -n myResourceGroup -l westus
```

L'output è simile al seguente. È possibile specificare un'opzione `--output` diversa se si vuole visualizzare un formato diverso:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Creare la macchina virtuale 
È ora possibile creare la VM e il relativo ambiente. Ricordare di sostituire il valore di `--public-ip-address-dns-name` con un valore univoco. Quello riportato di seguito potrebbe essere già in uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


L'output sarà simile al seguente. Prendere nota del valore di `publicIpAddress` o di `fqdn` per connettersi con **ssh** alla VM.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Accedere alla macchina virtuale usando l'indirizzo IP pubblico o il nome di dominio completo (FQDN) elencato nell'output.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

L'output visualizzato dovrebbe essere simile al seguente, a seconda della distribuzione scelta:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Per informazioni sulle altre operazioni che possono essere eseguite nella nuova VM con Managed Disks, vedere [Passaggi successivi](#next-steps).

## <a name="using-unmanaged-disks"></a>Uso di dischi non gestiti 

Le macchine virtuali che usano i dischi di archiviazione non gestiti hanno account di archiviazione non gestiti. Digitare prima di tutto [az group create](/cli/azure/group#create) per creare un gruppo di risorse contenente tutte le risorse distribuite:

```azurecli
az group create --name nativedisks --location westus
```

L'output sarà simile al seguente. Se si vuole, è possibile scegliere un'opzione `--output` diversa.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Creare la macchina virtuale 

È ora possibile creare la VM e il relativo ambiente. Utilizzare il flag `--use-unmanaged-disk` per creare la VM con dischi non gestiti. Viene creato anche un account di archiviazione non gestito. Ricordare di sostituire il valore di `--public-ip-address-dns-name` con un valore univoco. Quello riportato di seguito potrebbe essere già in uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

L'output sarà simile al seguente. Prendere nota del valore di `publicIpAddress` o di `fqdn` per connettersi con **ssh** alla VM.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Accedere alla macchina virtuale usando l'indirizzo IP pubblico o il nome di dominio completo (FQDN). Entrambi i valori sono elencati nell'output precedente.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

L'output visualizzato dovrebbe essere simile al seguente, a seconda della distribuzione scelta:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Passaggi successivi
Il comando `az vm create` consente di distribuire rapidamente una macchina virtuale per poter accedere a una shell bash e iniziare a lavorare. L'uso di `az vm create` , tuttavia, non consente un controllo esteso né permette di creare un ambiente più complesso.  Per informazioni su come distribuire una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti:

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

È anche possibile [usare il driver di Azure `docker-machine` con vari comandi per creare rapidamente una VM Linux come host Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se si usa Java, provare il metodo [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).


