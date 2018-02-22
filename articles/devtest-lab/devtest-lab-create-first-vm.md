---
title: Creare la prima macchina virtuale in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare una macchina virtuale in un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: v-craic
ms.openlocfilehash: 95fc017e64ac5c8a31be93790f53eb7fefa8f69a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Creare la prima macchina virtuale in Azure DevTest Labs

Quando si accede inizialmente a DevTest Labs e si intende creare la prima macchina virtuale, questa operazione avverrà tramite un'[immagine del marketplace base](devtest-lab-configure-marketplace-images.md) precaricata. In un secondo momento durante la creazione di altre macchine virtuali sarà possibile scegliere da un [immagine personalizzata e una formula](devtest-lab-add-vm.md). 

Questa esercitazione illustra l'uso del portale di Azure per aggiungere la prima macchina virtuale in un lab in DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Passaggi per aggiungere la prima macchina virtuale a un lab in Azure DevTest Labs
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.  
1. Nel pannello **Panoramica** del lab selezionare **+ Aggiungi**.  

    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Nel pannello **Scegli una base** selezionare un'immagine del marketplace per la macchina virtuale.
1. Nel pannello **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.

    ![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Immettere un **Nome utente** a cui vengono concessi privilegi di amministratore nella macchina virtuale.  
1. Immettere una password nel campo di testo **Digita un valore**.
1. Il **tipo di disco della macchina virtuale** determina il tipo di disco di archiviazione consentito per le macchine virtuali nel lab.
1. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base.
    **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, vedere la sezione [Aggiungere un elemento esistente in una macchina virtuale](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.

   Il pannello lab consente di visualizzare lo stato di creazione della VM prima come **Creazione**, poi come **Esecuzione** dopo aver avviato la VM.

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere creato la VM, è possibile connettersi ad essa selezionando **Connetti** nel pannello della VM.
* Vedere [Aggiungere una macchina virtuale a un lab](devtest-lab-add-vm.md) per informazioni più complete sull'aggiunta di successive macchine virtuali nel lab.
* Esplorare la [raccolta dei modelli di avvio rapido di Azure Resource Manager di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
