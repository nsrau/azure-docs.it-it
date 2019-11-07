---
title: Ripristino del contenitore dell'account di archiviazione
description: Ripristino del contenitore dell'account di archiviazione
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693579"
---
# <a name="storage-account-container-recovery"></a>Ripristino del contenitore dell'account di archiviazione

Archiviazione di Azure offre la resilienza dei dati tramite repliche automatizzate. Ciò non impedisce tuttavia al codice delle applicazioni o agli utenti di danneggiare i dati, intenzionalmente o accidentalmente. Per mantenere la conformità dei dati in caso di errore dell'applicazione o da parte dell'utente sono richieste tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo.

## <a name="checking-azure-storage-account-type"></a>Verifica del tipo di account di archiviazione di Azure

1. Passare al [portale di Azure](https://portal.azure.com/).

2. Individuare l'account di archiviazione.

3. Nella sezione **Panoramica** , controllare la **replica**.

   ![Image](media/storage-account-container-recovery/1.png)

4. Se il tipo di replica è **GRS/RA-GRS**, il ripristino del contenitore dell'account è possibile senza garanzie. Per tutti gli altri tipi di replica, non è possibile.

5. Raccogliere le informazioni seguenti e archiviare una richiesta di supporto con supporto tecnico Microsoft.

   * Nome dell'account di archiviazione:
   * Nome contenitore:
   * Ora dell'eliminazione:

   La tabella seguente fornisce una panoramica dell'ambito del ripristino del contenitore dell'account di archiviazione a seconda della strategia di replica.

   |Tipo di contenuto|Archiviazione con ridondanza locale|ZRS|Archiviazione con ridondanza geografica|RA-GRS| 
   |---|---|---|---|---|
   |Contenitore di archiviazione|No|No|Sì|Sì| 

   * È possibile tentare di ripristinare il contenitore dell'account di archiviazione, ma senza alcuna garanzia. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Operazioni da eseguire per il ripristino non riuscito

* Non ricreare il contenitore con lo stesso nome.  
* Se il contenitore è già stato ricreato, è necessario eliminare il contenitore prima di presentare una richiesta di supporto per il ripristino.

## <a name="steps-to-prevent-this-in-the-future"></a>Passaggi per evitare questo problema in futuro

1. Per evitare questo problema in futuro, la funzionalità più consigliata da usare è l' [eliminazione](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)temporanea.

2. Si consiglia inoltre di usare la funzionalità [snapshot](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) .
 
## <a name="next-steps"></a>Passaggi successivi

Ecco due codici di esempio sulla funzionalità:

  * [Creare e gestire uno snapshot BLOB in .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Uso di snapshot BLOB con PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

