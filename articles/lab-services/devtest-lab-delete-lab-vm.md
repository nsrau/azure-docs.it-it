---
title: Eliminare un lab o una macchina virtuale in un lab in Azure DevTest Labs | Microsoft Docs
description: Questo articolo descrive come eliminare un lab o una macchina virtuale in un lab.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 99caf04698226de8daa9cfb8f60662e5cb0f8b49
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450391"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Eliminare un lab o una macchina virtuale in un lab in Azure DevTest Labs
Questo articolo descrive come eliminare un lab o una macchina virtuale in un lab.

## <a name="delete-a-lab"></a>Eliminare un lab
Quando si elimina un'istanza di DevTest Labs da un gruppo di risorse, nel servizio DevTest Labs vengono eseguite le operazioni seguenti: 

- Tutte le risorse create automaticamente al momento della creazione del lab vengono automaticamente eliminate. Il gruppo di risorse stesso non viene eliminato. Se sono state create manualmente risorse in questo gruppo di risorse, il servizio non le elimina. 
- Tutte le macchine virtuali nel lab e tutti i gruppi di risorse associati a queste macchine virtuali vengono automaticamente eliminati. Quando si crea una macchina virtuale in un lab, il servizio crea le risorse (disco, interfaccia di rete, indirizzo IP pubblico e così via) per la macchina virtuale in un gruppo di risorse separato. Tuttavia, se in questi gruppi di risorse si creano manualmente risorse aggiuntive, il servizio DevTest Labs non elimina tali risorse né il gruppo di risorse. 

Per eliminare un lab, eseguire le operazioni seguenti: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu a sinistra, selezionare **DevTest Labs** come tipo di servizio e quindi selezionare il lab.

    ![Selezionare il lab](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Nella pagina **Istanza di DevTest Labs**fare clic su **Elimina** sulla barra degli strumenti. 

    ![Pulsante Elimina](media\devtest-lab-delete-lab-vm\delete-button.png)
4. Nella pagina **Conferma** immettere il **nome** del lab e selezionare **Elimina**. 

    ![Confirm](media\devtest-lab-delete-lab-vm\confirm-delete.png)
5. Per visualizzare lo stato dell'operazione, selezionare l'icona **Notifiche** (campanello). 

    ![Notifiche](media\devtest-lab-delete-lab-vm\delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Eliminare una macchina virtuale in un lab
Se si elimina una macchina virtuale in un lab, vengono eliminate anche alcune delle risorse (ma non tutte) create al momento della creazione del lab. Le risorse seguenti non vengono eliminate: 

-   Insieme di credenziali delle chiavi nel gruppo di risorse principale
-   Set di disponibilità, servizio di bilanciamento del carico e indirizzo IP pubblico nel gruppo di risorse della macchina virtuale. Queste risorse vengono condivise da più macchine virtuali in un gruppo di risorse. 

La macchina virtuale, l'interfaccia di rete e il disco associati alla macchina virtuale vengono invece eliminati. 

Per eliminare una macchina virtuale in un lab, eseguire le operazioni seguenti: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu a sinistra, selezionare **DevTest Labs** come tipo di servizio e quindi selezionare il lab.

    ![Selezionare il lab](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Fare clic su **...** (puntini di sospensione) accanto alla macchina virtuale nell'elenco di macchine virtuali e quindi selezionare **Elimina**. 

    ![Opzione per l'eliminazione della macchina virtuale nel menu](media\devtest-lab-delete-lab-vm\delete-vm-menu-in-list.png)
4. Nella finestra di dialogo di **conferma** selezionare **OK**. 
5. Per visualizzare lo stato dell'operazione, selezionare l'icona **Notifiche** (campanello). 

Per eliminare una macchina virtuale dalla pagina **Macchina virtuale**, selezionare **Elimina** sulla barra degli strumenti, come mostrato nell'immagine seguente:

![Opzione per l'eliminazione della macchina virtuale nella pagina della macchina virtuale](media\devtest-lab-delete-lab-vm\delete-from-vm-page.png) 


## <a name="next-steps"></a>Passaggi successivi
Se si vuole creare un lab, vedere gli articoli seguenti: 

- [Creare un lab](devtest-lab-create-lab.md)
- [Aggiungere una VM in un lab](devtest-lab-add-vm.md)