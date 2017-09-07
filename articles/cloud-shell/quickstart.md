---
title: Guida introduttiva ad Azure Cloud Shell (anteprima) | Microsoft Docs
description: Guida introduttiva per Azure Cloud Shell
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 75676eb0ab784e2adbfd27b170c1dee5599b74ac
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="quickstart-for-using-the-azure-cloud-shell"></a>Guida introduttiva all'uso di Azure Cloud Shell

Questo documento illustra dettagliatamente come usare Azure Cloud Shell nel [portale di Azure](https://ms.portal.azure.com/).

## <a name="start-cloud-shell"></a>Avviare Cloud Shell
1. Avviare **Cloud Shell** dalla barra di spostamento in alto nel portale di Azure <br>
![](media/shell-icon.png)
2. Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione file di Azure
3. Fare clic su "Create storage" (Crea risorsa di archiviazione)

> [!TIP]
> Si viene automaticamente autenticati per l'interfaccia della riga di comando di Azure 2.0 in ogni sessione.

### <a name="set-your-subscription"></a>Impostare la sottoscrizione
1. Elencare le sottoscrizioni a cui si ha accesso: <br>
`az account list`
2. Impostare la sottoscrizione preferita: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> La sottoscrizione verrà memorizzata per le sessioni future con `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un nuovo gruppo di risorse negli Stati Uniti occidentali denominato "MyRG": <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Creare una macchina virtuale Linux
Creare una VM Ubuntu nel nuovo gruppo di risorse. L'interfaccia della riga di comando di Azure 2.0 creerà chiavi SSH con cui configurerà la macchina virtuale. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Per impostazione predefinita, le chiavi pubbliche e private usate per autenticare la VM vengono inserite in `/User/.ssh/id_rsa` e `/User/.ssh/id_rsa.pub` dall'interfaccia della riga di comando di Azure 2.0. La cartella .ssh è persistente nell'immagine da 5 GB della condivisione file di Azure collegata.

Il nome utente in questa VM sarà quello usato in Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Usare SSH per connettersi alla VM Linux
1. Cercare il nome della VM nella barra di ricerca del portale di Azure
2. Fare clic su "Connetti" ed eseguire: `ssh username@ipaddress`

![](media/sshcmd-copy.png)

Quando viene stabilita la connessione SSH, verrà visualizzato il prompt di benvenuto di Ubuntu.
![](media/ubuntu-welcome.png)

## <a name="cleaning-up"></a>+ Cleaning up 
Eliminare il gruppo di risorse e tutte le risorse al suo interno: <br>
Eseguire `az group delete -n MyRG`

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sull'archiviazione permanente su Cloud Shell](persisting-shell-storage.md) <br>
[Informazioni sull'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Informazioni sull'archiviazione file di Azure](../storage/files/storage-files-introduction.md) <br>
