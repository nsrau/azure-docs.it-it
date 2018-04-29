---
title: Creare una macchina virtuale di Linux mediante Azure CLI nello Stack di Azure | Documenti Microsoft
description: Creare una macchina virtuale Linux con CLI nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Guida introduttiva: creare una macchina virtuale di Linux server usando l'interfaccia CLI di Azure nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile creare una macchina virtuale Ubuntu Server 16.04 LTS usando l'interfaccia CLI di Azure. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. In questo articolo offre inoltre la procedura per:

* Connettersi alla macchina virtuale con un client remoto.
* Installare il server web NGINX e visualizzare la home page predefinita.
* Pulire le risorse inutilizzate.

## <a name="prerequisites"></a>Prerequisiti

* **Un'immagine Linux nel marketplace Azure Stack**

   Per impostazione predefinita, il marketplace Azure Stack non contiene un'immagine Linux. Ottenere l'operatore di Stack di Azure per fornire il **Ubuntu Server 16.04 LTS** immagine necessaria. L'operatore può utilizzare i passaggi descritti nel [scaricare elementi del marketplace da Azure a Azure Stack](../azure-stack-download-azure-marketplace-item.md) articolo.

* Stack di Azure richiede una versione specifica dell'interfaccia CLI di Azure per creare e gestire le risorse. Se non si ha l'interfaccia CLI di Azure configurato per lo Stack di Azure, effettuare l'accesso per il [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o un client esterno con codifica basata su Windows se si è [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e seguire i passaggi per [ installare e configurare Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Una chiave SSH pubblica con il nome di id_rsa.pub salvati nella directory .ssh del profilo utente Windows. Per informazioni dettagliate sulla creazione di chiavi SSH, vedere [creazione SSH chiavi in Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui è possibile distribuire e gestire le risorse di Azure Stack. Il kit di sviluppo o lo Stack di Azure integrato system, eseguire il [gruppo az creare](/cli/azure/group#az_group_create) comando per creare un gruppo di risorse.

>[!NOTE]
 I valori vengono assegnati per tutte le variabili negli esempi di codice. Tuttavia, è possibile assegnare nuovi valori se si desidera.

Nell'esempio seguente viene creato un gruppo di risorse denominato myResourceGroup nel percorso locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale utilizzando il [creare vm az](/cli/azure/vm#az_vm_create) comando. L'esempio seguente crea una macchina virtuale denominata myVM. Questo esempio viene utilizzato Demouser per un nome utente amministrativo e Demouser@123 come password dell'utente. Modificare questi valori in un valore appropriato per l'ambiente.

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

L'indirizzo IP pubblico viene restituito nel **PublicIpAddress** parametro. Annotare questo indirizzo, poiché è necessario per accedere alla macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Poiché questa macchina virtuale è in esecuzione il server web IIS, è necessario aprire la porta 80 per il traffico Internet. Usare il comando [az vm open-port](/cli/azure/vm#open-port) per aprire la porta.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Usare SSH per la connessione alla macchina virtuale

Da un computer client con SSH installato, connettersi alla macchina virtuale. Se si lavora in un client Windows, utilizzare [Putty](http://www.putty.org/) per creare la connessione. Per connettersi alla macchina virtuale, usare il comando seguente:

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

Con NGINX installato e la porta 80 aperta nella macchina virtuale, è possibile accedere al server web utilizzando l'indirizzo IP pubblico della macchina virtuale. Aprire un web browser e passare a ```http://<public IP address>```.

![Pagina Completamento dell'installazione di server web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non è necessario più. È possibile usare il [eliminazione di gruppi di az](/cli/azure/group#az_group_delete) comando per rimuovere queste risorse. Per eliminare il gruppo di risorse e tutte le relative risorse, eseguire il comando seguente:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida, è stato distribuito una macchina virtuale di server Linux base con un server web. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
