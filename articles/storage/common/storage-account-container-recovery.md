---
title: Ripristino del contenitore dell'account di archiviazioneStorage Account Container Recovery
description: Ripristino del contenitore dell'account di archiviazioneStorage Account Container Recovery
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562276"
---
# <a name="storage-account-container-recovery"></a>Ripristino del contenitore dell'account di archiviazione

Archiviazione di Azure offre la resilienza dei dati tramite repliche automatizzate. Ciò non impedisce tuttavia al codice delle applicazioni o agli utenti di danneggiare i dati, intenzionalmente o accidentalmente. Per mantenere la conformità dei dati in caso di errore dell'applicazione o da parte dell'utente sono richieste tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo.

## <a name="checking-azure-storage-account-type"></a>Controllo del tipo di account di Archiviazione di AzureChecking Azure Storage account type

1. Passare al [portale di Azure](https://portal.azure.com/).

2. Individuare l'account di archiviazione.

3. Nella sezione **Panoramica** verificare la **presenza di replica**.

   ![Immagine](media/storage-account-container-recovery/1.png)

4. Se il tipo di replica è **GRS/RA-GRS**, il ripristino del contenitore di account è possibile senza garanzia. Per tutti gli altri tipi di replica, non è possibile.

5. Raccogliere le seguenti informazioni e presentare una richiesta di supporto con il supporto tecnico Microsoft.

   * Nome account di archiviazione:
   * Nome contenitore:
   * Ora di eliminazione:

   Nella tabella seguente viene fornita una panoramica dell'ambito del ripristino del contenitore degli account di archiviazione in base alla strategia di replica.

   |Tipo di contenuto|Archiviazione con ridondanza locale|ZRS|Archiviazione con ridondanza geografica|RA - GRS| 
   |---|---|---|---|---|
   |Contenitore di archiviazione|No|No|Sì|Sì| 

   * Possiamo tentare di ripristinare il contenitore dell'account di archiviazione, ma senza alcuna garanzia. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Cose da non fare per recuperare per avere successo

* Si prega di non ricreare il contenitore (con lo stesso nome).  
* Se il contenitore è già stato ricreato, è necessario eliminarlo prima di archiviare una richiesta di supporto per il ripristino.

## <a name="steps-to-prevent-this-in-the-future"></a>Misure per prevenire questo in futuro

1. Per evitare questo in futuro, la caratteristica più consigliata da utilizzare è [Soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Si consiglia anche la funzione [Istantanea.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati due codici di esempio sulla funzionalità:

  * [Creare e gestire uno snapshot BLOB in .NETCreate and manage a blob snapshot in .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Uso di snapshot BLOB con PowerShellUsing blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

