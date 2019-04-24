---
title: Guida introduttiva a Bash in Azure Cloud Shell | Microsoft Docs
description: Guida introduttiva a Bash in Cloud Shell
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: b8f96de7214a46c9e38182c141343a46c0e28139
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199627"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Guida introduttiva a Bash in Azure Cloud Shell

Questo documento illustra dettagliatamente come usare Bash in Azure Cloud Shell nel [portale di Azure](https://ms.portal.azure.com/).

> [!NOTE]
> È disponibile anche una guida introduttiva a [PowerShell in Azure Cloud Shell](quickstart-powershell.md).

## <a name="start-cloud-shell"></a>Avviare Cloud Shell
1. Avviare **Cloud Shell** dalla barra di spostamento in alto nel portale di Azure. <br>
![](media/quickstart/shell-icon.png)

2. Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione File di Microsoft Azure.
3. Fare clic su "Create storage" (Crea risorsa di archiviazione)

> [!TIP]
> Si viene automaticamente autenticati per l'interfaccia della riga di comando di Azure in ogni sessione.

### <a name="select-the-bash-environment"></a>Selezionare l'ambiente Bash
Controllare che l'elenco a discesa degli ambienti dal lato sinistro della finestra della shell sia impostato su `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Impostare la sottoscrizione
1. Elencare le sottoscrizioni a cui si ha accesso.
   ```azurecli-interactive
   az account list
   ```

2. Impostare la sottoscrizione preferita: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> La sottoscrizione verrà memorizzata per le sessioni future con `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un nuovo gruppo di risorse negli Stati Uniti occidentali denominato "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Creare una macchina virtuale Linux
Creare una VM Ubuntu nel nuovo gruppo di risorse. L'interfaccia della riga di comando di Azure creerà chiavi SSH con cui configurerà la macchina virtuale. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> L'utilizzo di `--generate-ssh-keys` indica all'interfaccia della riga di comando di Azure di creare e configurare chiavi pubbliche e private nella macchina virtuale e nella directory `$Home`. Per impostazione predefinita, le chiavi vengono inserite in Cloud Shell in corrispondenza di `/home/<user>/.ssh/id_rsa` e `/home/<user>/.ssh/id_rsa.pub`. La cartella `.ssh` è persistente nell'immagine da 5 GB della condivisione file collegata usata per rendere persistente `$Home`.

Il nome utente in questa VM sarà quello usato in Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Usare SSH per connettersi alla VM Linux
1. Cercare il nome della macchina virtuale nella barra di ricerca del portale di Azure.
2. Fare clic su "Connetti" per ottenere il nome e l'indirizzo IP pubblico della macchina virtuale. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH nella macchina virtuale con il cmd `ssh`.
   ```
   ssh username@ipaddress
   ```

Quando viene stabilita la connessione SSH, verrà visualizzato il prompt di benvenuto di Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Cleaning up 
1. Chiudere la sessione SSH.
   ```azurecli-interactive
   exit
   ```

2. Eliminare il gruppo di risorse e tutte le risorse al suo interno.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sulla persistenza dei file per Bash all’interno di Cloud Shell.](persisting-shell-storage.md) <br>
[Informazioni sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/) <br>
[Informazioni sull'archiviazione di File di Azure](../storage/files/storage-files-introduction.md) <br>
