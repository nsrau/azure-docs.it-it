---
title: Collegare o scollegare una raccolta di immagini condivise di Azure Lab Services | Microsoft Docs
description: Informazioni su come collegare una raccolta di immagini condivise a un lab in Azure Lab Services.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413897"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Collegare o scollegare una raccolta di immagini condivise di Azure Lab Services
Amministratore di insegnanti/lab è possibile salvare un'immagine modello di macchina virtuale in Azure [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md) affinché possa essere riutilizzato da altri utenti. Come primo passaggio, l'amministratore di laboratorio associa una raccolta di immagini condivise esistenti per l'account del lab. Una volta la raccolta di immagini condivise è collegata, creato nell'account di laboratorio lab può salvare immagini alla raccolta di immagini condivise. Altri docenti possono selezionare l'immagine dalla raccolta immagini condivise per creare un modello per le classi. 

Questo articolo illustra come collegare o scollegare una raccolta di immagini condivise a un account del lab. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account lab
Quando si crea un account del lab, è possibile collegare una raccolta di immagini condivise per l'account del lab. È possibile selezionare una raccolta di immagini condivise esistente dall'elenco a discesa o crearne uno nuovo. Per creare e collegare una raccolta di immagini condivise per l'account del lab, selezionare **Crea nuovo**, immettere un nome per la raccolta e immettere **OK**. 

![Configurare raccolta immagini condivise al momento della creazione dell'account lab](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurare dopo aver creato l'account del lab
Dopo aver creato l'account del lab, è possibile eseguire le attività seguenti:

- Creare e collegare una raccolta di immagini condivise
- Collegare una raccolta di immagini condivise per l'account del lab
- Scollegare una raccolta di immagini condivise dall'account del lab

## <a name="create-and-attach-a-shared-image-gallery"></a>Creare e collegare una raccolta di immagini condivise
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Lab Services** nella sezione **DEVOPS**. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Lab Services** in **PREFERITI**.

    ![Tutti i servizi -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selezionare l'account del lab per vedere le **Account del Lab** pagina. 
4. Selezionare **raccolta di immagini condivise** nel menu a sinistra e selezionare **+ crea** sulla barra degli strumenti.  

    ![Immagine condivisa raccolta pulsante Crea](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Nel **raccolta di immagini condivise Create** finestra, immettere una **name** per la raccolta e immettere **OK**. 

    ![Creare finestra Raccolta immagine condivisa](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la raccolta di immagini condivise e collegato all'account del lab. Tutti i lab creati in questo account del lab hanno accesso a raccolta di immagini condivise associate. 

    ![Raccolta di immagini collegato](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Nel riquadro inferiore visualizzare immagini nella raccolta immagini condivise. In questa nuova raccolta, non sono presenti immagini. Quando si carica le immagini in gallery, è visualizzato in questa pagina.     

    Tutte le immagini nella raccolta immagini condivisa associata sono abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandoli nell'elenco e usando il **abilitare le immagini selezionate** oppure **disabilitare le immagini selezionate** pulsante.

## <a name="attach-an-existing-shared-image-gallery"></a>Collegare una raccolta di immagini condivise esistenti
La procedura seguente illustra come collegare una raccolta di immagini condivise esistenti a un account del lab. 

1. Nel **Account del Lab** pagina, selezionare **raccolta immagini condivise** nel menu a sinistra e selezionare **Attach** sulla barra degli strumenti. 

    ![Condiviso immagine della raccolta: pulsante Aggiungi](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Nel **collegare una raccolta di immagini condivise esistenti** pagina, selezionare la raccolta di immagini condivise e selezionare **OK**.

    ![Selezionare una raccolta esistente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Viene visualizzata la schermata seguente: 

    ![Raccolta personale nell'elenco](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In questo esempio, non sono presenti immagini nella raccolta immagini condivise ancora.

    Identità di Azure Lab Services viene aggiunto come collaboratore alla raccolta di immagini condivise associato al lab. Consente a docenti / amministratore IT per salvare la macchina virtuale di immagini alla raccolta di immagini condivise. Tutti i lab creati in questo account del lab hanno accesso a raccolta di immagini condivise associate. 

    Tutte le immagini nella raccolta immagini condivisa associata sono abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandoli nell'elenco e usando il **abilitare le immagini selezionate** oppure **disabilitare le immagini selezionate** pulsante. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine alla raccolta di immagini condivise
Dopo che è associata una raccolta di immagini condivise, un amministratore di account di laboratorio o un docente può salvare o caricare un'immagine alla raccolta di immagini condivise in modo che può essere riutilizzato da altri docenti. Per istruzioni per caricare un'immagine alla raccolta di immagini condivise, vedere [Cenni preliminari sulla raccolta di immagini condivise](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Attualmente, l'interfaccia di utente (UI) di Lab per le classi non supporta il salvataggio di un'immagine di lab alla raccolta di immagini condivise. 

## <a name="detach-a-shared-image-gallery"></a>Scollegare una raccolta di immagini condivise
Solo una raccolta di immagini condivise può essere collegata a un lab. Se si vuole collegare un'altra raccolta di immagini condivise, scollegare quello corrente prima di collegare una nuova. Per scollegare una raccolta di immagini condivise nell'ambiente lab, selezionare **Scollega** sulla barra degli strumenti e confermare l'operazione di scollegamento. 

![Scollegare la raccolta di immagini condivise dall'account del lab](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come salvare un'immagine di lab alla raccolta di immagini condivise o usare un'immagine dalla raccolta immagini condivise per creare una macchina virtuale, vedere [come usare una raccolta di immagini condivise](how-to-use-shared-image-gallery.md).

Per altre informazioni su condivisi raccolte di immagini in generale, vedere [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
