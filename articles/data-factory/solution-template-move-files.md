---
title: Spostare i file tra l'archiviazione basata su file
description: Informazioni su come usare un modello di soluzione per spostare i file tra l'archiviazione basata su file usando Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: dc842ba0e7ca0f34b7dacb98322c4dc0cd056483
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931995"
---
# <a name="move-files-with-azure-data-factory"></a>Spostare i file con Azure Data Factory

Questo articolo descrive un modello di soluzione che è possibile usare per spostare i file da una cartella a un'altra tra archivi basati su file. Uno degli scenari comuni di utilizzo di questo modello: i file vengono continuamente rilasciati in una cartella di destinazione dell'archivio di origine. Tramite la creazione di un trigger di pianificazione, la pipeline ADF può spostare periodicamente tali file dall'origine all'archivio di destinazione.  Il modo in cui la pipeline di ADF ottiene "spostando i file" sta ricevendo i file dalla cartella di destinazione, copiando ognuno di essi in un'altra cartella nell'archivio di destinazione e quindi eliminando gli stessi file dalla cartella di destinazione nell'archivio di origine.

> [!NOTE]
> Tenere presente che questo modello è progettato per spostare i file anziché spostare le cartelle.  Se si vuole spostare la cartella cambiando il set di dati in modo che contenga solo un percorso di cartella e quindi usando l'attività di copia e l'attività di eliminazione per fare riferimento allo stesso set di dati che rappresenta una cartella, è necessario prestare molta attenzione. Il motivo è che è necessario assicurarsi che NON arrivino nuovi file nella cartella tra l'operazione di copia e quella di eliminazione. Se nuovi file arrivano nella cartella proprio quando l'attività Copy ha completato il processo di copia, ma l'attività Delete non è ancora stata avviata, è possibile che l'attività Delete elimini il file appena arrivato che NON è stato ancora copiato nella destinazione eliminando l'intera cartella.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello consente di ottenere i file dall'archivio basato su file di origine. Quindi sposta ciascuno di essi nell'archivio di destinazione.

Il modello contiene cinque attività:
- **GetMetadata** Ottiene l'elenco di oggetti che includono i file e le sottocartelle della cartella nell'archivio di origine. Gli oggetti non vengono recuperati in modo ricorsivo. 
- Filtrare **filtrare l'** elenco oggetti dall'attività **GetMetadata** per selezionare solo i file. 
- **Foreach** Ottiene l'elenco dei file dall'attività di **filtro** e quindi scorre l'elenco e passa ogni file all'attività di copia ed Elimina l'attività.
- **Copia copia** un file dall'origine all'archivio di destinazione.
- **Delete** Elimina lo stesso file dall'archivio di origine.

Il modello definisce due parametri:
- *FolderPath_SourceStore* è il percorso della cartella dell'archivio di origine da cui si desidera spostare i file. 
- *FolderPath_DestinationStore* è il percorso della cartella dell'archivio di destinazione in cui si desidera spostare i file. 

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **Move files** . Selezionare connessione esistente o creare una **nuova** connessione all'archivio file di origine da cui si desidera spostare i file. Tenere presente che **DataSource_Folder** e **DataSource_File** sono riferimenti alla stessa connessione dell'archivio file di origine.

    ![Creare una nuova connessione all'origine](media/solution-template-move-files/move-files1.png)

2. Selezionare connessione esistente o creare una **nuova** connessione all'archivio file di destinazione in cui si desidera spostare i file.

    ![Creare una nuova connessione alla destinazione](media/solution-template-move-files/move-files2.png)

3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-move-files/move-files3.png)
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-move-files/move-files4.png)

5. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.   I parametri corrispondono al percorso della cartella da cui si desidera spostare i file e al percorso della cartella in cui si desidera spostare i file. 

    ![Eseguire la pipeline](media/solution-template-move-files/move-files5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Copia i file nuovi e modificati da LastModifiedDate con Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Copia i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)