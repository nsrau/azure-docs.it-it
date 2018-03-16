---
title: Scaricare un disco rigido virtuale Linux da Azure | Microsoft Docs
description: Scaricare un disco rigido virtuale Linux usando l'interfaccia della riga di comando di Azure e il portale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: 20af28dd4caa6ee5487b9a2ed83715b9b16fad48
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="download-a-linux-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file [disco rigido virtuale Linux](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) da Azure usando l'interfaccia della riga di comando di Azure e il portale di Azure. 

In Azure, le macchine virtuali usano [dischi](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) come posizioni in cui archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure dispongono di almeno due dischi: un disco del sistema operativo Windows e un disco temporaneo. Il disco del sistema operativo viene inizialmente creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono costituiti da dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali.

Installare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), se non è già installata.

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Per scaricare un disco rigido virtuale, quindi, è necessario arrestare la macchina virtuale. Se si vuole usare un disco rigido virtuale come [immagine](tutorial-custom-images.md) per la creazione di altre macchine virtuali con nuovi dischi, è necessario effettuare il deprovisioning e generalizzare il sistema operativo contenuto nel file e arrestare la macchina virtuale. Per usare il disco rigido virtuale come disco in cui creare una nuova istanza di un disco dati o di una macchina virtuale esistente, è sufficiente arrestare e deallocare la macchina virtuale.

Per usare il disco rigido virtuale come immagine per la creazione di altre macchine virtuali, completare questi passaggi:

1. Usare il protocollo SSH, il nome dell'account e l'indirizzo IP pubblico della macchina virtuale per connettersi ad essa ed effettuarne il deprovisioning. Il parametro +user rimuove anche l'ultimo account utente di cui è stato effettuato il provisioning. Se si sta effettuando il back-up delle credenziali dell'account nella macchina virtuale, omettere questo parametro +user. Nell'esempio seguente viene rimosso l'ultimo account utente di cui è stato effettuato il provisioning:

    ```bash
    ssh azureuser@40.118.249.235
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Accedere all'account Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login).
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

- Apprendere come [caricare e creare una macchina virtuale Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

