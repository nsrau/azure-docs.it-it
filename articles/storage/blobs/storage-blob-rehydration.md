---
title: Reidratare i dati BLOB dal livello archivio
description: Riattivare i BLOB dalla risorsa di archiviazione dell'archivio per poter accedere ai dati.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113718"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratare i dati BLOB dal livello archivio

Mentre un BLOB si trova nel livello di accesso archivio, viene considerato offline e non può essere letto o modificato. I metadati del BLOB rimangono online e disponibili, consentendo di elencare il BLOB e le relative proprietà. La lettura e la modifica dei dati BLOB sono disponibili solo con i livelli online, ad esempio Hot o cool. Sono disponibili due opzioni per recuperare e accedere ai dati archiviati nel livello di accesso archivio.

1. [Reidratare un BLOB archiviato in un livello online](#rehydrate-an-archived-blob-to-an-online-tier) : riattivare un BLOB di archiviazione ad accesso frequente o sporadico modificando il relativo livello usando l'operazione di [impostazione del livello BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Copiare un BLOB archiviato in un livello online](#copy-an-archived-blob-to-an-online-tier) : creare una nuova copia di un BLOB di archiviazione usando l'operazione [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Specificare un nome di BLOB diverso e un livello di destinazione ad accesso frequente o sporadico.

 Per altre informazioni sui livelli, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratare un BLOB archiviato in un livello online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiare un BLOB archiviato in un livello online

Se non si vuole riattivare il BLOB di archiviazione, è possibile scegliere di eseguire un'operazione di [copia del BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Il BLOB originale rimarrà invariato nell'archivio mentre viene creato un nuovo BLOB nel livello di accesso frequente o ad accesso sporadico in linea. Nell'operazione copy BLOB, è anche possibile impostare la proprietà facoltativa *x-ms-reidratate-Priority* su standard o High (Preview) per specificare la priorità in base alla quale si vuole creare la copia BLOB.

I BLOB di archiviazione possono essere copiati solo nei livelli di destinazione online all'interno dello stesso account di archiviazione. La copia di un BLOB di archiviazione in un altro BLOB di archiviazione non è supportata.

Il completamento della copia di un BLOB dall'archivio può richiedere ore, a seconda della priorità di reidratazione selezionata. Dietro le quinte, l'operazione **Copy Blob** legge il BLOB di origine dell'archivio per creare un nuovo BLOB online nel livello di destinazione selezionato. Il nuovo BLOB può essere visibile quando si elencano i BLOB, ma i dati non sono disponibili finché non viene completata la lettura dal BLOB di archiviazione di origine e i dati vengono scritti nel nuovo BLOB di destinazione online. Il nuovo BLOB è una copia indipendente e qualsiasi modifica o eliminazione non influisce sul BLOB di archiviazione di origine.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La reidratazione dei BLOB fuori dall'archivio in livelli ad accesso frequente o sporadico viene addebitata come operazioni di lettura e recupero dati. L'uso della priorità alta (anteprima) prevede costi operativi e di recupero dei dati più elevati rispetto alla priorità standard. La riattivazione con priorità alta viene visualizzata come una voce separata nella fattura. Se una richiesta con priorità alta per restituire un BLOB di archiviazione di pochi gigabyte richiede più di 5 ore, non verrà addebitata la tariffa per il recupero con priorità alta. Tuttavia, le tariffe di recupero standard si applicano anche quando la riattivazione è stata classificata in ordine di priorità su altre richieste.

La copia di BLOB dall'archivio in livelli ad accesso frequente o sporadico viene addebitata come operazioni di lettura e recupero dati. Per la creazione della nuova copia BLOB viene addebitata un'operazione di scrittura. Le tariffe per l'eliminazione anticipata non si applicano quando si esegue la copia in un BLOB online perché il BLOB di origine rimane invariato nel livello archivio. Se selezionato, verranno applicati addebiti per il recupero con priorità alta.

I BLOB nel livello archivio devono essere archiviati per almeno 180 giorni. Se si eliminano o si riattivano i BLOB archiviati prima di 180 giorni, le tariffe per l'eliminazione anticipata

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi e la riattivazione dei dati, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Per ulteriori informazioni sugli addebiti per il trasferimento di dati in uscita, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui livelli di archiviazione BLOB](storage-blob-storage-tiers.md)
* [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
* [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
