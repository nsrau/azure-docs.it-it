---
title: Esame dello stato del processo di importazione/esportazione di Azure - versione 1 | Documentazione Microsoft
description: Informazioni su come usare i file di log creati dal processo di importazione o esportazione per visualizzare lo stato del processo.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 7ef06bb9c5b5010f3fbbe413f98d77cc8519de00
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791801"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Esame dello stato del processo di Importazione/Esportazione di Azure con i file di log di copia
Quando il servizio Importazione/Esportazione di Microsoft Azure elabora le unità associate a un processo di importazione o esportazione, i file di log di copia vengono scritti nell'account di archiviazione usato per importare o esportare i BLOB. Il file di log contiene lo stato dettagliato di ogni file importato o esportato. Il servizio restituisce l'URL per ogni file di log di copia quando si esegue una query sullo stato di un processo completato. Per ulteriori informazioni, vedere [Get Job](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URL di esempio

Di seguito sono mostrati URL di esempio per i file di log di copia per un processo di importazione con due unità:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Vedere [formato del file di log del servizio importazione/esportazione](/previous-versions/azure/storage/common/storage-import-export-file-format-log) per il formato dei log di copia e l'elenco completo dei codici di stato.  

## <a name="next-steps"></a>Passaggi successivi

 * [Configurazione dello strumento di importazione/esportazione di Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparazione dei dischi rigidi per un processo di importazione](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import-v1)   
 * [Riparazione di un processo di importazione](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)