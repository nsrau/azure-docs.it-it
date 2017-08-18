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
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>Usare un'immagine personalizzata per creare un pool

Quando si crea un pool in Azure Batch, è necessario specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool usando un'immagine di Azure Marketplace oppure fornendo un'immagine personalizzata preparata. Quando si fornisce un'immagine personalizzata, è possibile controllare il modo in cui viene configurato il sistema operativo al momento del provisioning di ogni nodo di calcolo. L'immagine personalizzata può includere anche applicazioni e dati di riferimento che diventano disponibili nel nodo di calcolo non appena viene effettuato il provisioning.

L'uso di un'immagine personalizzata permette di risparmiare tempo nel preparare i nodi di calcolo del pool per l'esecuzione del carico di lavoro di Batch. Benché sia sempre possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo averne effettuato il provisioning, questo approccio può rivelarsi meno efficiente rispetto all'uso di un'immagine personalizzata. Se è necessario installare applicazioni di grandi dimensioni, copiare quantità significative di dati o riavviare la VM durante il processo di configurazione, provare a usare un'immagine personalizzata configurata in base alle esigenze.  

## <a name="prerequisites"></a>Prerequisiti

- **Account Batch creato con la modalità di allocazione pool Sottoscrizione utente.** Per usare un'immagine personalizzata per il provisioning di pool di macchine virtuali, creare l'account Batch con la [modalità di allocazione pool](batch-api-basics.md#pool-allocation-mode) Sottoscrizione utente. Con questa modalità, i pool di Batch vengono allocati nella sottoscrizione in cui si trova l'account. Per informazioni sull'impostazione della modalità di allocazione pool per la creazione di un account Batch, vedere la sezione [Account](batch-api-basics.md#account) in [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).

- Un **account di archiviazione di Azure**. Per creare un pool basato su Configurazione macchina virtuale usando un'immagine personalizzata, sono necessari uno o più account di archiviazione di Azure Standard per archiviare le immagini del disco rigido virtuale personalizzate. Le immagini personalizzate vengono archiviate come BLOB. Per fare riferimento alle immagini personalizzate quando si crea un pool, specificare gli URI dei BLOB dei dischi rigidi virtuali delle immagini personalizzate per la proprietà [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) della proprietà [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

    Assicurarsi che gli account di archiviazione soddisfino i criteri seguenti:   
    
    - Gli account di archiviazione contenenti i BLOB dei dischi rigidi virtuali delle immagini personalizzate devono trovarsi nella stessa sottoscrizione dell'account Batch (la sottoscrizione utente).
    - Gli account di archiviazione specificati devono trovarsi nella stessa regione dell'account Batch.
    - Attualmente sono supportati solo gli account di archiviazione Standard per utilizzo generico. Il supporto per Archiviazione Premium di Azure verrà aggiunto in futuro.
    - È possibile specificare un account di archiviazione con più BLOB dei dischi rigidi virtuali personalizzati o più account di archiviazione ognuno con un singolo BLOB. È consigliabile usare più account di archiviazione per ottenere prestazioni migliori.
    - Un BLOB del disco rigido virtuale di un'immagine personalizzata univoco può supportare fino a 40 istanze di VM Linux o 20 istanze di VM Windows. È possibile creare copie del BLOB del disco rigido virtuale per creare pool con più VM. Un pool con 200 VM Windows richiede ad esempio 10 BLOB dei dischi rigidi virtuali univoci specificati per la proprietà **osDisk**.
    
## <a name="prepare-a-custom-image"></a>Preparare un'immagine personalizzata

Per preparare un'immagine personalizzata da usare con Batch, è necessario generalizzare un'installazione esistente di Linux o Windows. La generalizzazione di un'installazione del sistema operativo comporta la rimozione delle informazioni specifiche del computer. Il risultato è un'immagine che può essere installata in altri computer o VM.  

È possibile usare un'immagine di Azure Marketplace come immagine di base per l'immagine personalizzata. Per personalizzare l'immagine di base, creare una VM di Azure e aggiungervi le applicazioni o i dati. Generalizzare quindi la VM da usare come immagine personalizzata.   

Per informazioni sulla preparazione di immagini Linux personalizzate da VM di Azure, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../virtual-machines/linux/capture-image.md). 

Per informazioni sulla preparazione di immagini Windows personalizzate da VM di Azure, vedere [Creare immagini di VM personalizzate con Azure PowerShell](../virtual-machines/windows/tutorial-custom-images.md) e [Sysprep (System Preparation) Overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Panoramica di Sysprep - System Preparation). 

> [!IMPORTANT]
> Durante la preparazione dell'immagine personalizzata, tenere presente quanto segue:
> - Assicurarsi che l'immagine del sistema operativo di base usata per il provisioning dei pool di Batch non includa alcuna estensione di Azure preinstallata, ad esempio l'estensione Script personalizzato. Se l'immagine contiene un'estensione preinstallata, è possibile che Azure rilevi problemi durante la distribuzione della VM.
> - Assicurarsi che l'immagine del sistema operativo di base fornita usi l'unità temporanea predefinita, perché l'agente del nodo Batch prevede attualmente l'unità temporanea predefinita.
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Per creare un pool da un'immagine personalizzata nel portale

Per creare un pool da un'immagine personalizzata tramite il portale di Azure:

1. Passare all'account Batch nel portale di Azure.
2. Nel pannello **Impostazioni** selezionare la voce di menu **Pool**.
3. Nel pannello **Pool** selezionare il comando **Aggiungi**. Verrà visualizzato il pannello **Aggiungi pool**.
4. Selezionare **Immagine personalizzata (Linux/Windows)** nell'elenco a discesa **Tipo di immagine**. Nel portale verrà visualizzato lo strumento di selezione **Immagine personalizzata**. Scegliere uno o più dischi rigidi virtuali dello stesso contenitore e fare clic sul pulsante **Seleziona**. 
   Il supporto per la selezione di dischi rigidi virtuali da account di archiviazione e contenitori diversi sarà disponibile in una delle prossime versioni.
5. Selezionare il valore appropriato per **Autore/Offerta/SKU** per i dischi rigidi virtuali personalizzati, selezionare la modalità **Caching** desiderata e quindi specificare tutti gli altri parametri per il pool.
6. Per verificare se un pool è basato su un'immagine personalizzata, vedere la proprietà **Sistema operativo** nella sezione di riepilogo delle risorse del pannello **Pool**. Il valore di questa proprietà deve essere **Immagine di macchina virtuale personalizzata**.
7. Tutti i dischi rigidi virtuali personalizzati associati a un pool vengono visualizzati nel pannello **Proprietà** del pool.
 
## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
- Per informazioni sulla creazione di un account Batch, vedere [Creare un account Batch nel portale di Azure](batch-account-create-portal.md).
