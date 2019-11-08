---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799912"
---
- Non copiare i file direttamente in una delle condivisioni PreCreate. È necessario creare una cartella nella condivisione e quindi copiare i file in tale cartella.
- Una cartella in *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* è un contenitore. I contenitori, ad esempio, vengono creati come *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Ogni cartella creata direttamente in *StorageAccount_AzureFiles* viene convertita in una condivisione file di Azure.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, Data Box sovrascriverà il file nel cloud.
- Ogni file scritto nelle condivisioni *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* viene caricato rispettivamente come BLOB in blocchi e BLOB di pagine.
- L'archivio BLOB di Azure non supporta le directory. Se si crea una cartella nella cartella *StorageAccount_BlockBlob*, vengono create cartelle virtuali nel nome del BLOB. Per File di Azure, viene mantenuta la struttura di directory effettiva.
- Qualsiasi gerarchia di directory vuota (senza file) creata in *StorageAccount_BlockBlob* e le cartelle di *StorageAccount_PageBlob* non vengono caricate.
- Se si verificano errori durante il caricamento dei dati in Azure, viene creato un log degli errori nell'account di archiviazione di destinazione. Il percorso del log degli errori è disponibile quando il caricamento è completo ed è possibile esaminare il log per eseguire azioni correttive. Non eliminare i dati dall'origine senza verificare i dati caricati.
- I metadati di file e le autorizzazioni NTFS non vengono conservati quando i dati vengono caricati in File di Azure. Ad esempio, l' *ultimo attributo modificato* dei file non verrà mantenuto quando i dati vengono copiati.
