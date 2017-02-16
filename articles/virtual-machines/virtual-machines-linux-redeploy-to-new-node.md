---
title: Ridistribuire macchine virtuali Linux in Azure | Documentazione Microsoft
description: Come ridistribuire macchine virtuali Linux in Azure per ridurre i problemi di connessione SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 3295120664e409440641818b13dd1abab6f2f72f
ms.openlocfilehash: 3864148ecd09b5bc4199185bc0e5a781703f3fdb


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ridistribuire una macchina virtuale Linux in un nuovo nodo di Azure
Se si riscontrano difficoltà nella risoluzione dei problemi relativi a SSH o all'accesso delle applicazioni a una macchina virtuale (VM) Linux di Azure, potrebbe essere utile ridistribuire la VM. Quando si ridistribuisce una VM, quest'ultima viene spostata su un nuovo nodo dell'infrastruttura di Azure, quindi viene riattivata conservando tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una VM con l'interfaccia della riga di comando di Azure o il portale di Azure.

> [!NOTE]
> Dopo la ridistribuzione di una VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati. 


## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Assicurarsi che sulla macchina [sia installata la versione più recente dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e di trovarsi in modalità Resource Manager (`azure config mode arm`).

L'esempio seguente ridistribuisce la VM denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passaggi successivi
In caso di problemi di connessione alla VM, è possibile trovare assistenza specifica sulla [risoluzione dei problemi relativi alle connessioni SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o sui [passaggi dettagliati per la risoluzione dei problemi SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se non si riesce ad accedere a un'applicazione in esecuzione sulla VM, è possibile leggere l'articolo sulle [difficoltà nella risoluzione dei problemi relativi alle applicazioni](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Feb17_HO2-->


