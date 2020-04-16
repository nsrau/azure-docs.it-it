---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522819"
---
## <a name="select-an-ingestion-type"></a>Selezionare un tipo di inserimento

In **Ingestion type** (Tipo di inserimento) selezionare una delle opzioni seguenti:
   * **from storage** (da account di archiviazione): nel campo **Link to storage** (Collega ad account di archiviazione) aggiungere l'URL dell'account di archiviazione. Usare [URL di firma di accesso condiviso del BLOB](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) per gli account di archiviazione privati.
   
      ![Inserimento con un clic dall'account di archiviazione](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **from file** (da file): selezionare **Sfoglia** per individuare il file oppure trascinare il file nel campo.
  
      ![Inserimento con un clic dal file](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **from container** (da contenitore): nel campo **Link to storage** (Collega all'account di archiviazione) aggiungere l'[URL della firma di accesso condiviso](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) del contenitore e facoltativamente immettere le dimensioni del campione.

      ![Inserimento con un clic dal contenitore](media/data-explorer-one-click-ingestion-types/from-container.png)

  Viene visualizzato un campione dei dati. Se si vuole, è possibile filtrarlo in modo da visualizzare solo i file che iniziano con caratteri specifici. Quando si modificano i filtri, l'anteprima viene aggiornata automaticamente.
  
  Ad esempio, è possibile filtrare tutti i file che iniziano con la parola *data* e terminano con un'estensione *.csv.gz*.

  ![Filtro per l'inserimento con un clic](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
