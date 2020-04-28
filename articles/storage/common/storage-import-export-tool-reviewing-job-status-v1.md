---
title: Esame dello stato del processo di importazione/esportazione di Azure - versione 1 | Documentazione Microsoft
description: Informazioni su come usare i file di log creati quando il processo di importazione o esportazione è stato eseguito per visualizzare lo stato del processo di importazione/esportazione.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978443"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Esame dello stato del processo di Importazione/Esportazione di Azure con i file di log di copia
Quando il servizio Importazione/Esportazione di Microsoft Azure elabora unità associate a un processo di importazione o esportazione, scrive file di log di copia per l'account di archiviazione verso cui o da cui si importano o si esportano BLOB. Il file di log contiene lo stato dettagliato di ogni file importato o esportato. L'URL ad ogni file di log di copia viene restituito quando si esegue una query sullo stato di un processo completato. Per ulteriori informazioni, vedere [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URL di esempio

Di seguito sono mostrati URL di esempio per i file di log di copia per un processo di importazione con due unità:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Vedere [formato del file di log del servizio importazione/esportazione](../storage-import-export-file-format-log.md) per il formato dei log di copia e l'elenco completo dei codici di stato.  

## <a name="next-steps"></a>Passaggi successivi

 * [Configurazione dello strumento di importazione/esportazione di Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparazione dei dischi rigidi per un processo di importazione](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Ripristino di un processo di importazione](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Repairing an export job](../storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)   
 * [Risoluzione dei problemi relativi allo strumento di importazione/esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)
