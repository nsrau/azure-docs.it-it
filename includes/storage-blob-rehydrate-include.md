---
title: File di inclusione
description: File di inclusione
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684102"
---
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come reidratazione e può richiedere ore per essere completato. È consigliabile utilizzare BLOB di grandi dimensioni per prestazioni di reidratazione ottimali. La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo. Esistono attualmente due priorità di reidratazione, High e Standard, che possono essere impostate tramite la proprietà facoltativa *x-ms-rehydrate-priority* in un'operazione [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) o Copy [Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Priorità standard**: La richiesta di reidratazione verrà elaborata nell'ordine in cui è stata ricevuta e potrebbe richiedere fino a 15 ore.
* **Priorità alta**: La richiesta di reidratazione avrà la priorità rispetto alle richieste Standard e potrebbe finire in meno di 1 ora. La priorità alta può richiedere più di 1 ora, a seconda delle dimensioni del BLOB e della domanda corrente. È garantito che le richieste con priorità alta siano classificate in base alle priorità rispetto alle richieste di priorità Standard.High priority requests are guaranteed to be prioritid over Standard priority requests.

> [!NOTE]
> La priorità standard è l'opzione di reidratazione predefinita per l'archivio. La priorità alta è un'opzione più veloce che costa più della reidratazione con priorità Standard ed è solitamente riservata per l'utilizzo in situazioni di ripristino dei dati di emergenza.

Una volta avviata, una richiesta di reidratazione non può essere annullata. Durante il processo di reidratazione, la proprietà BLOB *x-ms-access-tier* continuerà a essere visualizzata come archivio fino al completamento della riattivazione a un livello online. Per verificare lo stato e lo stato di riattivazione, è possibile chiamare [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) per controllare le proprietà *x-ms-archive-status* e *x-ms-rehydrate-priority* BLOB. Lo stato dell'archivio può essere letto "reidratare,in attesa di essere caldo" o "reidratare-in attesa di raffreddare" a seconda del livello di destinazione reidratato. La priorità di reidratazione indicherà la velocità di "Alto" o "Standard". Al termine, lo stato dell'archivio e le proprietà di priorità reidratati vengono rimossi e la proprietà BLOB del livello di accesso verrà aggiornata per riflettere il livello attivo o freddo selezionato.
