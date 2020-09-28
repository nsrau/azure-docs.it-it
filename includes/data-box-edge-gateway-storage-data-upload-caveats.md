---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400939"
---
Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, il dispositivo sovrascriverà il file nel cloud.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.
- È possibile copiare i dati tramite il trascinamento della selezione con Esplora file o tramite la riga di comando. Se le dimensioni aggregate dei file copiati sono maggiori di 10 GB, è consigliabile utilizzare un programma di copia bulk, ad esempio Robocopy o rsync. Gli strumenti per la copia bulk ritentano l'operazione di copia per errori intermittenti e forniscono resilienza aggiuntiva.
- Se la condivisione associata al contenitore di archiviazione di Azure consente di caricare BLOB che non corrispondono al tipo di BLOB definito per la condivisione al momento della creazione, questi BLOB non vengono aggiornati. Si supponga, ad esempio, di creare una condivisione di BLOB in blocchi nel dispositivo. Associare la condivisione a un contenitore cloud esistente in cui sono presenti BLOB di pagine. Aggiornare la condivisione per scaricare i file. Modificare alcuni dei file aggiornati già archiviati come BLOB di pagine nel cloud. Verranno visualizzati errori di caricamento.
- Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo.
- Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.
- Se si usa rsync per copiare i dati, l' `rsync -a` opzione non è supportata.

