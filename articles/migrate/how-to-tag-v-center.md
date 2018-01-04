---
title: Raggruppare le macchine virtuali con i tag di VMware vCenter | Microsoft Docs
description: Descrive come creare un gruppo prima di eseguire una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Raggruppare le macchine virtuali con i tag di vCenter

Questo articolo descrive come creare un gruppo di macchine virtuali per eseguire la valutazione con [Azure Migrate](migrate-overview.md) tramite i tag di VMware vCenter. Si specifica la categoria di tag da usare per creare un gruppo. 

## <a name="set-up-tagging"></a>Impostare i tag

Durante la distribuzione di Azure Migrate una macchina virtuale locale di Azure Migrate individua i computer in esecuzione negli host ESXi gestiti da un server vCenter. È necessario impostare i tag di vCenter prima di eseguire il processo di individuazione.

1. In VMware vSphere Client Web accedere al server vCenter.
2. Fare clic su **Tags** (Tag) per verificare la presenza di eventuali tag correnti.
3. Per assegnare un tag a una macchina virtuale, selezionare **Related Objects** (Oggetti correlati)  > **Virtual Machines** (Macchine virtuali) e quindi scegliere la macchina virtuale a cui si vuole assegnare il tag.
4. In **Summary** (Riepilogo)  > **Tags** (Tag) fare clic su **Assign** (Assegna). 
5. Fare clic su **New Tag** (Nuovo tag) e specificare un nome e una descrizione per il tag.
6. Per creare una categoria per il tag, selezionare **New Category** (Nuova categoria) nell'elenco a discesa.
7. Specificare un nome e una descrizione per la categoria e la cardinalità. Fare quindi clic su **OK**.

    ![Tag della macchina virtuale](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Usare i tag per creare gruppi

1. Configurare l'individuazione di computer locali come descritto nell'[esercitazione sulla valutazione di VMware](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. In **Tag category for grouping** (Categoria tag per raggruppamento) selezionare la categoria di tag di vCenter su cui deve essere basato il gruppo di valutazione. Viene creato automaticamente un gruppo per la categoria selezionata.

    

## <a name="next-steps"></a>Passaggi successivi

[Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure](tutorial-assessment-vmware.md)
