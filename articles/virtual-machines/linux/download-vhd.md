---
title: Scaricare un disco rigido virtuale Linux da Azure | Microsoft Docs
description: Scaricare un disco rigido virtuale Linux usando l'interfaccia della riga di comando di Azure e il portale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 6d827e1142fa17b29633c98c062fd204f7a49e36
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729731"
---
# <a name="download-a-linux-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file [disco rigido virtuale Linux](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) da Azure usando l'interfaccia della riga di comando di Azure e il portale di Azure. 

Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-az-cli2), se non è già installata.

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Per scaricare un disco rigido virtuale, quindi, è necessario arrestare la macchina virtuale. Se si vuole usare un disco rigido virtuale come [immagine](tutorial-custom-images.md) per la creazione di altre macchine virtuali con nuovi dischi, è necessario effettuare il deprovisioning e generalizzare il sistema operativo contenuto nel file e arrestare la macchina virtuale. Per usare il disco rigido virtuale come disco in cui creare una nuova istanza di un disco dati o di una macchina virtuale esistente, è sufficiente arrestare e deallocare la macchina virtuale.

Per usare il disco rigido virtuale come immagine per la creazione di altre macchine virtuali, completare questi passaggi:

1. Usare il protocollo SSH, il nome dell'account e l'indirizzo IP pubblico della macchina virtuale per connettersi ad essa ed effettuarne il deprovisioning. È possibile cercare un indirizzo IP pubblico con [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Il parametro +user rimuove anche l'ultimo account utente di cui è stato effettuato il provisioning. Se si sta effettuando il back-up delle credenziali dell'account nella macchina virtuale, omettere questo parametro +user. Nell'esempio seguente viene rimosso l'ultimo account utente di cui è stato effettuato il provisioning:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Accedere all'account Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index).
3. Arrestare e deallocare la macchina virtuale.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalizzare la macchina virtuale. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Per usare il disco rigido virtuale come disco in cui creare una nuova istanza di un disco dati o di una macchina virtuale esistente, completare questi passaggi:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Scegliere **Macchine virtuali**dal menu Hub.
3.  Selezionare la macchina virtuale dall'elenco.
4.  Nel pannello della VM fare clic su **Interrompi**.

    ![Arrestare la macchina virtuale](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generare l'URL SAS

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1.  Nel menu del pannello della macchina virtuale fare clic su **Dischi**.
2.  Selezionare il disco del sistema operativo relativo alla macchina virtuale e quindi fare clic su **Esporta**.
3.  Fare clic su **Genera URL**.

    ![Generare l'URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1.  Nell'URL appena generato fare clic su Scarica il file VHD.

    ![Scaricare il disco rigido virtuale](./media/download-vhd/export-download.png)

2.  Potrebbe essere necessario fare clic su **Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

    ![Fare clic su Salva nel browser.](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passaggi successivi

- Apprendere come [caricare e creare una macchina virtuale Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

