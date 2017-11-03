---
title: Creare una macchina virtuale di Linux mediante Azure CLI nello Stack di Azure | Documenti Microsoft
description: Creare una macchina virtuale Linux con CLI nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Creare una macchina virtuale di Linux mediante Azure CLI nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

CLI di Azure viene utilizzato per creare e gestire le risorse di Azure Stack dalla riga di comando. Dettagli di questa Guida introduttiva usando l'interfaccia CLI di Azure per creare una macchina virtuale Linux nello Stack di Azure.  Dopo aver creata la macchina virtuale, viene installato un server web e la porta 80 è aperta per consentire il traffico web.

## <a name="prerequisites"></a>Prerequisiti 

* Verificare che l'operatore di Azure Stack ha aggiunto l'immagine "Ubuntu Server 16.04 LTS" nel Marketplace dello Stack di Azure. 

* Stack di Azure richiede una versione specifica di CLI di Azure per creare e gestire le risorse. Se non è configurato per Azure Stack CLI di Azure, effettuare l'accesso per il [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e seguire i passaggi per [installare e configurare Azure CLI](azure-stack-connect-cli.md).

* Una chiave pubblica SSH con id_rsa.pub il nome deve essere creata nella directory .ssh del profilo utente Windows. Per informazioni dettagliate sulla creazione di chiavi SSH, vedere [creazione SSH chiavi in Windows](../../virtual-machines/linux/ssh-from-windows.md). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui Stack di Azure le risorse vengono distribuite e gestite. Il kit di sviluppo o lo Stack di Azure integrato system, eseguire il [gruppo az creare](/cli/azure/group#create) comando per creare un gruppo di risorse. Sono stati assegnati valori per tutte le variabili in questo documento, è possibile utilizzarli così come sono oppure assegnare un valore diverso. Nell'esempio seguente viene creato un gruppo di risorse denominato myResourceGroup nel percorso locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata myVM. L'esempio Usa Demouser per un nome utente amministrativo e Demouser@123 come password. Aggiornare i valori in modo che siano appropriati all'ambiente. Questi valori sono necessari quando ci si connette alla macchina virtuale.

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

Al termine dell'operazione, il comando verrà dei parametri di output per la macchina virtuale.  Annotare il *PublicIPAddress*, dal momento che permette di connettersi e gestire la macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Per impostazione predefinita nelle macchine virtuali Linux distribuite in Azure sono consentite solo le connessioni SSH. Se si intende usare questa macchina virtuale come un server Web, è necessario aprire la porta 80 da Internet. Usare il comando [az vm open-port](/cli/azure/vm#open-port) per aprire la porta.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Da un sistema con SSH installato, usare il comando seguente per connettersi alla macchina virtuale. Se si lavora in Windows, è possibile usare [Putty](http://www.putty.org/) per creare la connessione. Assicurarsi di sostituire con l'indirizzo IP pubblico corretto della macchina virtuale. Nell'esempio precedente, l'indirizzo IP è 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installare NGINX

Usare lo script bash seguente per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di NGINX. Assicurarsi di usare l'indirizzo *publicIpAddress* descritto in precedenza per passare alla pagina predefinita. 

![Sito NGINX predefinito](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva, aver distribuito una semplice macchina virtuale di Linux. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).

