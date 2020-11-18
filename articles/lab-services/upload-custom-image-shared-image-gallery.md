---
title: Azure Lab Services-caricare un'immagine personalizzata in una raccolta di immagini condivise
description: Viene descritto come caricare un'immagine personalizzata nella raccolta di immagini condivise. I reparti IT universitari troveranno le immagini di importazione particolarmente utili.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 93b4141636b629168e9bb7a73e71a9fe4bfc39f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654644"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Caricare un'immagine personalizzata in Raccolta immagini condivise

Raccolta immagini condivise è disponibile per l'importazione di immagini personalizzate per la creazione di Lab in Azure Lab Services. I reparti IT universitari troveranno le immagini di importazione particolarmente utili per i motivi seguenti: 

* Non è necessario creare manualmente immagini usando una macchina virtuale modello del Lab.
* È possibile caricare immagini create usando altri strumenti, ad esempio SCCM, Endpoint Manager e così via.

Questo articolo descrive i passaggi che possono essere eseguiti per portare un'immagine personalizzata e usarla in Azure Lab Services. 

> [!IMPORTANT]
> Quando si spostano le immagini da un ambiente lab fisico a AZ Labs, è necessario ristrutturarle appropriatly. Non riutilizzare semplicemente le immagini esistenti dai lab fisici. <br/>Per informazioni dettagliate, vedere il post di Blog relativo al [passaggio da un Lab fisico a un Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) .

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Portare un'immagine personalizzata da un ambiente lab fisico

Nei passaggi seguenti viene illustrato come importare un'immagine personalizzata che viene avviata da un ambiente lab fisico. Un VHD viene quindi creato da questo ambiente e importato nella raccolta di immagini condivise in Azure, in modo che possa essere usato all'interno Azure Lab Services.

Per la creazione di un disco rigido virtuale da un ambiente lab fisico sono disponibili molte opzioni. Nei passaggi seguenti viene illustrato come creare un disco rigido virtuale da una macchina virtuale Windows Hyper-V:

1. Iniziare con una macchina virtuale Hyper-V nell'ambiente lab fisico creato dall'immagine.
    1. La macchina virtuale deve essere creata come macchina virtuale di prima generazione.
    1. La macchina virtuale deve usare una dimensione disco fisso. È anche possibile specificare le dimensioni del disco in questa finestra. Le dimensioni del disco non devono essere maggiori di 128 GB.<br/>    
    Le immagini con dimensioni del disco > 128 GB non sono supportate da Azure Lab Services. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Connetti disco rigido virtuale":::   
    1. Creare un'immagine della macchina virtuale normalmente.
1. [Connettersi alla macchina virtuale e prepararla per Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Impostare le configurazioni di Windows per Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [Controllare i servizi Windows che sono il requisito minimo necessario per garantire la connettività della macchina virtuale](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [Aggiornare le impostazioni del registro di sistema desktop remoto](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Configurare le regole del firewall di Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Installare gli aggiornamenti di Windows
    1. [Installare l'agente di macchine virtuali di Azure e la configurazione aggiuntiva, come illustrato qui](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    Nei passaggi precedenti viene creata un'immagine specializzata. Se si crea un'immagine generalizzata, sarà necessario eseguire anche [Sysprep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep). <br/>
        È necessario creare un'immagine specializzata se si vuole gestire la directory utente (che può contenere file, informazioni sull'account utente e così via) necessaria per il software incluso nell'immagine.
1. Poiché **Hyper-V** crea un file **VHDX** per impostazione predefinita, è necessario convertirlo in un file VHD.
    1. Passare a console di **gestione di Hyper-V**  ->  **Action**  ->  **modifica disco**.
    1. Qui è possibile **convertire** il disco da un VHDX a un disco rigido virtuale
    1. Quando si tenta di espandere le dimensioni del disco, assicurarsi di non superare 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Scegliere un'azione":::   
1. Caricare VHD in Azure per creare un disco gestito.
    1. È possibile usare Storage Explorer o AzCopy dalla riga di comando, come descritto in [caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).        
    Se il computer passa alla modalità di sospensione o di blocco, è possibile che il processo di caricamento venga interrotto e abbia esito negativo.
    1. Il risultato di questo passaggio è che è ora disponibile un disco gestito che è possibile visualizzare nella portale di Azure. 
        È possibile usare la scheda "Size\Performance" del portale di Azure per scegliere le dimensioni del disco. Come indicato in precedenza, le dimensioni non devono essere > 128 GB.
1. Eseguire uno snapshot del disco gestito.
    Questa operazione può essere eseguita da PowerShell, usando il portale di Azure o dall'interno di Storage Explorer, come descritto in [creare uno snapshot usando il portale o PowerShell](../virtual-machines/windows/snapshot-copy-managed-disk.md).
1. Nella raccolta immagini condivise creare una definizione e una versione dell'immagine:
    1. [Creare una definizione di immagine](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).
    1. È necessario specificare anche qui se si sta creando un'immagine specializzata o generalizzata.
1. Creare il Lab in Azure Lab Services e selezionare l'immagine personalizzata dalla raccolta immagini condivise.

    Se il disco è stato espanso dopo l'installazione del sistema operativo nella macchina virtuale Hyper-V originale, sarà necessario estendere anche l'unità C in Windows per utilizzare lo spazio su disco non allocato. A tale scopo, accedere alla macchina virtuale del modello dopo la creazione del Lab, quindi seguire questa procedura in modo analogo a quanto illustrato in [estendere un volume di base](/windows-server/storage/disk-management/extend-a-basic-volume). Sono disponibili opzioni per eseguire questa operazione tramite l'interfaccia utente e con PowerShell.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md)
* [Come usare la raccolta di immagini condivise](how-to-use-shared-image-gallery.md)