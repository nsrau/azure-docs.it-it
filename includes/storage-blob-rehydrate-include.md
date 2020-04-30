---
title: includere il file
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684102"
---
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come riattivazione e può richiedere ore per il completamento. Per ottenere prestazioni ottimali di riattivazione, è consigliabile usare dimensioni BLOB di grandi dimensioni La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo. Attualmente sono disponibili due priorità di reidratazione, High e standard, che possono essere impostate tramite la proprietà facoltativa *x-ms-reidrato-Priority* in un'operazione di [impostazione del livello BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) o di [copia del BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) .

* **Priorità standard**: la richiesta di reidratazione verrà elaborata nell'ordine in cui è stata ricevuta e potrebbe richiedere fino a 15 ore.
* **Priorità alta**: la richiesta di riattivazione verrà classificata in base alle richieste standard e potrebbe terminare tra 1 ora. La priorità alta potrebbe richiedere più di un'ora, a seconda della dimensione del BLOB e della richiesta corrente. Per le richieste con priorità alta è garantita la priorità sulle richieste di priorità standard.

> [!NOTE]
> La priorità standard è l'opzione di reidratazione predefinita per l'archivio. La priorità alta è un'opzione più veloce che costerà più della riattivazione della priorità standard ed è in genere riservata per l'uso in situazioni di ripristino dei dati di emergenza.

Una volta avviata una richiesta di reidratazione, non è possibile annullarla. Durante il processo di riattivazione, la proprietà del BLOB *x-MS-Access-Tier* continuerà a essere visualizzata come archivio fino al completamento della reidratazione in un livello online. Per confermare lo stato di riattivazione e lo stato di avanzamento, è possibile chiamare [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) per verificare le proprietà del BLOB x- *MS-Archive-status* e *x-ms-* reidratate-Priority. Lo stato dell'archivio può leggere "reidratare-in sospeso-a-caldo" o "reidratare-in sospeso-a-freddo" a seconda del livello di destinazione di reidratazione. La priorità di reidratazione indicherà la velocità di "High" o "standard". Al termine, le proprietà relative a stato di archiviazione e priorità di reidratazione vengono rimosse e la proprietà BLOB livello di accesso verrà aggiornata in modo da riflettere il livello attivo o sporadico selezionato.
