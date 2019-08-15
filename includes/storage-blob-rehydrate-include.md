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
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952917"
---
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come riattivazione e può richiedere ore per il completamento. Per ottenere prestazioni ottimali di riattivazione, è consigliabile usare dimensioni BLOB di grandi dimensioni La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo. Attualmente sono disponibili due priorità di reidratazione, High (Preview) e standard, che possono essere impostate tramite la proprietà facoltativa *x-ms-reidratate-Priority* in un'operazione di [impostazione del livello BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) o di [copia del BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) .

* **Priorità standard**: La richiesta di reidratazione verrà elaborata nell'ordine in cui è stata ricevuta e potrebbe richiedere fino a 15 ore.
* **Priorità alta (anteprima)** : La richiesta di reidratazione verrà classificata in base alle richieste standard e potrà terminare tra 1 ora. La priorità alta potrebbe richiedere più di un'ora, a seconda della dimensione del BLOB e della richiesta corrente. Per le richieste con priorità alta è garantita la priorità sulle richieste di priorità standard.

> [!NOTE]
> La priorità standard è l'opzione di reidratazione predefinita per l'archivio. La priorità alta è un'opzione più veloce che costerà più della riattivazione della priorità standard ed è in genere riservata per l'uso in situazioni di ripristino dei dati di emergenza.

Durante la riattivazione è possibile controllare la proprietà BLOB *Archive Status* (Stato di archiviazione) per assicurarsi che il livello sia stato modificato. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB *Livello di accesso* indica un il nuovo livello frequente o sporadico.
