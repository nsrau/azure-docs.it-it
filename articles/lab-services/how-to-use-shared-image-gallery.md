---
title: Usare una raccolta di immagini condivise in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account lab per l'uso di una raccolta di immagini condivise in modo che un utente possa condividere un'immagine con altri utenti e un altro utente possa usare l'immagine per creare una macchina virtuale modello nel lab.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: d9f4e75163f591680cc8f85ac42c1b6ada5f2365
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647767"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usare una raccolta di immagini condivise in Azure Lab Services
Questo articolo illustra il modo in cui docenti e amministratori di lab possono salvare un'immagine di macchina virtuale modello in una [raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md) in modo che possa essere usata da altri utenti per creare lab. 

> [!IMPORTANT]
> Quando si usa una raccolta di immagini condivise, Azure Lab Services supporta solo le immagini con meno di 128 GB di spazio su disco del sistema operativo. Le immagini con più di 128 GB di spazio su disco o più dischi non verranno visualizzate nell'elenco delle immagini di macchine virtuali durante la creazione del Lab.

## <a name="scenarios"></a>Scenari
Ecco i due scenari supportati da questa funzionalità: 

- Un amministratore dell'account lab collega una raccolta di immagini condivise all'account lab e carica un'immagine nella raccolta di immagini condivise al di fuori del contesto di un lab. Quindi, gli autori del lab possono usare tale immagine dalla raccolta di immagini condivise per creare i lab. 
- Un amministratore dell'account lab collega una raccolta di immagini condivise all'account lab. Un autore del lab (docente) salva l'immagine personalizzata del suo lab nella raccolta di immagini condivise. Quindi, altri creatori di lab possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per i propri lab. 

    Quando un'immagine viene salvata in una raccolta di immagini condivise, Azure Lab Services replica l'immagine salvata in altre regioni disponibili nella stessa [area geografica](https://azure.microsoft.com/global-infrastructure/geographies/). Ciò assicura che l'immagine sia disponibile per i lab creati in altre regioni nella stessa area geografica. Il salvataggio di immagini in una raccolta di immagini condivise comporta un costo aggiuntivo, che include il costo per tutte le immagini replicate. Questo costo è separato dal costo di utilizzo di Azure Lab Services. Per altre informazioni sui prezzi della Raccolta immagini condivise, consultare [Raccolta immagini condivise - Fatturazione]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Prerequisiti
- Creare una raccolta di immagini condivise usando [Azure PowerShell](../virtual-machines/shared-images-powershell.md) o l'[interfaccia della riga di comando di Azure](../virtual-machines/shared-images-cli.md).
- Una raccolta di immagini condivise è stata collegata a un account lab. Per istruzioni dettagliate, consultare [Come collegare o rimuovere una raccolta di immagini condivise](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine nella raccolta di immagini condivise
Una volta collegata una raccolta di immagini condivise, un amministratore dell'account lab o un docente può salvare un'immagine nella raccolta di immagini condivise in modo che possa essere riutilizzata da altri docenti. 

1. Nella pagina **Modello** per il lab selezionare **Esporta in raccolta immagini condivise** sulla barra degli strumenti.

    ![Pulsante Salva immagine](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Nella finestra di dialogo **Esporta in Raccolta immagini condivise** immettere un **nome per l'immagine** e quindi selezionare **Esporta**. 

    ![Finestra di dialogo Esporta in Raccolta immagini condivise](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. È possibile visualizzare lo stato di avanzamento di questa operazione nella pagina **Modello**. L'operazione può richiedere alcuni minuti. 

    ![Esportazione in corso](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando l'operazione di esportazione ha esito positivo, viene visualizzato il messaggio seguente:

    ![Esportazione completata](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Dopo avere salvato l'immagine nella raccolta di immagini condivise, è possibile usare tale immagine dalla raccolta durante la creazione di un altro lab. È anche possibile caricare un'immagine nella raccolta di immagini condivise all'esterno del contesto di un lab. Per altre informazioni, consultare [Panoramica di raccolta di immagini condivise](../virtual-machines/shared-images-powershell.md). 

    > [!IMPORTANT]
    > Quando si [Salva un'immagine modello di un Lab](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) in Azure Lab Services in una raccolta di immagini condivise, l'immagine viene caricata nella raccolta come **immagine specializzata**. Le [Immagini specializzate](../virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) mantengono le informazioni e i profili utente specifici del computer. È comunque possibile caricare direttamente un'immagine generalizzata nella raccolta al di fuori della Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta di immagini condivise
Un docente può scegliere un'immagine personalizzata disponibile nella raccolta di immagini condivise per il modello durante la creazione di un nuovo lab.

![Usare un'immagine di macchina virtuale dalla raccolta](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> È possibile creare una macchina virtuale modello basata su immagini **generalizzate** e **specializzate** in Azure Lab Services. 


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte immagini condivise, consultare [raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md).