---
title: Creare una macchina virtuale Linux usando Azure CLI in Azure Stack | Microsoft Docs
description: Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b3fbd91ddf06d4515ee55d24ba9330d0af6dc124
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540268"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Guida introduttiva: creare una macchina virtuale di server Linux con CLI di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Ubuntu Server 16.04 LTS tramite la CLI di Azure. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. Questo articolo fornisce anche i passaggi per:

* Connettersi alla macchina virtuale con un client remoto.
* Installare il server web NGINX e visualizzare la home page predefinita.
* Pulire le risorse inutilizzate.

## <a name="prerequisites"></a>Prerequisiti

* **Un'immagine Linux in Azure Stack marketplace**

   Per impostazione predefinita, il marketplace di Azure Stack non contiene un'immagine Linux. Ottiene l'operatore di Azure Stack per fornire il **Ubuntu Server 16.04 LTS** immagine necessaria. L'operatore può usare la procedura descritta nel [Download di elementi di marketplace di Azure ad Azure Stack](../azure-stack-download-azure-marketplace-item.md) articolo.

* Azure Stack richiede una versione specifica della riga di comando di Azure per creare e gestire le risorse. Se non si ha la CLI di Azure configurato per Azure Stack, accedere al [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o un client esterno con codifica basata su Windows se sei [connesse tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e seguire i passaggi per [ installare e configurare Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Una chiave pubblica SSH con il nome di id_rsa salvato nella directory. SSH del profilo utente di Windows. Per informazioni dettagliate sulla creazione di chiavi SSH, vedere [creazione di chiavi SSH in Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui è possibile distribuire e gestire le risorse di Azure Stack. Il kit di sviluppo o Azure Stack integrato system, eseguire la [creare il gruppo di az](/cli/azure/group#az-group-create) comando per creare un gruppo di risorse.

>[!NOTE]
 I valori vengono assegnati per tutte le variabili negli esempi di codice. Tuttavia, è possibile assegnare nuovi valori, se si desidera.

L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella località locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale usando il [az vm create](/cli/azure/vm#az-vm-create) comando. L'esempio seguente crea una macchina virtuale denominata myVM. Questo esempio Usa Demouser per un nome utente amministrativo e Demouser@123 come la password dell'utente. Modificare questi valori su un valore appropriato per l'ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

L'indirizzo IP pubblico viene restituito nel **PublicIpAddress** parametro. Annotare questo indirizzo perché necessaria per accedere alla macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Perché questa macchina virtuale verrà eseguito il server web IIS, è necessario aprire la porta 80 al traffico Internet. Usare il comando [az vm open-port](/cli/azure/vm) per aprire la porta.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Usare SSH per connettersi alla macchina virtuale

Da un computer client con SSH installato, connettersi alla macchina virtuale. Se si lavora in un client Windows, usare [Putty](https://www.putty.org/) per creare la connessione. Per connettersi alla macchina virtuale, usare il comando seguente:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Installare il server web NGINX

Per aggiornare le risorse del pacchetto e installare il pacchetto NGINX più recente, eseguire lo script seguente:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Con NGINX installato e la porta 80 aperta sulla macchina virtuale, è possibile accedere al server web tramite l'indirizzo IP pubblico della macchina virtuale. Aprire un web browser e passare a ```http://<public IP address>```.

![Pagina Completamento dell'installazione di server web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non sono più necessari. È possibile usare la [eliminazione gruppo az](/cli/azure/group#az-group-delete) comando per rimuovere queste risorse. Per eliminare il gruppo di risorse e tutte le relative risorse, eseguire il comando seguente:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una macchina virtuale di server Linux base con un server web. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
