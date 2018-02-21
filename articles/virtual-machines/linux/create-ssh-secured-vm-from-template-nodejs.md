---
title: Creare una VM Linux usando un modello di Azure con l'interfaccia della riga di comando 1.0 di Azure| Microsoft Docs
description: Creare una VM Linux in Azure con un'interfaccia della riga di comando 1.0 di Azure e un modello di Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Procedura per creare una VM Linux in Azure con un'interfaccia della riga di comando 1.0 di Azure e un modello di Azure Resource Manager
Questo articolo illustra come distribuire rapidamente una macchina virtuale Linux usando un'interfaccia della riga di comando 1.0 di Azure e un modello di Azure Resource Manager. L'articolo richiede:

* Un account Azure. È possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Accesso tramite `azure login` per l'[interfaccia della riga di comando 1.0 di Azure](../../cli-install-nodejs.md).
* L'interfaccia della riga di comando di Azure *deve essere impostata obbligatoriamente* sulla modalità Azure Resource Manager `azure config mode arm`.

È anche possibile distribuire rapidamente un modello di VM Linux usando il [portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-command-summary): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](create-ssh-secured-vm-from-template.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse

## <a name="quick-command-summary"></a>Breve riepilogo del comando
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata
I modelli consentono di creare VM in Azure con le impostazioni da personalizzare durante l'avvio, ad esempio i nomi utente e i nomi host. Per questo articolo, verrà avviato un modello di Azure utilizzando una VM Ubuntu con un gruppo di sicurezza di rete (NSG) con la porta 22 aperta per SSH.

I modelli di Azure Resource Manager sono file JSON che possono essere usati per semplici attività occasionali, ad esempio l'avvio di una VM come in questo articolo.  I modelli di Azure possono essere usati anche per costruire configurazioni di Azure complesse di interi ambienti, ad esempio uno stack di distribuzione di test, di sviluppo o di produzione.

## <a name="create-the-linux-vm"></a>Creare la VM Linux
L'esempio di codice seguente illustra come chiamare `azure group create` per creare un gruppo di risorse e distribuire una VM Linux protetta con SSH, usando contemporaneamente [questo modello di Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tenere presente che nell'esempio è necessario usare nomi univoci per l'ambiente locale. In questo esempio il nome del gruppo di risorse è *myResourceGroup* e il nome della VM è *myVM*.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

L'output dovrebbe essere simile al blocco di output seguente:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

L'esempio ha distribuito una VM usando il parametro `--template-uri` .  È anche possibile scaricare o creare un modello in locale e passarlo usando il parametro `--template-file` con un percorso del file modello come argomento. L'interfaccia della riga di comando di Azure richiede i parametri necessari per il modello.

## <a name="next-steps"></a>Passaggi successivi
Eseguire una ricerca nella [raccolta di modelli](https://azure.microsoft.com/documentation/templates/) per scoprire quali framework per app si possono distribuire successivamente.

