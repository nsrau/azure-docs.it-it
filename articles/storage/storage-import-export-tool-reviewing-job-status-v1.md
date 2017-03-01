---
title: Esame dello stato del processo di Importazione/Esportazione di Azure | Documentazione Microsoft
description: "Informazioni su come utilizzare i file di log creati quando il processo di importazione o esportazione è stato eseguito per visualizzare lo stato del processo di importazione/esportazione."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: f0d445e51ff91f6edc219522ec3c12a86dc4ee98
ms.lasthandoff: 02/16/2017


---

# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Esame dello stato del processo di Importazione/Esportazione di Azure con i file di log di copia
Quando il servizio Importazione/Esportazione di Microsoft Azure elabora unità associate a un processo di importazione o esportazione, scrive file di log di copia per l'account di archiviazione verso cui o da cui si importano o si esportano BLOB. Il file di log contiene lo stato dettagliato di ogni file importato o esportato. L'URL ad ogni file di log di copia viene restituito quando si esegue una query sullo stato di un processo completato. Per ulteriori informazioni, vedere [Get Job](/rest/api/storageservices`Get-Job3).  
  
 Di seguito sono mostrati URL di esempio per i file di log di copia per un processo di importazione con due unità:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Per informazioni sul formato dei log di copia e l'elenco completo di codici di stato, vedere [Formato dei file di log per il servizio di importazione/esportazione](storage-import-export-file-format-log.md).  
  
## <a name="see-also"></a>Vedere anche  
 [Configurazione dello strumento Importazione/Esportazione di Azure](storage-import-export-tool-setup-v1.md)   
 [Preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [Ripristino di un processo di importazione](storage-import-export-tool-repairing-an-import-job-v1.md)   
 [Ripristino di un processo di esportazione](storage-import-export-tool-repairing-an-export-job-v1.md)   
 [Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure](storage-import-export-tool-troubleshooting-v1.md)

