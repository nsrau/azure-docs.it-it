---
title: Collegare o scollegare una raccolta di immagini condivise in Azure Lab Services Documenti Microsoft
description: Questo articolo descrive come collegare una raccolta di immagini condivise a un lab della classe in Azure Lab Services.This article describes how to attach a shared image gallery to a classroom lab in Azure Lab Services.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284317"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Collegare o scollegare una raccolta di immagini condivise in Azure Lab ServicesAttach or detach a shared image gallery in Azure Lab Services
Insegnanti/lab admin può salvare un'immagine di macchina virtuale modello in una raccolta di [immagini condivise](../../virtual-machines/windows/shared-image-galleries.md) di Azure per poter essere riutilizzata da altri utenti. Come primo passaggio, l'amministratore del lab allega una raccolta di immagini condivise esistente all'account lab. Una volta allegata la raccolta di immagini condivise, i lab creati nell'account lab possono salvare le immagini nella raccolta di immagini condivise. Altri insegnanti possono selezionare questa immagine dalla galleria di immagini condivise per creare un modello per le loro classi. 

Quando un'immagine viene salvata in una raccolta di immagini condivisa, Azure Lab Services replica l'immagine salvata in altre aree disponibili nella stessa [area geografica.](https://azure.microsoft.com/global-infrastructure/geographies/) Garantisce che l'immagine sia disponibile per i lab creati in altre aree nella stessa area geografica. Il salvataggio delle immagini in una raccolta di immagini condivise comporta un costo aggiuntivo, che include il costo per tutte le immagini replicate. Questo costo è separato dal costo di utilizzo di Azure Lab Services.This cost is separate from the Azure Lab Services usage cost. Per ulteriori informazioni sui prezzi di Shared Image Gallery, vedere [Shared Image Gallery – Billing]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

Questo articolo illustra come allegare o scollegare una raccolta di immagini condivise a un account lab. 

> [!NOTE]
> Attualmente, Azure Lab Services supporta la creazione di macchine virtuali modello basate solo su immagini di macchine virtuali **generalizzate** (immagini non specializzate) in una raccolta di immagini condivise. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account lab
Quando si crea un account lab, è possibile allegare una raccolta di immagini condivise all'account lab. È possibile selezionare una raccolta di immagini condivise esistente dall'elenco a discesa o crearne una nuova. Per creare e allegare una raccolta di immagini condivise all'account lab, selezionare **Crea nuovo**, immettere un nome per la raccolta e immettere **OK**. 

![Configurare la raccolta di immagini condivise al momento della creazione dell'account lab](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurare dopo la creazione dell'account lab
Dopo aver creato l'account lab, è possibile eseguire le attività seguenti:After the lab account is created, you can do the following tasks:

- Creare e allegare una raccolta di immagini condivise
- Allegare una raccolta di immagini condivise all'account lab
- Scollegare una raccolta di immagini condivise dall'account lab

## <a name="create-and-attach-a-shared-image-gallery"></a>Creare e allegare una raccolta di immagini condivise
1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Lab Services** nella sezione **DEVOPS**. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Lab Services** in **PREFERITI**.

    ![Tutti i servizi -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selezionare l'account lab per visualizzare la pagina **Account lab.** 
4. Selezionare **Raccolta immagini condivise** nel menu a sinistra, quindi selezionare **Crea** sulla barra degli strumenti.  

    ![Pulsante Crea raccolta immagini condivise](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Nella finestra **Crea raccolta immagini condivise** immettere un **nome** per la raccolta e scegliere **OK**. 

    ![Crea finestra della raccolta immagini condivise](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la raccolta di immagini condivise e la associa all'account lab. Tutti i lab creati in questo account lab hanno accesso alla raccolta di immagini condivise allegata. 

    ![Raccolta immagini allegate](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Nel riquadro inferiore vengono visualizzate le immagini nella raccolta immagini condivise. In questa nuova galleria, non ci sono immagini. Quando carichi le immagini nella galleria, le vedi in questa pagina.     

    Tutte le immagini nella galleria di immagini condivise allegate sono abilitate per impostazione predefinita. È possibile attivare o disattivare le immagini selezionate selezionandole nell'elenco e utilizzando il **pulsante Abilita immagini selezionate** o **Disattiva immagini selezionate.**

## <a name="attach-an-existing-shared-image-gallery"></a>Allegare una raccolta di immagini condivise esistente
Nella procedura seguente viene illustrato come allegare una raccolta di immagini condivise esistente a un account lab. 

1. Nella pagina **Account lab** selezionare Raccolta **immagini condivise** nel menu a sinistra e quindi **Selezionare Allega** sulla barra degli strumenti. 

    ![Raccolta immagini condivise - pulsante Aggiungi](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Nella pagina **Allega una raccolta immagini condivise esistente** selezionare la raccolta immagini condivise e scegliere **OK**.

    ![Selezionare una raccolta esistente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Viene visualizzata la seguente schermata: 

    ![La mia galleria nell'elenco](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In questo esempio, non ci sono ancora immagini nella galleria di immagini condivise.

    L'identità di Azure Lab Services viene aggiunta come collaboratore alla raccolta di immagini condivise associata al lab. Consente agli insegnanti/amministratore IT di salvare le immagini delle macchine virtuali nella raccolta di immagini condivise. Tutti i lab creati in questo account lab hanno accesso alla raccolta di immagini condivise allegata. 

    Tutte le immagini nella galleria di immagini condivise allegate sono abilitate per impostazione predefinita. È possibile attivare o disattivare le immagini selezionate selezionandole nell'elenco e utilizzando il **pulsante Abilita immagini selezionate** o **Disattiva immagini selezionate.** 

## <a name="detach-a-shared-image-gallery"></a>Scollegare una raccolta immagini condivise
A un lab è possibile allegare una sola raccolta di immagini condivisa. Se si desidera allegare un'altra raccolta di immagini condivise, scollegare quella corrente prima di allegare quella nuova. Per scollegare una raccolta di immagini condivise dal lab, selezionare **Scollega** sulla barra degli strumenti e confermare l'operazione di scollegamento. 

![Scollegare la raccolta di immagini condivise dall'account lab](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come salvare un'immagine del laboratorio nella raccolta di immagini condivise o usare un'immagine dalla raccolta di immagini condivise per creare una macchina virtuale, vedere Come usare la [raccolta immagini condivise.](how-to-use-shared-image-gallery.md)

Per ulteriori informazioni sulle raccolte di immagini condivise in generale, consultate [Raccolta immagini condivise.](../../virtual-machines/windows/shared-image-galleries.md)
