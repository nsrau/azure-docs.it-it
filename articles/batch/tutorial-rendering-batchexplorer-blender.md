---
title: Eseguire il rendering di una scena di Blender con Azure Batch e Batch Explorer
description: Esercitazione - Come eseguire il rendering di più fotogrammi da una scena di Blender con Azure Batch e l'applicazione client Batch Explorer
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 8df9054e069540398c137290e682bb4160b4a799
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036478"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Esercitazione: Eseguire il rendering di una scena di Blender con Batch Explorer

Questa esercitazione illustra come eseguire il rendering di più fotogrammi da una scena dimostrativa di Blender. Per l'esercitazione viene usato Blender perché è gratuito sia per le VM client sia per quelle di rendering, ma il processo è molto simile anche se si usano altre applicazioni, come Maya o 3ds Max.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Caricare una scena di Blender nell'archivio di Azure
> * Creare un pool Batch con più nodi per eseguire il rendering
> * Eseguire il rendering di più fotogrammi
> * Visualizzare e scaricare i file dei fotogrammi sottoposti a rendering

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Account Azure Batch con un account di archiviazione associato.  Per creare un account Batch, vedere una delle guide introduttive di Batch, ad esempio l'[articolo relativo all'interfaccia della riga di comando](https://docs.microsoft.com/azure/batch/quick-create-cli).

Quota di core per priorità bassa con almeno 50 core, necessaria per la dimensione di VM e il numero di macchine virtuali specificati in questa esercitazione. È possibile usare la quota predefinita, ma dovrà essere usata una dimensione di VM inferiore con cui il rendering delle immagini richiederà più tempo. Il processo per richiedere un incremento della quota di core è illustrato in dettaglio in [questo articolo](https://docs.microsoft.com/azure/batch/batch-quota-limit).

È infine necessario che sia installato [Batch Explorer](https://azure.github.io/BatchExplorer/), disponibile per Windows, OSX e Linux. [Blender](https://www.blender.org/download/) è facoltativo, ma se è installato è possibile visualizzare il file di modello di esempio.

## <a name="download-the-demo-scene"></a>Scaricare la scena dimostrativa

Scaricare il file ZIP di dimostrazione "Class room" per Blender dalla [pagina Web dei file dimostrativi di Blender](https://www.blender.org/download/demo-files/) e decomprimerlo in un'unità locale.

## <a name="upload-a-scene-to-azure-storage"></a>Caricare una scena nell'archiviazione di Azure

Creare un contenitore dell'account di archiviazione per i file della scena dimostrativa:

* Avviare Batch Explorer.
* Selezionare la voce di menu "Data" (Dati) nel menu principale sul lato sinistro.
* Verificare che nell'elenco a discesa sia selezionata l'opzione "File groups" (Gruppi di file).
* Selezionare il pulsante "+" e creare un nuovo gruppo di file vuoto denominato "blender-classroom".
  * Un gruppo di file è semplicemente un contenitore BLOB di Archiviazione di Azure con prefisso "fgrp-". Questa convenzione consente di escludere tramite filtro gli altri contenitori nell'account di archiviazione.

![Gruppo di file per i file della scena](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Caricare i file della scena:

* Selezionare il nuovo contenitore e trascinare il contenuto della cartella "classroom" nel contenitore in Batch Explorer.

![File della scena caricati](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Creare un contenitore di archiviazione di Azure per le immagini di output

Creare un contenitore dell'account di archiviazione per i file di output della scena dimostrativa:

* Selezionare la voce di menu "Data" (Dati) nel menu principale sul lato sinistro.
* Selezionare il pulsante "+" e creare un nuovo gruppo di file vuoto denominato "render-output".

![Gruppo di file per i file di output](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Creare un pool di VM per il rendering

Creare un pool Batch usando l'immagine di VM di Azure Marketplace per il rendering che contiene l'applicazione Blender:

* Selezionare la voce di menu "Gallery" (Raccolta) nel menu principale sul lato sinistro.
* Selezionare l'elemento "Blender" nell'elenco degli elementi applicazione.
* Selezionare gli elementi per il rendering di fotogrammi in Windows Server.
* Facoltativamente, è possibile selezionare l'icona del collegamento a destra in corrispondenza dell'elemento per visualizzare i file modello che verranno usati per creare un pool e un processo.

![Elementi della raccolta per Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Selezionare il pulsante "Create pool for later use" (Crea pool per uso successivo).
  * Lasciare "blender-windows" come nome del pool.
  * Impostare "Dedicated vm count" (Numero VM dedicate) su "0".
  * Impostare "Low priority vm count" (Numero VM per priorità bassa) su "3".
  * Impostare "Node size" (Dimensioni nodo) su "Standard_F16". È possibile selezionare un'altra dimensione di VM, ma il tempo necessario per eseguire il rendering di un fotogramma dipenderà principalmente dal numero di core.
* Selezionare il pulsante verde per creare il pool.
  * Il pool verrà creato quasi immediatamente, ma l'allocazione e l'avvio delle VM richiederanno alcuni minuti.

![Modello di pool per Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Si noti che quando in un pool sono presenti macchine virtuali, il costo di tali VM verrà addebitato alla sottoscrizione di Azure. Per interrompere gli addebiti, è necessario eliminare il pool o le VM. Eliminare il pool alla fine di questa esercitazione per evitare continui addebiti.

Lo stato del pool e delle VM può essere monitorato nella visualizzazione "Pools" (Pool). L'esempio seguente mostra che tutte e tre le VM sono state allocate e che due sono avviate e inattive, mentre per una è ancora in corso l'avvio: ![Mappa termica del pool](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Creare un processo di rendering

Creare un processo per eseguire il rendering di alcuni fotogrammi usando il pool che è stato creato:
* Selezionare la voce di menu "Gallery" (Raccolta) nel menu principale sul lato sinistro.
* Selezionare l'elemento "Blender" nell'elenco degli elementi applicazione.
* Selezionare gli elementi per il rendering di fotogrammi in Windows Server.
* Selezionare il pulsante "Run job with existing pool" (Esegui processo con pool esistente).
* Selezionare il pool "blender-windows".
* Impostare "Job name" (Nome processo) su "blender-render-tutorial1".
* Selezionare "fgrp-blender-classroom" per "Input data" (Dati di input).
* Selezionare l'icona del file per "Blend file" (File BLEND) e quindi "classroom.blend".
* Lasciare "1" come "Frame start" (Fotogramma iniziale) e impostare "Frame end" (Fotogramma finale) su "5".
* Impostare "Outputs" (Output) su "fgrp-render-output".
* Selezionare il pulsante verde per creare il processo. Verrà creato un processo con cinque attività, una per ogni fotogramma.

![Modello di processo per Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Al termine della creazione del processo e di tutte le attività, verrà visualizzato il processo insieme alle relative attività: ![Elenco delle attività del processo](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Quando viene avviata per la prima volta l'esecuzione di un'attività in una VM del pool, viene eseguita un'attività di preparazione del processo Batch che copia i file della scena dal gruppo di file di archiviazione alla VM per consentire a Blender di accedervi.
Lo stato del rendering può essere determinato visualizzando il file di log stdout.txt generato da Blender.  Selezionando un'attività, per impostazione predefinita viene visualizzata la scheda "Task Outputs" (Output attività) ed è possibile selezionare e visualizzare il file "stdout.txt".
![File stdout](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Se viene selezionato il pool "blender-windows", le VM del pool risulteranno in stato di esecuzione: ![Mappa termica del pool con nodi in esecuzione](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

La generazione delle immagini sottoposte a rendering richiederà diversi minuti, in base alla dimensione di VM selezionata.  Usando la VM F16 specificata sopra, il rendering dei fotogrammi ha richiesto circa 16 minuti.

## <a name="view-the-rendering-output"></a>Visualizzare l'output del rendering

Al termine del rendering dei fotogrammi, le attività verranno visualizzate come completate: ![Completamento delle attività](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

L'immagine sottoposta a rendering viene scritta prima di tutto nella VM e può essere visualizzata selezionando la cartella "wd": ![Immagine sottoposta a rendering nel nodo del pool](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

Il modello di processo specifica anche il writeback dei file di log e dei fotogrammi di output nel gruppo di file dell'account di archiviazione di Azure specificato al momento della creazione del processo.  L'interfaccia utente "Data" (Dati) può essere usata per visualizzare i file di output e i log, nonché per scaricare i file: ![Immagine sottoposta a rendering nel gruppo di file di archiviazione](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Al termine di tutte le attività, il processo verrà contrassegnato come completato: ![Completamento di tutte le attività e del processo](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Pulire le risorse

> [!WARNING]
> Per interrompere l'accumulo di addebiti per le VM nella sottoscrizione di Azure, è necessario eliminare il pool. È anche possibile ridurne le dimensioni a zero nodi.

* Selezionare "Pools" (Pool).
* Selezionare il pool "blender-windows".
* Fare clic con il pulsante destro del mouse e scegliere "Delete" (Elimina) oppure selezionare l'icona del cestino sopra il pool.

## <a name="next-steps"></a>Passaggi successivi
* Nella sezione "Gallery" (Raccolta) esplorare le applicazioni di rendering disponibili tramite Batch Explorer.
* Per ogni applicazione sono disponibili diversi modelli, che verranno estesi nel tempo.  Per Blender, ad esempio, esistono modelli che suddividono una singola immagine in riquadri e consentono così di eseguire il rendering in parallelo delle parti di un'immagine.
* Per una descrizione completa delle funzionalità di rendering, vedere il set di articoli disponibile [qui](https://docs.microsoft.com/azure/batch/batch-rendering-service).
