---
title: Configurare un Lab con GPU in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab con macchine virtuali GPU (Graphics Processing Unit).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 264d103cd5f1459a6d29a35a1ecefc595285587a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433989"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Configurare un Lab con macchine virtuali GPU

Questo articolo illustra come eseguire le attività seguenti:

- Scegliere tra le *visualizzazioni* e le GPU (Graphics Processing Unit) di *calcolo* .
- Verificare che siano installati i driver GPU appropriati.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Scegliere tra le dimensioni di visualizzazione e di calcolo delle GPU
Nella prima pagina della procedura guidata di creazione Lab, nell'elenco a discesa specificare le **dimensioni della macchina virtuale necessario** , è possibile selezionare le dimensioni delle macchine virtuali necessarie per la classe.  

![Screenshot del riquadro "nuovo Lab" per la selezione delle dimensioni della macchina virtuale](./media/how-to-setup-gpu/lab-gpu-selection.png)

In questo processo, è possibile selezionare la **visualizzazione** o le GPU di **calcolo** .  È importante scegliere il tipo di GPU basata sul software che gli studenti utilizzeranno.  

Come descritto nella tabella seguente, le dimensioni della GPU di *calcolo* sono destinate alle applicazioni a elevato utilizzo di calcolo.  Ad esempio, l' [apprendimento avanzato nel tipo di classe di elaborazione del linguaggio naturale](./class-type-deep-learning-natural-language-processing.md) usa la **piccola dimensione GPU (Compute)** .  La GPU di calcolo è adatta per questo tipo di classe, perché gli studenti usano Framework e strumenti di apprendimento avanzato forniti dall'immagine di [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) per eseguire il training di modelli di Deep Learning con grandi set di dati.

| Dimensione | Core | RAM | Descrizione | 
| ---- | ----- | --- | ----------- | 
| GPU small (calcolo) | -&nbsp;6 &nbsp; Core<br>-&nbsp;56 &nbsp; GB di &nbsp; RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |Queste dimensioni sono ideali per applicazioni a elevato utilizzo di calcolo, ad esempio intelligenza artificiale e apprendimento avanzato. |

Le dimensioni della GPU di *visualizzazione* sono destinate a applicazioni a elevato utilizzo di grafica.  Ad esempio, il [tipo di classe ingegneristica SolidWorks](./class-type-solidworks.md) Mostra l'uso della dimensione della **GPU (visualizzazione) piccola** .  La GPU di visualizzazione è adatta per questo tipo di classe, perché gli studenti interagiscono con l'ambiente SOLIDWORKs 3D computerizzato (CAD) per la modellazione e la visualizzazione di oggetti solidi.

| Dimensione | Core | RAM | Descrizione | 
| ---- | ----- | --- | ----------- | 
| GPU small (visualizzazione) | -&nbsp;6 &nbsp; Core<br>-&nbsp;56 &nbsp; GB di &nbsp; RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | Queste dimensioni sono ideali per la visualizzazione remota, lo streaming, i giochi e la codifica che usano Framework come OpenGL e DirectX. |
| GPU medium (visualizzazione) | -&nbsp;12 &nbsp; Core<br>-&nbsp;112 &nbsp; GB di &nbsp; RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Queste dimensioni sono ideali per la visualizzazione remota, lo streaming, i giochi e la codifica che usano Framework come OpenGL e DirectX. |

> [!NOTE]
> È possibile che alcune di queste dimensioni di macchina virtuale non vengano visualizzate nell'elenco durante la creazione di un lab per le classi. L'elenco viene popolato in base alla capacità corrente della località del lab. Se l'autore dell'account lab [consente agli autori del lab di selezionare una località per il lab](allow-lab-creator-pick-lab-location.md), è possibile provare a scegliere una località diversa per il lab e verificare se le dimensioni della macchina virtuale sono disponibili. Per la disponibilità delle macchine virtuali, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/?products=virtual-machines).

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Verificare che siano installati i driver GPU appropriati
Per sfruttare i vantaggi delle funzionalità GPU delle VM Lab, assicurarsi che siano installati i driver GPU appropriati.  Nella procedura guidata di creazione Lab, quando si selezionano le dimensioni della macchina virtuale GPU, è possibile selezionare l'opzione **Installa driver GPU** .  

