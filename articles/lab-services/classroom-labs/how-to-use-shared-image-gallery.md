---
title: Usa una raccolta di immagini condivise di Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account del lab per l'uso di una raccolta di immagini condivise in modo che un utente può condividere un'immagine con altri e un altro utente può usare l'immagine per creare un modello di macchina virtuale nel lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653068"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usa una raccolta di immagini condivise di Azure Lab Services
Questo articolo illustra come amministratore di insegnanti/laboratorio consenta di risparmiare un'immagine di macchina virtuale modello affinché possa essere riutilizzato da altri utenti. Queste immagini vengono salvate in un'istanza di Azure [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md). Come primo passaggio, l'amministratore di laboratorio associa una raccolta di immagini condivise esistenti per l'account del lab. Una volta la raccolta di immagini condivise è collegata, creato nell'account di laboratorio lab può salvare immagini alla raccolta di immagini condivise. Altri docenti possono selezionare l'immagine dalla raccolta immagini condivise per creare un modello per le classi. 

## <a name="prerequisites"></a>Prerequisiti
Creare una raccolta di immagini condivise usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) oppure [CLI Azure](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Collegare una raccolta di immagini condivise a un account lab
La procedura seguente illustra come collegare una raccolta di immagini condivise a un account del lab. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Lab Services** nel **DEVOPS** sezione. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, viene aggiunto al **Preferiti** sezione nel menu a sinistra. Da e versioni successive la volta successiva, si seleziona **Lab Services** sotto **Preferiti**.

    ![All Services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selezionare l'account del lab per vedere le **Account del Lab** pagina. 
4. Selezionare **raccolta di immagini condivise** nel menu a sinistra e selezionare **Attach** sulla barra degli strumenti. 

    ![Condiviso immagine della raccolta: pulsante Aggiungi](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Nel **collegare una raccolta di immagini condivise esistenti** pagina, selezionare la raccolta di immagini condivise e selezionare **OK**.

    ![Selezionare una raccolta esistente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Viene visualizzata la schermata seguente: 

    ![Raccolta personale nell'elenco](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In questo esempio, non sono presenti immagini nella raccolta immagini condivise ancora.

Identità di Azure Lab Services viene aggiunto come collaboratore alla raccolta di immagini condivise associato al lab. Consente a docenti / amministratore IT per salvare la macchina virtuale di immagini alla raccolta di immagini condivise. Tutti i lab creati in questo account del lab hanno accesso a raccolta di immagini condivise associate. 

Tutte le immagini nella raccolta immagini condivisa associata sono abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandoli nell'elenco e usando il **abilitare le immagini selezionate** oppure **disabilitare le immagini selezionate** pulsante. 

## <a name="detach-a-shared-image-gallery"></a>Scollegare una raccolta di immagini condivise
Solo una raccolta di immagini condivise può essere collegata a un lab. Se si vuole collegare un'altra raccolta di immagini condivise, scollegare quello corrente prima di collegare una nuova. Per scollegare una raccolta di immagini condivise nell'ambiente lab, selezionare **Scollega** sulla barra degli strumenti e confermare l'operazione di scollegamento. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine alla raccolta di immagini condivise
Dopo che è associata una raccolta di immagini condivise, un docente può salvare un'immagine modello alla raccolta di immagini condivise in modo che può essere riutilizzato da altri docenti.

![Salvare l'immagine di macchina virtuale nella raccolta](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivise
Una docente può selezionare un'immagine personalizzata disponibile nella raccolta immagini condivise per il modello durante la creazione del nuovo lab.

![Usare l'immagine di macchina virtuale dalla raccolta](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte di immagini condivise, vedere [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
