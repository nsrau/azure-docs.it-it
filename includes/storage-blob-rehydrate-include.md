---
title: File di inclusione
description: File di inclusione
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780242"
---
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come reidratazione e può richiedere ore per essere completato. È consigliabile utilizzare BLOB di grandi dimensioni per prestazioni di reidratazione ottimali. La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo. Esistono attualmente due priorità di reidratazione, Alta (anteprima) e Standard, che possono essere impostate tramite la proprietà facoltativa *x-ms-rehydrate-priority* in un'operazione [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) o Copy [Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Priorità standard**: La richiesta di reidratazione verrà elaborata nell'ordine in cui è stata ricevuta e potrebbe richiedere fino a 15 ore.
* **Priorità alta (anteprima):** la richiesta di reidratazione avrà la priorità sulle richieste Standard e potrebbe terminare in meno di 1 ora. La priorità alta può richiedere più di 1 ora, a seconda delle dimensioni del BLOB e della domanda corrente. È garantito che le richieste con priorità alta siano classificate in base alle priorità rispetto alle richieste di priorità Standard.High priority requests are guaranteed to be prioritid over Standard priority requests.

> [!NOTE]
> La priorità standard è l'opzione di reidratazione predefinita per l'archivio. La priorità alta è un'opzione più veloce che costa più della reidratazione con priorità Standard ed è solitamente riservata per l'utilizzo in situazioni di ripristino dei dati di emergenza.

Una volta avviata, una richiesta di reidratazione non può essere annullata. Durante la riattivazione è possibile controllare la proprietà BLOB *Archive Status* (Stato di archiviazione) per assicurarsi che il livello sia stato modificato. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà dello stato dell'archivio viene rimossa e la proprietà BLOB *del livello di accesso* riflette il nuovo livello attivo o interessante.
