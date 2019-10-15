---
title: "Guida introduttiva: Usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale Linux"
description: In questa guida introduttiva si apprenderà come usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 0208e72263991daceed52073bcce8b3e01f8e8c2
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174288"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Guida introduttiva: Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale Linux in Azure. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. In questa esercitazione verrà installato Ubuntu 16.04 LTS. Per mostrare la macchina virtuale in azione, connettersi a essa con SSH nella macchina virtuale e installare il server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm).

Nell'esempio seguente viene creata una macchina virtuale denominata *myVM* e aggiunto un account utente denominato *azureuser*. Il parametro `--generate-ssh-keys` viene usato per generare automaticamente una chiave SSH e inserito nella posizione predefinita della chiave ( *~/.ssh*). Per usare invece un set specifico di chiavi, usare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Si noti `publicIpAddress` nell'output della macchina virtuale. Questo indirizzo viene usato per l'accesso alla macchina virtuale nei passaggi successivi.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Per impostazione predefinita, le connessioni SSH vengono aperte solo quando si crea una macchina virtuale Linux in Azure. Usare [az vm open-port](/cli/azure/vm) per aprire la porta TCP 80 per l'uso con il server Web NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Eseguire una connessione SSH alla VM nel modo usuale. Sostituire **publicIpAddress** con l'indirizzo IP pubblico della macchina virtuale, come annotato negli output precedenti dalla VM:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web NGINX. Aggiornare le origini dei pacchetti e quindi installare il pacchetto NGINX più recente.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Al termine, digitare `exit` per uscire dalla sessione SSH.

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Usare il Web browser che si preferisce per vedere la pagina iniziale di NGINX predefinita. Usare l'indirizzo IP pubblico della macchina virtuale come indirizzo Web. L'esempio seguente mostra il sito Web NGINX predefinito:

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.


> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
