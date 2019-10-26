---
title: Accesso a più protocolli su Azure Data Lake Storage (anteprima) | Microsoft Docs
description: Usare le API BLOB e le applicazioni che usano le API BLOB con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6e3b368d1f5615c34a49b155d288d3c37c28bc26
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933128"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Accesso a più protocolli su Azure Data Lake Storage (anteprima)

Le API blob ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Questo sblocca l'intero ecosistema di strumenti, applicazioni e servizi, nonché tutte le funzionalità di archiviazione BLOB per gli account che hanno uno spazio dei nomi gerarchico.

Fino a poco tempo fa, potrebbe essere necessario mantenere soluzioni di archiviazione separate per l'archiviazione di oggetti e l'archiviazione di analisi. Questo perché Azure Data Lake Storage Gen2 aveva un supporto limitato per l'ecosistema. Ha anche accesso limitato alle funzionalità del servizio BLOB, ad esempio la registrazione diagnostica. Una soluzione di archiviazione frammentata è difficile da gestire perché è necessario spostare i dati tra gli account per realizzare diversi scenari. Questa operazione non è più necessario.

> [!NOTE]
> L'accesso a più protocolli su Data Lake Storage è disponibile in anteprima pubblica ed è disponibile in tutte le aree geografiche. Non è necessario registrarsi nell'anteprima pubblica perché è automaticamente disponibile per tutti gli account che hanno uno spazio dei nomi gerarchico. Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) .

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Usare l'intero ecosistema di applicazioni, strumenti e servizi

Con l'accesso a più protocolli su Data Lake Storage, è possibile usare tutti i dati usando l'intero ecosistema di strumenti, applicazioni e servizi. Sono inclusi i servizi di Azure, ad esempio [analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [Hub](https://docs.microsoft.com/azure/iot-hub/)Internet, [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)e molti altri. Per un elenco completo, vedere [integrare Azure Data Lake storage con i servizi di Azure](data-lake-store-integrate-with-azure-services.md).

Sono inclusi anche strumenti e applicazioni di terze parti. È possibile indirizzarli agli account che dispongono di uno spazio dei nomi gerarchico senza doverli modificare. Queste applicazioni funzionano *così come sono* anche se chiamano API blob, perché le API BLOB possono ora operare sui dati negli account che hanno uno spazio dei nomi gerarchico.

> [!NOTE]
> Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Usare tutte le funzionalità di archiviazione BLOB

Le funzionalità di archiviazione BLOB come la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità importanti. 

> [!NOTE]
> Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funzionamento dell'accesso con più protocolli in data Lake storage

Le API BLOB e le API Data Lake Storage Gen2 possono operare sugli stessi dati negli account di archiviazione che hanno uno spazio dei nomi gerarchico. Data Lake Storage Gen2 instrada le API BLOB tramite lo spazio dei nomi gerarchico per poter ottenere i vantaggi delle operazioni di directory di prima classe e degli elenchi di controllo di accesso (ACL) conformi a POSIX. 

![Accesso a più protocolli su Data Lake Storage concettuale](./media/data-lake-storage-interop/interop-concept.png) 

Gli strumenti e le applicazioni esistenti che usano l'API blob ottengono automaticamente questi vantaggi. Gli sviluppatori non dovranno modificarli. Data Lake Storage Gen2 applica in modo coerente le directory e gli ACL a livello di file indipendentemente dal protocollo usato da strumenti e applicazioni per accedere ai dati. 

## <a name="next-steps"></a>Passaggi successivi

Vedere i [problemi noti](data-lake-storage-known-issues.md)




