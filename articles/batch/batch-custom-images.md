---
title: Effettuare il provisioning dei pool di Azure Batch da immagini personalizzate | Microsoft Docs
description: "È possibile creare un pool di Batch da un'immagine personalizzata per effettuare il provisioning dei nodi di calcolo che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usare un'immagine personalizzata per creare un pool di macchine virtuali

Quando si crea un pool di macchine virtuali in Azure Batch, è necessario specificare l'immagine di macchina virtuale (VM) che fornisce il sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali usando un'immagine di Azure Marketplace oppure fornendo un'immagine di disco rigido virtuale personalizzata preparata. Quando si fornisce un'immagine personalizzata, è possibile controllare il modo in cui viene configurato il sistema operativo al momento del provisioning di ogni nodo di calcolo. L'immagine personalizzata può includere anche applicazioni e dati di riferimento che diventano disponibili nel nodo di calcolo non appena viene effettuato il provisioning.

L'uso di un'immagine personalizzata permette di risparmiare tempo nel preparare i nodi di calcolo del pool per l'esecuzione del carico di lavoro di Batch. Benché sia sempre possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo averne effettuato il provisioning, questo approccio può rivelarsi meno efficiente rispetto all'uso di un'immagine personalizzata. 

Tra i motivi per usare un'immagine personalizzata configurata per lo scenario è inclusa la necessità di:

- **Configurare il sistema operativo.** Le configurazioni speciali del sistema operativo possono essere eseguite sull'immagine personalizzata. 
- **Installare applicazioni di grandi dimensioni.** L'installazione di applicazioni in un'immagine personalizzata è più efficiente che installarle in ogni nodo di calcolo dopo averne effettuato il provisioning.
- **Copiare quantità significative di dati.** Se i dati vengono copiati nell'immagine personalizzata, è necessario copiarli una sola volta, invece che in ogni nodo di calcolo, risparmiando tempo e larghezza di banda.
- **Riavviare la VM durante il processo di configurazione.** Il riavvio della VM è un processo che può richiedere molto tempo, soprattutto se sono presenti più nodi di calcolo.

## <a name="prerequisites"></a>Prerequisiti

