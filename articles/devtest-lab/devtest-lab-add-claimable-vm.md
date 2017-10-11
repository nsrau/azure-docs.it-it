---
title: Aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: tarcher
ms.openlocfilehash: 98950d72e90b0e178bae2fffa7644fd824a25eea
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs
Per aggiungere una macchina virtuale a disposizione degli utenti a un lab è necessario seguire una procedura simile all'[aggiunta di una macchina virtuale standard](devtest-lab-add-vm.md), partendo da una *base* che può essere un'[immagine personalizzata](devtest-lab-create-template.md), una [formula](devtest-lab-manage-formulas.md) o un'[immagine del Marketplace](devtest-lab-configure-marketplace-images.md). In questa esercitazione viene descritto come usare il portale di Azure per aggiungere una macchina virtuale a disposizione degli utenti a un lab in DevTest Labs e viene illustrato il processo che un utente deve seguire per richiedere la macchina virtuale.

> [!NOTE]
> Se si distribuiscono macchine virtuali lab tramite i [modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md), è possibile creare macchine virtuali a disposizione degli utenti impostando la proprietà **allowClaim** su vero nella sezione delle proprietà.
>
>

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Procedura per aggiungere una macchina virtuale a disposizione degli utenti in un lab in Azure DevTest Labs
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco di lab selezionare il lab in cui si vuole creare la VM a disposizione degli utenti.  
1. Nel pannello **Panoramica** del lab selezionare **+ Aggiungi**.  

    ![Pulsante Aggiungi VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Nel pannello **Scegli una base** selezionare una base per la macchina virtuale.
1. Nel pannello **Macchina virtuale** immettere un nome per la nuova macchina virtuale nella casella di testo **Nome macchina virtuale**.

    ![Pannello Lab VM (VM lab)](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Immettere un **Nome utente** a cui vengono concessi privilegi di amministratore nella macchina virtuale.  
1. Se si vuole usare una password archiviata nell'[archivio segreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selezionare **Use a saved secret** (Usa un segreto salvato) e specificare un valore chiave corrispondente al segreto (password). In alternativa, immettere una password nel campo di testo **Digita un valore**.
1. Il **tipo di disco della macchina virtuale** determina il tipo di disco di archiviazione consentito per le macchine virtuali nel lab.
1. Selezionare **Dimensioni macchina virtuale** e uno degli elementi predefiniti che specificano le memorie centrali del processore, la dimensione della RAM e le dimensioni dell'unità disco rigido della VM da creare.
1. Selezionare **Elementi** e dall'elenco di elementi selezionare e configurare gli elementi da aggiungere all'immagine di base. Se non si ha familiarità con DevTest Labs o con la configurazione di elementi, vedere la sezione [Aggiungere un elemento esistente in una macchina virtuale](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e tornare qui al termine dell'operazione.
1. Selezionare **Impostazioni avanzate** per configurare le opzioni di rete e le opzioni relative alla scadenza della VM. In **Claim options** (Opzioni di richiesta), scegliere **Sì** per rendere la macchina a disposizione degli utenti.

  ![Scegliere di rendere la macchina virtuale a disposizione degli utenti.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Se si vuole visualizzare o copiare il modello di Azure Resource Manager, vedere la sezione [Salvare il modello di Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) e tornare qui al termine dell'operazione.
1. Selezionare **Crea** per aggiungere la macchina virtuale specificata al lab.
1. Il pannello lab consente di visualizzare lo stato di creazione della VM prima come **Creazione**, poi come **Esecuzione** dopo aver avviato la VM.


## <a name="using-a-claimable-vm"></a>Usare una macchina virtuale a disposizione degli utenti

Un utente può richiedere qualsiasi macchina virtuale dall'elenco "Claimable virtual machines" (Macchine virtuali a disposizione degli utenti) effettuando una delle seguenti operazioni:

* Dall'elenco "Claimable virtual machines" (Macchine virtuali a disposizione degli utenti), nella parte inferiore del pannello Panoramica del lab, fare clic con il tasto destro del mouse su una delle macchine virtuali nell'elenco e scegliere **Claim machine** (Richiedi macchina).

 ![Richiedere una specifica macchina virtuale a disposizione degli utenti.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Nella parte superiore del pannello **Panoramica**, scegliere **Claim any** (Richiedi qualsiasi). Una macchina virtuale casuale viene assegnata dall'elenco di macchine virtuali a disposizione degli utenti.

 ![Richiedere una macchina virtuale a disposizione degli utenti.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Quando una macchina virtuale viene richiesta da un utente, questa viene spostata in alto nell'elenco "My virtual machines" (Le mie macchine virtuali) e non è più disponibile per un altro utente.

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere creato la VM, è possibile connettersi ad essa selezionando **Connetti** nel pannello della VM.
* Esplorare la [raccolta dei modelli Azure Resource Manager di avvio rapido di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)
