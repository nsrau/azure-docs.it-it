---
title: Creare trigger basati su eventi in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in risposta a un evento.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 457983021034d83e0eed05bd91eae1ac30c046da
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296878"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Creare un trigger che esegue una pipeline in risposta a un evento

Questo articolo descrive i trigger basati su eventi che è possibile creare nelle pipeline di Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Negli scenari di integrazione dei dati è spesso necessario che i clienti di Data Factory attivino pipeline in base agli eventi.

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

### <a name="create-a-new-event-trigger"></a>Creare un nuovo trigger di evento

Un tipico evento è costituito dall'arrivo di un file o dall'eliminazione di un file nell'account di archiviazione di Azure. È possibile creare un trigger che risponde a questo evento nella pipeline di Data Factory.

![Creare un nuovo trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Selezionare il tipo di trigger di evento

Non appena il file arriva nel percorso di archiviazione e viene creato il BLOB corrispondente, questo evento attiva ed esegue la pipeline di Data Factory. È possibile creare un trigger che risponde a un evento di creazione di BLOB, a un evento di eliminazione di BLOB oppure a entrambi nelle pipeline di Data Factory.

![Selezionare il tipo di trigger come evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Configurare il trigger di evento

Con le proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con** è possibile specificare i contenitori, le cartelle e i nomi di BLOB per cui si vuole ricevere eventi. È possibile usare svariati modelli per le due proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con**, come mostrato negli esempi più avanti in questo articolo. Almeno una di queste due proprietà è obbligatoria.

![Configurare il trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>Schema JSON

La tabella seguente offre una panoramica degli elementi dello schema correlati ai trigger basati su elenchi:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID risorsa di Azure Resource Manager dell'account di archiviazione. | string | ID Azure Resource Manager | Sì |
| **eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sì, qualsiasi combinazione. |
| **blobPathBeginsWith** | Il percorso del BLOB deve iniziare con il modello fornito per l'attivazione del trigger. Ad esempio, '/records/blobs/december/' attiva solo il trigger per i BLOB nella cartella december all'interno del contenitore records. | string   | | È necessario specificare almeno una di queste proprietà: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Il percorso del BLOB deve terminare con il modello fornito per l'attivazione del trigger. Ad esempio, 'december/boxes.csv' attiva solo il trigger per i BLOB denominati boxes in una cartella december. | string   | | È necessario specificare almeno una di queste proprietà: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Esempi di trigger basati su eventi

Questa sezione contiene alcuni esempi di impostazioni di trigger basati su eventi.

-   **Percorso BLOB inizia con**('/nomecontenitore/'): riceve gli eventi per qualsiasi BLOB nel contenitore.
-   **Percorso BLOB inizia con**('/nomecontenitore/nomecartella'): riceve gli eventi per qualsiasi BLOB nel contenitore nomecontenitore e nella cartella nomecartella.
-   **Percorso BLOB inizia con**('/nomecontenitore/nomecartella/file.txt'): riceve gli eventi per un BLOB denominato file.txt nella cartella nomecartella all'interno del contenitore nomecontenitore.
-   **Percorso BLOB termina con**('file.txt'): riceve gli eventi per un BLOB denominato file.txt in qualsiasi percorso.
-   **Percorso BLOB termina con**('/nomecontenitore/file.txt'): riceve gli eventi per un BLOB denominato file.txt nel contenitore nomecontenitore.
-   **Percorso BLOB termina con**('nomecartella/file.txt'): riceve gli eventi per un BLOB denominato file.txt nella cartella nomecartella all'interno di qualsiasi contenitore.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#triggers).
