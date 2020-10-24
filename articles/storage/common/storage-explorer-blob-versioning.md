---
title: Guida al controllo delle versioni dei BLOB Azure Storage Explorer | Microsoft Docs
description: Linee guida sul controllo delle versioni dei BLOB per Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: 55fff7e4e36f066ba8ae5734e483df393fbd72cd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488657"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Guida al controllo delle versioni dei BLOB Azure Storage Explorer

Microsoft Azure Storage Explorer consente di accedere e gestire facilmente le versioni dei BLOB. Questa guida consente di comprendere il funzionamento del controllo delle versioni dei BLOB in Storage Explorer. Prima di continuare, è consigliabile leggere altre informazioni sul [controllo delle versioni dei BLOB](/azure/storage/blobs/versioning-overview).

## <a name="terminology"></a>Terminologia

In questa sezione vengono fornite alcune definizioni per comprendere l'utilizzo in questo articolo.

- Eliminazione temporanea: funzionalità di protezione automatica dei dati alternativa. Per altre informazioni sull'eliminazione temporanea, vedere [qui](/azure/storage/blobs/soft-delete-blob-overview).
- BLOB attivo: una versione BLOB o BLOB viene creata nello stato attivo. È possibile operare solo su BLOB o versioni BLOB nello stato attivo.
- BLOB eliminato temporaneamente: una versione BLOB o BLOB contrassegnata come eliminata temporaneamente. I BLOB eliminati temporaneamente vengono conservati solo per il periodo di memorizzazione.
- Versione BLOB: un blob creato con il controllo delle versioni dei BLOB abilitato. Ogni versione del BLOB è associata a un ID versione.
- Versione corrente: versione BLOB contrassegnata come versione corrente.
- Versione precedente: versione BLOB che non è la versione corrente.
- BLOB non di versione: un blob creato con il controllo delle versioni dei BLOB disabilitato. Un BLOB non di versione non dispone di un ID versione.

## <a name="view-blob-versions"></a>Visualizza versioni BLOB

Storage Explorer supporta quattro visualizzazioni diverse per la visualizzazione dei BLOB.

| Visualizzazione | BLOB non di versione attivi | BLOB non di versione eliminati temporaneamente | Versioni BLOB |
| ---- | :----------: | :-----------: | :------------------: |
| BLOB attivi | Sì | No | Solo versione corrente |
| BLOB attivi e BLOB eliminati temporaneamente | Sì | Sì | Solo versione corrente |
| BLOB e BLOB attivi senza versione corrente | Sì | No | Versione corrente o versione più recente attiva |
| Tutti i BLOB e i BLOB senza versione corrente | Sì | Sì | Versione corrente o versione più recente |

### <a name="active-blobs"></a>BLOB attivi

In questa visualizzazione Storage Explorer viene visualizzato:

- BLOB non di versione attivi
- Versioni correnti

### <a name="active-blobs-and-soft-deleted-blobs"></a>BLOB attivi e BLOB eliminati temporaneamente

In questa visualizzazione Storage Explorer viene visualizzato:

- BLOB non di versione attivi
- BLOB non di versione eliminati temporaneamente
- Versioni correnti.

### <a name="active-blobs-and-blobs-without-current-version"></a>BLOB e BLOB attivi senza versione corrente

In questa visualizzazione Storage Explorer viene visualizzato:

- BLOB non di versione attivi
- Versioni correnti
- Versioni precedenti attive più recenti. 

Per i BLOB che non dispongono di una versione corrente ma con una versione precedente attiva, Storage Explorer visualizza la versione precedente attiva più recente come rappresentazione di tale BLOB.

### <a name="all-blobs-and-blobs-without-current-version"></a>Tutti i BLOB e i BLOB senza versione corrente

In questa visualizzazione Storage Explorer viene visualizzato:

- BLOB non di versione attivi
- BLOB non di versione eliminati temporaneamente
- Versioni correnti
- Versioni precedenti più recenti. 

Per i BLOB che non dispongono di una versione corrente, Storage Explorer visualizza la versione precedente più recente come rappresentazione di tale BLOB.

> [!Note]
> A causa della limitazione del servizio, Storage Explorer necessita di un'elaborazione aggiuntiva per ottenere una visualizzazione gerarchica delle directory virtuali quando si elencano le versioni di BLOB. L'elenco dei BLOB verrà più lungo nei seguenti punti di vista:
> 
> - BLOB e BLOB attivi senza versione corrente
> - Tutti i BLOB e i BLOB senza versione corrente

## <a name="manage-blob-versions"></a>Gestire le versioni BLOB

### <a name="view-versions-of-a-blob"></a>Visualizzare le versioni di un BLOB

Storage Explorer fornisce un comando **Gestisci versioni** per visualizzare tutte le versioni di un BLOB. Per visualizzare le versioni di un BLOB, selezionare il BLOB per cui si vogliono visualizzare le versioni e selezionare **Gestisci cronologia &rarr; Gestisci versioni** dalla barra degli strumenti o dal menu di scelta rapida.

### <a name="download-blob-versions"></a>Scarica versioni BLOB

Per scaricare una o più versioni BLOB, selezionare le versioni dei BLOB da scaricare e selezionare **Scarica** dalla barra degli strumenti o dal menu di scelta rapida.

Se si scaricano più versioni di un BLOB, i file scaricati avranno gli ID versione all'inizio dei nomi file.

### <a name="delete-blob-versions"></a>Elimina versioni BLOB

Per eliminare una o più versioni BLOB, selezionare le versioni dei BLOB da eliminare e scegliere **Elimina** dalla barra degli strumenti o dal menu di scelta rapida.

Le versioni BLOB sono soggette ai criteri di eliminazione temporanea. Se l'eliminazione temporanea è abilitata, le versioni BLOB verranno eliminate temporaneamente. Un caso speciale è l'eliminazione di una versione corrente. L'eliminazione di una versione corrente la renderà automaticamente diventata una versione precedente attiva.

### <a name="promote-blob-version"></a>Alza di livello la versione BLOB

È possibile ripristinare il contenuto di un BLOB promuovendo una versione precedente in modo che diventi la versione corrente. Selezionare la versione del BLOB che si desidera alzare di livello e selezionare **alza versione** dalla barra degli strumenti o dal menu di scelta rapida.

I BLOB non di versione verranno sovrascritti dalla versione BLOB promossa. Assicurarsi che i dati non siano più necessari o eseguire il backup dei dati prima di confermare l'operazione. Le versioni correnti diventano automaticamente versioni precedenti, quindi Storage Explorer non richiederà alcuna conferma.

### <a name="undelete-blob-version"></a>Annulla l'eliminazione della versione BLOB

Non è possibile annullare l'eliminazione di versioni BLOB singolarmente. È necessario annullare l'eliminazione di tutti insieme. Per annullare l'eliminazione di tutte le versioni BLOB di un BLOB, selezionare una delle versioni del BLOB e selezionare **Annulla eliminazione selezionata** dalla barra degli strumenti o dal menu di scelta rapida.

### <a name="change-access-tier-of-blob-versions"></a>Modificare il livello di accesso delle versioni BLOB

Ogni versione del BLOB ha il proprio livello di accesso. Per modificare il livello di accesso delle versioni BLOB, selezionare le versioni dei BLOB per cui si vuole modificare il livello di accesso e selezionare **Cambia livello** di accesso dal menu di scelta rapida.

## <a name="see-also"></a>Vedere anche

* [Controllo delle versioni dei BLOB](/azure/storage/blobs/versioning-overview)
* [Eliminazione temporanea per i BLOB](/azure/storage/blobs/soft-delete-blob-overview)
