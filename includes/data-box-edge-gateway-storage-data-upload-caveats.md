---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333767"
---
Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente (ad esempio un oggetto BLOB o un file) nel cloud con lo stesso nome dell'oggetto copiato, il dispositivo sovrascriverà il file nel cloud.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.
- Per i file di grandi dimensioni, è consigliabile usare robocopy, che ritenta l'operazione di copia per gli errori intermittenti.
- Se la condivisione associata al contenitore di archiviazione di Azure consente di caricare BLOB che non corrispondono al tipo di BLOB definito per la condivisione al momento della creazione, questi BLOB non vengono aggiornati. Si supponga, ad esempio, di creare una condivisione di BLOB in blocchi nel dispositivo. Associare la condivisione a un contenitore cloud esistente in cui sono presenti BLOB di pagine. Aggiornare la condivisione per scaricare i file. Modificare alcuni dei file aggiornati già archiviati come BLOB di pagine nel cloud. Verranno visualizzati errori di caricamento.
