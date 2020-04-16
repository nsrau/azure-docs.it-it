---
title: Spostare i file tra l'archiviazione basata su file
description: Informazioni su come usare un modello di soluzione per spostare file tra archiviazione basata su file tramite Azure Data Factory.Learn how to use a solution template to move files between file-based storage by using Azure Data Factory.
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
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414753"
---
# <a name="move-files-with-azure-data-factory"></a>Spostare file con Azure Data FactoryMove files with Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene descritto un modello di soluzione che è possibile utilizzare per spostare i file da una cartella a un'altra tra archivi basati su file. Uno degli scenari comuni di utilizzo di questo modello: i file vengono continuamente rilasciati in una cartella di destinazione dell'archivio di origine. Creando un trigger di pianificazione, la pipeline ADF può spostare periodicamente i file dall'archivio di origine all'archivio di destinazione.  Il modo in cui la pipeline ADF raggiunge "spostamento di file" è ottenere i file dalla cartella di destinazione, copiare ognuno di essi in un'altra cartella nell'archivio di destinazione e quindi eliminare gli stessi file dalla cartella di destinazione nell'archivio di origine.

> [!NOTE]
> Tenere presente che questo modello è progettato per spostare i file anziché le cartelle.  Se si desidera spostare la cartella modificando il set di dati in modo che contenga solo un percorso di cartella e quindi utilizzando l'attività di copia e l'attività di eliminazione per fare riferimento allo stesso set di dati che rappresenta una cartella, è necessario prestare molta attenzione. Il motivo è che è necessario assicurarsi che NON arrivino nuovi file nella cartella tra l'operazione di copia e quella di eliminazione. Se nuovi file arrivano nella cartella proprio quando l'attività Copy ha completato il processo di copia, ma l'attività Delete non è ancora stata avviata, è possibile che l'attività Delete elimini il file appena arrivato che NON è stato ancora copiato nella destinazione eliminando l'intera cartella.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello ottiene i file dall'archivio basato su file di origine. Quindi sposta ognuno di essi nell'archivio di destinazione.

Il modello contiene cinque attività:
- **GetMetadata** ottiene l'elenco di oggetti inclusi i file e le sottocartelle dalla cartella nell'archivio di origine. Non recupererà gli oggetti in modo ricorsivo. 
- **Filtrare** l'elenco di oggetti dall'attività **GetMetadata** per selezionare solo i file. 
- **ForEach** ottiene l'elenco di file dall'attività **Filter** e quindi scorre l'elenco e passa ogni file all'attività Copy e Delete.
- **Copia** copia un file dall'origine all'archivio di destinazione.
- **Elimina** elimina lo stesso file dall'archivio di origine.

Il modello definisce quattro parametri:
- *SourceStore_Location* è il percorso della cartella dell'archivio di origine da cui si desidera spostare i file. 
- *SourceStore_Directory* è il percorso della sottocartella dell'archivio di origine da cui si desidera spostare i file.
- *DestinationStore_Location* è il percorso della cartella dell'archivio di destinazione in cui si desidera spostare i file. 
- *DestinationStore_Directory* è il percorso delle sottocartelle dell'archivio di destinazione in cui si desidera spostare i file.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **Sposta file.** Selezionare una connessione esistente o creare una **nuova** connessione all'archivio file di origine da cui si desidera spostare i file. Tenere presente che **DataSource_Folder** e **DataSource_File** fanno riferimento alla stessa connessione dell'archivio file di origine.

    ![Creare una nuova connessione all'origine](media/solution-template-move-files/move-files1.png)

2. Selezionare una connessione esistente o creare una **nuova** connessione all'archivio file di destinazione in cui si desidera spostare i file.

    ![Creare una nuova connessione alla destinazione](media/solution-template-move-files/move-files2.png)

3. Selezionare Usa la scheda **del modello.**
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:You'll see the pipeline, as in the following example:

    ![Mostra la pipeline](media/solution-template-move-files/move-files4.png)

5. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.   I parametri sono il percorso della cartella da cui si desidera spostare i file e il percorso della cartella in cui si desidera spostare i file. 

    ![Eseguire la pipeline](media/solution-template-move-files/move-files5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Copiare i file nuovi e modificati di LastModifiedDate con Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)