- **Account Batch creato con la modalità di allocazione pool Sottoscrizione utente.** Per usare un'immagine personalizzata per il provisioning di pool di macchine virtuali, creare l'account Batch con la [modalità di allocazione pool](batch-api-basics.md#pool-allocation-mode) Sottoscrizione utente. Con questa modalità, i pool di Batch vengono allocati nella sottoscrizione in cui si trova l'account. Per informazioni sull'impostazione della modalità di allocazione pool per la creazione di un account Batch, vedere la sezione [Account](batch-api-basics.md#account) in [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).

- Un **account di archiviazione di Azure**. Per creare un pool di macchine virtuali usando un'immagine personalizzata, è necessario un account di archiviazione di Azure standard per utilizzo generico nella stessa sottoscrizione e nella stessa area. Se si crea un'immagine personalizzata da una VM di Azure, si copierà l'immagine nell'account di archiviazione in cui si trova il disco del sistema operativo della VM e non sarà necessario creare un account di archiviazione separato. 
    
## <a name="prepare-a-custom-image"></a>Preparare un'immagine personalizzata

Per preparare un'immagine personalizzata da usare con Batch, è necessario generalizzare un'installazione esistente di Linux o Windows. La generalizzazione di un'installazione del sistema operativo comporta la rimozione delle informazioni specifiche del computer. Il risultato è un'immagine che può essere installata in altri computer o VM.  

> [!IMPORTANT]
> Batch attualmente non supporta l'uso di immagini gestite di Azure per il provisioning di un pool. L'immagine personalizzata usata per il provisioning di un pool deve essere archiviata in Archiviazione di Azure. 
>
> Durante la preparazione dell'immagine personalizzata, tenere presente i punti seguenti:
> - Assicurarsi che l'immagine del sistema operativo di base usata per il provisioning dei pool di Batch non includa alcuna estensione di Azure preinstallata, ad esempio l'estensione Script personalizzato. Se l'immagine contiene un'estensione preinstallata, è possibile che Azure rilevi problemi durante la distribuzione della VM.
> - Assicurarsi che l'immagine del sistema operativo di base fornita usi l'unità temporanea predefinita, perché l'agente del nodo Batch prevede attualmente l'unità temporanea predefinita.
>
>

È possibile usare qualsiasi immagine Windows o Linux preparata esistente come immagine personalizzata. Per usare, ad esempio, un'immagine locale, caricare l'immagine in un account di archiviazione di Azure nella stessa sottoscrizione e nella stessa area dell'account Batch usando [AzCopy](../storage/storage-use-azcopy.md) o un altro strumento di caricamento.

È anche possibile preparare un'immagine personalizzata da una VM di Azure nuova o esistente. Se si crea una nuova VM, è possibile usare un'immagine di Azure Marketplace come immagine di base per l'immagine personalizzata e quindi personalizzarla. Per personalizzare l'immagine di base, creare una VM di Azure e aggiungervi le applicazioni o i dati. Generalizzare quindi la VM da usare come immagine personalizzata e salvarla in Archiviazione di Azure. 

Per preparare un'immagine personalizzata da una VM di Azure, seguire questa procedura:

1. Creare una VM di Azure **non gestita** da un'immagine di Azure Marketplace. Azure Marketplace include immagini sia per [Windows](../virtual-machines/windows/quick-create-portal.md) che per [Linux](../virtual-machines/linux/quick-create-portal.md).
    
    Nel passaggio 3 del processo di creazione della VM selezionare **No** per l'opzione **Archiviazione: Usa il servizio Managed Disks**. Prendere anche nota del nome dell'account di archiviazione per il disco del sistema operativo della VM, perché questo è anche l'account di archiviazione in cui Azure salverà l'immagine personalizzata:

    ![Creare una VM non gestita e prendere nota del nome dell'account di archiviazione](media/batch-custom-images/vm-create-storage.png)
 
2. Completare il processo di creazione della VM e attendere che venga allocata da Azure. Ecco un'immagine che illustra una VM nel portale di Azure nello stato in corso di esecuzione:

    ![Creare una macchina virtuale da un'immagine del marketplace](media/batch-custom-images/vm-status-running.png)

3. Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati e quindi generalizzare la VM. Seguire la procedura descritta in [Generalizzare la VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm). 
   
4. Seguire la procedura descritta in [Accedere ad Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell). Per installare Azure PowerShell, vedere [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0). 

5. Seguire quindi la procedura descritta in [Deallocare la VM e impostare lo stato generalizzato](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized). 

    Nel portale di Azure si noti che la VM è deallocata:

    ![Assicurarsi che la VM sia deallocata](media/batch-custom-images/vm-status-deallocated.png)

6.  Creare e salvare l'immagine della VM in Archiviazione di Azure usando il cmdlet di PowerShell [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage). Seguire le istruzioni descritte in [Creare l'immagine](../virtual-machines/windows/sa-copy-generalized.md#create-the-image).
    
    L'immagine della VM viene salvata nell'account di archiviazione di Azure creato quando è stata creata la VM, come illustrato nel passaggio 1 di questa procedura. Il cmdlet Save-AzureRmVMImage salva l'immagine nel contenitore **system** in tale account di archiviazione. Il parametro `-DestinationContainername` assegna un nome a una directory virtuale nel contenitore **system**. Il parametro `-VHDNamePrefix` specifica un prefisso per il nome del BLOB. Questo prefisso viene aggiunto all'inizio del nome del BLOB con un trattino. 

    Si supponga, ad esempio, di chiamare Save-AzureRmVMImage con i parametri seguenti:  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    L'immagine risultante viene salvata nel percorso e con il nome di BLOB illustrati qui:

    ![Percorso del disco rigido virtuale salvato nel contenitore system](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Una VM non gestita di Azure crea più account di archiviazione per scopi diversi. Se non si è preso nota del nome del contenitore di archiviazione per il disco del sistema operativo quando è stata creata la VM, trovare l'account di archiviazione associato che include il contenitore **system**. Spostarsi nel contenitore **system** per trovare l'immagine personalizzata usando i valori specificati per il comando **Save-AzureRmVMImage**.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Per creare un pool da un'immagine personalizzata nel portale

Dopo avere salvato l'immagine personalizzata e conoscendone il percorso, è possibile creare un pool di Batch da tale immagine. Seguire questa procedura per creare un pool dal portale di Azure:

1. Passare all'account Batch nel portale di Azure. Questo account deve trovarsi nella stessa area e nella stessa sottoscrizione dell'account di archiviazione contenente l'immagine personalizzata. 
2. Nella finestra **Impostazioni** a sinistra scegliere la voce di menu **Pool**.
3. Nella finestra **Pool** selezionare il comando **Aggiungi**.
4. Nella finestra **Aggiungi pool** selezionare **Immagine personalizzata (Linux/Windows)** nell'elenco a discesa **Tipo di immagine**. Nel portale verrà visualizzato lo strumento di selezione **Immagine personalizzata**. Passare all'account di archiviazione in cui si trova l'immagine personalizzata e selezionare il disco rigido virtuale desiderato dal contenitore. 
5. Selezionare i valori corretti per il disco rigido virtuale in **Server di pubblicazione/Offerta/SKU**.
6. Specificare le rimanenti impostazioni obbligatorie, incluse **Dimensioni nodo**, **Nodi dedicati target** e **Nodi per priorità bassa**, oltre alle impostazioni facoltative desiderate.

    Per un'immagine personalizzata di Microsoft Windows Server Datacenter 2016, ad esempio, la finestra **Aggiungi pool** viene visualizzata come illustrato qui:

    ![Aggiungere un pool da un'immagine di Windows personalizzata](media/batch-custom-images/add-pool-custom-image.png)
  
Per verificare se un pool esistente è basato su un'immagine personalizzata, vedere la proprietà **Sistema operativo** nella sezione di riepilogo delle risorse della finestra **Pool**. Se il pool è stato creato da un'immagine personalizzata, viene impostato su **Immagine di macchina virtuale personalizzata**.

Tutti i dischi rigidi virtuali personalizzati associati a un pool vengono visualizzati nella finestra **Proprietà** del pool.
 
## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
- Per informazioni sulla creazione di un account Batch, vedere [Creare un account Batch nel portale di Azure](batch-account-create-portal.md).
