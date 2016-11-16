---
title: Creare una VM Linux tramite il portale di Azure | Microsoft Docs
description: Creare una macchina virtuale Linux tramite il portale di Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Creare una VM Linux in Azure usando il portale.
Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com/) per creare una macchina virtuale Linux.

I requisiti sono:

* [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
* [File di chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>pagina di accesso
Dopo avere eseguito l'accesso al portale di Azure con l'identità dell'account Azure, fare clic su **+ Nuovo** nell'angolo superiore sinistro:

![schermata1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Scegliere una VM
Fare clic su **Macchine virtuali** nel **Marketplace** quindi su **Ubuntu Server 14.04 LTS** dall'elenco delle immagini **App in primo piano**.  Verificare nella parte inferiore che il modello di distribuzione sia `Resource Manager` e quindi fare clic su **Crea**.

![schermata2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Immettere le opzioni della VM
Nella pagina **Informazioni di base** immettere:

* Nome della macchina virtuale
* Nome utente dell'utente amministratore
* Tipo di autenticazione impostato su **Chiave pubblica SSH**
* Chiave pubblica SSH sotto forma di stringa, dalla directory `~/.ssh/`
* Nome del gruppo di risorse o selezionare un gruppo esistente

Fare clic su **OK** per continuare e scegliere le dimensioni della VM, il cui aspetto sarà simile a quello nello screenshot seguente:

![schermata3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Scegliere le dimensioni della VM
Scegliere come dimensioni **DS1** per installare Ubuntu in un'unità SSD Premium e fare clic su **Seleziona** per configurare le impostazioni.

![schermata4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Archiviazione e rete
In **Impostazioni**lasciare le impostazioni predefinite per i valori di archiviazione e di rete e fare clic su **OK** per visualizzare il riepilogo.  Si noti che il tipo di disco è stato impostato su Premium (SSD) scegliendo DS1. La lettera **S** sta per SSD.

![schermata5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Confermare le impostazioni della VM e avviare
Confermare le impostazioni per la nuova macchina virtuale Ubuntu e fare clic su **OK**.

![schermata6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Trovare la scheda di interfaccia di rete della VM
Aprire il dashboard del portale e in **Interfacce di rete** scegliere la scheda di rete.

![schermata7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Trovare l'indirizzo IP pubblico
Aprire il menu di indirizzi IP pubblici sotto le impostazioni della scheda di rete.

![schermata8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>Eseguire SSH sulla VM
Eseguire SSH per accedere all'indirizzo IP pubblico con la chiave SSH pubblica.  Da una workstation Mac o Linux è possibile eseguire SSH direttamente dal Terminale. Se si usa una workstation Windows, è necessario usare PuTTY, MobaXTerm o Cygwin per eseguire SSH su Linux.  Se non è ancora disponibile, qui è fornito un documento per preparare la workstation Windows per eseguire SSH su Linux.

[Come usare le chiavi SSH con Windows su Azure](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto è stata creata rapidamente una VM Linux da usare per scopi di test o dimostrazione. Per creare una VM Linux personalizzata per l'infrastruttura, è possibile vedere questi articoli.

* [Creare una VM Linux in Azure usando i modelli](virtual-machines-linux-cli-deploy-templates.md)
* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


