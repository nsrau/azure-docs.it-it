---
title: Collegare o scollegare una raccolta di immagini condivise in Azure Lab Services | Microsoft Docs
description: Questo articolo descrive come collegare una raccolta di immagini condivise a un lab per le classi in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e0b29bcabe1cfb234b422982c0f8faab49c30796
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445356"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Collegare o scollegare una raccolta di immagini condivise in Azure Lab Services
Questo articolo descrive come collegare o scollegare una raccolta di immagini condivise a un account lab. 

> [!NOTE]
> Quando si [Salva un'immagine modello di un Lab](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) in Azure Lab Services in una raccolta di immagini condivise, l'immagine viene caricata nella raccolta come immagine specializzata. Le [Immagini specializzate](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) mantengono le informazioni e i profili utente specifici del computer. È comunque possibile caricare direttamente un'immagine generalizzata nella raccolta al di fuori della Azure Lab Services. 
>
> Un autore del Lab può creare una macchina virtuale modello basata su immagini generalizzate e specializzate in Azure Lab Services. 

## <a name="scenarios"></a>Scenari
Ecco i due scenari supportati da questa funzionalità: 

- Un amministratore dell'account lab collega una raccolta di immagini condivise all'account lab e carica un'immagine nella raccolta di immagini condivise al di fuori del contesto di un lab. Quindi, gli autori del lab possono usare tale immagine dalla raccolta di immagini condivise per creare i lab. 
- Un amministratore dell'account lab collega una raccolta di immagini condivise all'account lab. Un autore del lab (docente) salva l'immagine personalizzata del suo lab nella raccolta di immagini condivise. Quindi, altri creatori di lab possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per i propri lab. 

    Quando un'immagine viene salvata in una raccolta di immagini condivise, Azure Lab Services replica l'immagine salvata in altre regioni disponibili nella stessa [area geografica](https://azure.microsoft.com/global-infrastructure/geographies/). Ciò assicura che l'immagine sia disponibile per i lab creati in altre regioni nella stessa area geografica. Il salvataggio di immagini in una raccolta di immagini condivise comporta un costo aggiuntivo, che include il costo per tutte le immagini replicate. Questo costo è separato dal costo di utilizzo di Azure Lab Services. Per altre informazioni sui prezzi della Raccolta immagini condivise, consultare [Raccolta immagini condivise - Fatturazione](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Eseguire la configurazione al momento della creazione dell'account lab
È possibile collegare una raccolta di immagini condivise al momento di creare un account lab. È possibile selezionare una raccolta di immagini condivise esistente dall'elenco a discesa o crearne una nuova. Per creare e collegare una raccolta di immagini condivise all'account lab, selezionare **Crea nuova**, immettere un nome per la raccolta e premere **OK**. 

![Configurare la raccolta di immagini condivise al momento della creazione dell'account lab](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurare dopo la creazione dell'account lab
Dopo aver creato l'account lab, è possibile eseguire le attività seguenti:

- Creare e collegare una raccolta di immagini condivise
- Collegare una raccolta di immagini condivise a un account lab
- Scollegare una raccolta di immagini condivise dall'account lab

## <a name="create-and-attach-a-shared-image-gallery"></a>Creare e collegare una raccolta di immagini condivise
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Lab Services** nella sezione **DEVOPS**. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Lab Services** in **PREFERITI**.

    ![Tutti i servizi -> Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selezionare l'account lab per visualizzare la pagina **Account lab**. 
4. Selezionare **Raccolta immagini condivise** dal menu a sinistra e selezionare **+ Crea** sulla barra degli strumenti.  

    ![Pulsante Crea raccolta immagini condivise](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Nella finestra **Crea raccolta immagini condivise** immettere un **nome** per la raccolta e premere **OK**. 

    ![Finestra Crea raccolta immagini condivise](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la raccolta di immagini condivise e la collega all'account lab. Tutti i lab creati con questo account lab potranno accedere alla raccolta di immagini condivise collegata. 

    ![Raccolta immagini collegata](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Nel riquadro inferiore sono visualizzate le immagini nella raccolta di immagini condivise. In questa nuova raccolta non sono presenti immagini. Le immagini caricate nella raccolta sono visualizzate in questa pagina.     

    Tutte le immagini nella raccolta di immagini condivise collegata saranno abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandole nell'elenco e usando il pulsante **Abilita immagini selezionate** o **Disabilita immagini selezionate**.

## <a name="attach-an-existing-shared-image-gallery"></a>Collegare una raccolta di immagini condivise esistente
La procedura seguente illustra come collegare una raccolta di immagini condivise esistente a un account lab. 

1. Nella pagina **Account lab** selezionare **Raccolta immagini condivise** dal menu a sinistra e selezionare **Collega** sulla barra degli strumenti. 

    ![Raccolta immagini condivise - Pulsante Aggiungi](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Nella pagina **Collega una Raccolta immagini condivise esistente** selezionare la raccolta di immagini condivise e quindi **OK**.

    ![Selezionare una galleria esistente](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Viene visualizzata la schermata seguente: 

    ![Raccolta personale nell'elenco](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In questo esempio non sono ancora presenti immagini nella raccolta di immagini condivise.

    L'identità Azure Lab Services verrà aggiunta come collaboratore alla raccolta di immagini condivise collegata al lab. Ciò consente ai docenti e agli amministratori IT di salvare le immagini di macchine virtuali nella raccolta di immagini condivise. Tutti i lab creati con questo account lab potranno accedere alla raccolta di immagini condivise collegata. 

    Tutte le immagini nella raccolta di immagini condivise collegata saranno abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandole nell'elenco e usando il pulsante **Abilita immagini selezionate** o **Disabilita immagini selezionate**. 

## <a name="detach-a-shared-image-gallery"></a>Scollegare una raccolta di immagini condivise
È possibile collegare una sola raccolta di immagini condivise a un lab. Per collegare un'altra raccolta di immagini condivise, scollegare quella corrente prima di collegarne una nuova. Per scollegare una raccolta di immagini condivise dal lab, selezionare **Scollega** sulla barra degli strumenti e confermare l'operazione di scollegamento. 

![Scollegare una raccolta di immagini condivise dall'account lab](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come salvare un'immagine del lab nella raccolta di immagini condivise o usare un'immagine dalla raccolta di immagini condivise per creare una macchina virtuale, vedere [Come usare la raccolta di immagini condivise](how-to-use-shared-image-gallery.md).

Per altre informazioni sulle raccolte immagini condivise in generale, consultare [raccolta immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries).
