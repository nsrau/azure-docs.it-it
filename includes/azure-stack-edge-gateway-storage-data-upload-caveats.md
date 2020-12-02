---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467612"
---
Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, il dispositivo sovrascriverà il file nel cloud.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.
- È possibile copiare i dati tramite il trascinamento della selezione con Esplora file o tramite la riga di comando. Se le dimensioni di aggregazione dei file copiati sono maggiori di 10 GB, è consigliabile usare un programma di copia bulk, ad esempio Robocopy o rsync. Gli strumenti per la copia bulk ritentano l'operazione di copia per gli errori intermittenti e forniscono resilienza aggiuntiva. Se si usa l'archiviazione BLOB tramite REST, è possibile usare AzCopy o Azure Storage Explorer.
- Se la condivisione associata al contenitore di archiviazione di Azure consente di caricare BLOB che non corrispondono al tipo di BLOB definito per la condivisione al momento della creazione, questi BLOB non vengono aggiornati. Si supponga, ad esempio, di creare una condivisione di BLOB in blocchi nel dispositivo. Associare la condivisione a un contenitore cloud esistente in cui sono presenti BLOB di pagine. Aggiornare la condivisione per scaricare i file. Modificare alcuni dei file aggiornati già archiviati come BLOB di pagine nel cloud. Verranno visualizzati errori di caricamento.
- Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo.
- Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.
- Se si usa rsync per copiare i dati, l' `rsync -a` opzione non è supportata.