![Screenshot del "nuovo Lab" che mostra l'opzione "Installa driver GPU"](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Come illustrato nell'immagine precedente, questa opzione è abilitata per impostazione predefinita, in modo da garantire che i driver *più recenti* siano installati per il tipo di GPU e l'immagine selezionata.
- Quando si seleziona una dimensione della GPU di *calcolo* , le macchine virtuali del Lab sono basate sulla GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  In questo caso, vengono installati i driver più recenti di [calcolo Unified Device Architecture (CUDA)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) , che consente un calcolo ad alte prestazioni.
- Quando si seleziona una dimensione della GPU di *visualizzazione* , le macchine virtuali del Lab sono basate sulla tecnologia GPU e [griglia](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf) [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) .  In questo caso, vengono installati i driver della griglia più recenti, che consente l'uso di applicazioni con utilizzo intensivo di grafica.

### <a name="install-the-drivers-manually"></a>Installare manualmente i driver
Potrebbe essere necessario installare una versione di driver diversa dalla versione più recente.  Questa sezione illustra come installare manualmente i driver appropriati, a seconda che si stia usando una GPU di *calcolo* o una GPU di *visualizzazione* .

#### <a name="install-the-compute-gpu-drivers"></a>Installare i driver della GPU di calcolo

Per installare manualmente i driver per le dimensioni della GPU di calcolo, procedere come segue:

1. Nella creazione guidata Lab, quando si [Crea il Lab](./how-to-manage-classroom-labs.md), disabilitare l'impostazione **Installa driver GPU** .

1. Dopo aver creato il Lab, connettersi alla macchina virtuale modello per installare i driver appropriati.

   ![Screenshot della pagina dei download del driver NVIDIA](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. In un browser passare alla [pagina download driver NVIDIA](https://www.nvidia.com/Download/index.aspx).  
   b. Impostare il **tipo di prodotto** su **Tesla**.  
   c. Impostare la **serie di prodotti** su **serie K**.  
   d. Impostare il **sistema operativo** in base al tipo di immagine di base selezionata durante la creazione del Lab.  
   e. Impostare il **Toolkit CUDA** sulla versione del driver CUDA necessario.  
   f. Selezionare **Cerca** per cercare i driver.  
   g. Selezionare **download** per scaricare il programma di installazione.  
   h. Eseguire il programma di installazione in modo che i driver siano installati nella macchina virtuale del modello.  
1. Verificare che i driver siano installati correttamente seguendo le istruzioni riportate nella sezione [convalidare i driver installati](how-to-setup-lab-gpu.md#validate-the-installed-drivers) . 
1. Dopo aver installato i driver e altri software necessari per la classe, selezionare **pubblica** per creare le macchine virtuali degli studenti.

> [!NOTE]
> Se si usa un'immagine Linux, dopo aver scaricato il programma di installazione, installare i driver seguendo le istruzioni riportate in [Install CUDA drivers on Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Installare i driver GPU per la visualizzazione

Per installare manualmente i driver per le dimensioni della GPU di visualizzazione, procedere come segue:

1. Nella creazione guidata Lab, quando si [Crea il Lab](./how-to-manage-classroom-labs.md), disabilitare l'impostazione **Installa driver GPU** .
1. Dopo aver creato il Lab, connettersi alla macchina virtuale modello per installare i driver appropriati.
1. Installare i driver della griglia forniti da Microsoft nella macchina virtuale modello seguendo le istruzioni per il sistema operativo in uso:
   -  [Driver della griglia di Windows NVIDIA](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Driver NVIDIA GRID Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#nvidia-grid-drivers)
  
1. Riavviare la macchina virtuale del modello.
1. Verificare che i driver siano installati correttamente seguendo le istruzioni riportate nella sezione [convalidare i driver installati](how-to-setup-lab-gpu.md#validate-the-installed-drivers) .
1. Dopo aver installato i driver e altri software necessari per la classe, selezionare **pubblica** per creare le macchine virtuali degli studenti.

### <a name="validate-the-installed-drivers"></a>Convalidare i driver installati
Questa sezione descrive come verificare che i driver GPU siano installati correttamente.

#### <a name="windows-images"></a>Immagini di Windows
1.  Seguire le istruzioni nella sezione "verificare l'installazione dei driver" di [installare driver GPU NVIDIA in VM serie N che eseguono Windows](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation).
1.  Se si usa una GPU di *visualizzazione* , è anche possibile:
    - Consente di visualizzare e modificare le impostazioni della GPU nel pannello di controllo di NVIDIA. A tale scopo, nel **Pannello di controllo di Windows** selezionare **hardware** e quindi selezionare **Pannello di controllo NVIDIA**.

      ![Screenshot del pannello di controllo di Windows che mostra il collegamento del pannello di controllo di NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Visualizzare le prestazioni della GPU tramite **Gestione attività**.  A tale scopo, selezionare la scheda **prestazioni** , quindi selezionare l'opzione **GPU** .

       ![Screenshot che mostra la scheda prestazioni GPU di gestione attività](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > È possibile accedere alle impostazioni del pannello di controllo NVIDIA solo per le GPU di *visualizzazione* .  Se si tenta di aprire il pannello di controllo NVIDIA per una GPU di calcolo, verrà visualizzato l'errore seguente: "le impostazioni di visualizzazione NVIDIA non sono disponibili.  Attualmente non si sta usando una visualizzazione collegata a una GPU NVIDIA ".  Analogamente, le informazioni sulle prestazioni della GPU in Gestione attività vengono fornite solo per le GPU di visualizzazione.

#### <a name="linux-images"></a>Immagini di Linux
Seguire le istruzioni nella sezione "verificare l'installazione dei driver" di [installare driver GPU NVIDIA in VM serie N che eseguono Linux](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creazione e gestione di Lab](how-to-manage-classroom-labs.md)
- [Tipo classe CAD (computer-aided design) SOLIDWORKs](class-type-solidworks.md)
- [Tipo classe MATLAB (Matrix Laboratory)](class-type-matlab.md)