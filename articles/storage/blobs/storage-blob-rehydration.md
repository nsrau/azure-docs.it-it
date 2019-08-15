---
title: Reidratare i dati BLOB dal livello archivio
description: Riattivare i BLOB dalla risorsa di archiviazione dell'archivio per poter accedere ai dati.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958213"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratare i dati BLOB dal livello archivio

Mentre un BLOB si trova nel livello di accesso archivio, viene considerato offline e non può essere letto o modificato. I metadati del BLOB rimangono online e disponibili, consentendo di elencare il BLOB e le relative proprietà. La lettura e la modifica dei dati BLOB sono disponibili solo con i livelli online, ad esempio Hot o cool. Sono disponibili due opzioni per recuperare e accedere ai dati archiviati nel livello di accesso archivio.

1. [Reidratare un BLOB archiviato in un livello online](#rehydrate-an-archived-blob-to-an-online-tier) : riattivare un BLOB archiviato in accesso frequente o sporadico modificando il relativo livello usando l'operazione di [impostazione del livello BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Copiare un BLOB archiviato in un livello online](#copy-an-archived-blob-to-an-online-tier) : creare una nuova copia di un BLOB archiviato usando l'operazione [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Specificare un nome di BLOB diverso e un livello di destinazione ad accesso frequente o sporadico.

 Per altre informazioni sui livelli, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratare un BLOB archiviato in un livello online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiare un BLOB archiviato in un livello online

Se non si vuole riattivare un BLOB, è possibile scegliere un'operazione di [copia del BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Il BLOB originale rimarrà invariato nell'archivio mentre si lavora sul nuovo BLOB nel livello ad accesso frequente o sporadico. Quando si usa il processo di copia, è possibile impostare la proprietà facoltativa *x-ms-reidrato-Priority* su standard o High (anteprima).

I BLOB di archiviazione possono essere copiati solo nei livelli di destinazione online. La copia di un BLOB di archiviazione in un altro BLOB di archiviazione non è supportata.

La copia di un BLOB dall'archivio richiede tempo. Dietro le quinte, l'operazione **Copy Blob** riattiva temporaneamente il BLOB di origine dell'archivio per creare un nuovo BLOB online nel livello di destinazione. Questo nuovo BLOB non è disponibile fino a quando non viene completata la riattivazione temporanea dell'archivio e i dati vengono scritti nel nuovo BLOB.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La reidratazione dei BLOB fuori dall'archivio in livelli ad accesso frequente o sporadico viene addebitata come operazioni di lettura e recupero dati. L'uso della priorità alta (anteprima) prevede costi operativi e di recupero dei dati più elevati rispetto alla priorità standard. La riattivazione con priorità alta viene visualizzata come una voce separata nella fattura. Se una richiesta con priorità alta per restituire un BLOB di archiviazione di pochi gigabyte richiede più di 5 ore, non verrà addebitata la tariffa per il recupero ad alta priorità. Tuttavia, vengono comunque applicate le tariffe di recupero standard.

La copia di BLOB dall'archivio in livelli ad accesso frequente o sporadico viene addebitata come operazioni di lettura e recupero dati. Un'operazione di scrittura viene addebitata per la creazione della nuova copia. Le tariffe per l'eliminazione anticipata non si applicano quando si esegue la copia in un BLOB online perché il BLOB di origine rimane invariato nel livello archivio. Vengono applicati addebiti con priorità alta.

I BLOB nel livello archivio devono essere archiviati per almeno 180 giorni. Se si eliminano o si riattivano i BLOB archiviati prima di 180 giorni, le tariffe per l'eliminazione anticipata

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi e la riattivazione dei dati, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Per ulteriori informazioni sugli addebiti per il trasferimento di dati in uscita, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Fasi successive

* [Informazioni sui livelli di archiviazione BLOB](storage-blob-storage-tiers.md)
* [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
* [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
