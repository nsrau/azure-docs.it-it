---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161300"
---
Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, il dispositivo sovrascriverà il file nel cloud.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.
- È possibile copiare i dati mediante trascinamento e rilascio con Esplora File o dalla riga di comando. Se le dimensioni aggregate dei file da copiare sono maggiore di 10 GB, è consigliabile che usare un programma di copia bulk, ad esempio Robocopy o rsync. Gli strumenti di copia bulk ripetere l'operazione di copia per gli errori intermittenti e garantiscono una maggiore resilienza.
- Se la condivisione associata al contenitore di archiviazione di Azure consente di caricare BLOB che non corrispondono al tipo di BLOB definito per la condivisione al momento della creazione, questi BLOB non vengono aggiornati. Si supponga, ad esempio, di creare una condivisione di BLOB in blocchi nel dispositivo. Associare la condivisione a un contenitore cloud esistente in cui sono presenti BLOB di pagine. Aggiornare la condivisione per scaricare i file. Modificare alcuni dei file aggiornati già archiviati come BLOB di pagine nel cloud. Verranno visualizzati errori di caricamento.
