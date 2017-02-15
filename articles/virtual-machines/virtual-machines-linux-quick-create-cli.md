---
title: Creare una VM Linux usando l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima) | Microsoft Azure
description: Creare una VM Linux usando l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima).
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0 (az.py)
Questo articolo illustra come distribuire rapidamente una macchina virtuale (VM) Linux in Azure con il comando [az vm create](/cli/azure/vm#create) usando l'interfaccia della riga di comando di Azure 2.0 (anteprima). 

> [!NOTE] 
> L'anteprima dell'interfaccia della riga di comando di Azure 2.0 è l'interfaccia della riga di comando multipiattaforma di nuova generazione. [Versione di valutazione.](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> Nel resto della documentazione viene usata l'interfaccia della riga di comando di Azure esistente. Per creare una VM usando l'interfaccia della riga di comando di Azure 1.0 esistente invece dell'anteprima della versione 2.0, vedere [Creare una VM con l'interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per creare una VM, è necessario: 

* Un account Azure (è possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Aver installato la [versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima)](/cli/azure/install-az-cli2)
* Essere connessi all'account Azure (digitare [az login](/cli/azure/#login))

È anche possibile distribuire rapidamente una VM Linux usando il [portale di Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L'esempio seguente mostra come distribuire una VM Debian e collegare la chiave SSH (Secure Shell). Gli argomenti possono variare. Se si vuole usare un'immagine diversa, è [possibile cercarne una](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Digitare prima di tutto [az group create](/cli/azure/group#create) per creare un gruppo di risorse contenente tutte le risorse distribuite:

```azurecli
az group create -n myResourceGroup -l westus
```

L'output sarà simile al seguente. Se si vuole, è possibile scegliere un'opzione `--output` diversa.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Creare la VM usando l'immagine Debian più recente

È ora possibile creare la VM e il relativo ambiente. Ricordare di sostituire il valore di `----public-ip-address-dns-name` con un valore univoco. Quello riportato di seguito potrebbe essere già in uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


L'output sarà simile al seguente. Prendere nota del valore di `publicIpAddress` o di `fqdn` per connettersi con **ssh** alla VM.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Connettersi alla macchina virtuale usando l'indirizzo IP pubblico elencato nell'output. È anche possibile usare il nome di dominio completo (FQDN) elencato.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

L'output visualizzato dovrebbe essere simile al seguente, a seconda della distribuzione scelta:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Passaggi successivi
Il comando `az vm create` consente di distribuire rapidamente una macchina virtuale per poter accedere a una shell bash e iniziare a lavorare. L'uso di `az vm create` , tuttavia, non consente un controllo esteso né permette di creare un ambiente più complesso.  Per informazioni su come distribuire una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti:

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

È anche possibile [usare il driver di Azure `docker-machine` con vari comandi per creare rapidamente una VM Linux come host Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se si usa Java, provare il metodo [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Jan17_HO1-->


