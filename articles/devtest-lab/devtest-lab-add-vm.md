---
title: Aggiungere una VM in un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere una macchina virtuale in un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 9ac43f4ac0a58ae3326c63ab53259d7621577d34
ms.lasthandoff: 04/18/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Aggiungere una VM in un lab in Azure DevTest Labs
È possibile aggiungere una VM in un lab a partire da un elemento di *base* costituito da un'[immagine personalizzata](devtest-lab-create-template.md), da una [formula](devtest-lab-manage-formulas.md) o da un'[immagine di Marketplace](devtest-lab-configure-marketplace-images.md). Questa esercitazione illustra l'uso del portale di Azure per aggiungere una VM in un lab in DevTest Labs.

> [!NOTE]
> L'argomento [Add a claimable VM](devtest-lab-add-claimable-vm.md) (Aggiungere una macchina virtuale a disposizione degli utenti) illustra come rendere disponibile una macchina virtuale per l'uso da parte di qualsiasi utente nel laboratorio.
>
>

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passaggi per aggiungere una VM a un lab in Azure DevTest Labs
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco di lab selezionare il lab in cui si vuole creare la nuova VM.  
1. Nel pannello **Panoramica** del lab selezionare **+ Aggiungi**.  

    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Nel pannello **Scegli una base** selezionare una base per la macchina virtuale.
1. Nel pannello **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.

    ![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Immettere un **Nome utente** a cui vengono concessi privilegi di amministratore nella macchina virtuale.  
1. Se si vuole usare una password archiviata nell'[archivio segreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selezionare **Use a saved secret** (Usa un segreto salvato) e specificare un valore chiave corrispondente al segreto (password). In alternativa, immettere una password nel campo di testo **Digita un valore**.
1. Il **tipo di disco della macchina virtuale** determina il tipo di disco di archiviazione consentito per le macchine virtuali nel lab.
1. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base.
    **Nota:** se non si ha familiarità con DevTest Labs o con la configurazione di elementi, vedere la sezione [Aggiungere un elemento esistente in una macchina virtuale](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
1. Selezionare **Impostazioni avanzate** per configurare le opzioni di rete e le opzioni relative alla scadenza della VM. 

   Per impostare un'opzione di scadenza, scegliere l'icona del calendario per specificare una data in cui la macchina virtuale verrà eliminata automaticamente.  Per impostazione predefinita, la macchina virtuale non scadrà. 
1. Se si vuole visualizzare o copiare il modello di Azure Resource Manager, vedere la sezione [Salvare il modello di Azure Resource Manager](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template) e tornare qui al termine dell'operazione.
1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.
1. Il pannello lab consente di visualizzare lo stato di creazione della VM prima come **Creazione**, poi come **Esecuzione** dopo aver avviato la VM.

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere creato la VM, è possibile connettersi ad essa selezionando **Connetti** nel pannello della VM.
* Esplorare la [raccolta dei modelli Azure Resource Manager di avvio rapido di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

