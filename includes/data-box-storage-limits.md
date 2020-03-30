---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 036e99a193d539bdcb204b5b062de261c30dffc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244563"
---
Questa sezione descrive i limiti relativi al servizio Archiviazione di Azure e le convenzioni di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabili al servizio Data Box. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Bloccare i BLOB e le convenzioni dei BLOB di pagineBlock blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se sono presenti file o directory che superano i limiti del servizio archiviazione di Azure o non sono conformi alle convenzioni di denominazione File/BLOB di Azure, questi file o directory non vengono inseriti in Archiviazione di Azure tramite il servizio Data Box.If there are any files or directories that exceed the Azure Storage service limits, or don't conform to Azure Files/Blob naming conventions, then these files or directories are't inested into the Azure Storage via the Data Box service.
