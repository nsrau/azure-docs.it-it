---
title: Accesso a più protocolli su Azure Data Lake Storage | Microsoft Docs
description: Usare le API BLOB e le applicazioni che usano le API BLOB con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cc0191a9484a09ed12e0ca0cde4d51681e44ec5f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855547"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Accesso a più protocolli su Azure Data Lake Storage

Le API blob ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Questo sblocca l'intero ecosistema di strumenti, applicazioni e servizi, nonché tutte le funzionalità di archiviazione BLOB per gli account che hanno uno spazio dei nomi gerarchico.

Fino a poco tempo fa, potrebbe essere necessario mantenere soluzioni di archiviazione separate per l'archiviazione di oggetti e l'archiviazione di analisi. Questo perché Azure Data Lake Storage Gen2 aveva un supporto limitato per l'ecosistema. Ha anche accesso limitato alle funzionalità del servizio BLOB, ad esempio la registrazione diagnostica. Una soluzione di archiviazione frammentata è difficile da gestire perché è necessario spostare i dati tra gli account per realizzare diversi scenari. Questa operazione non è più necessario.

> [!NOTE]
> L'accesso a più protocolli su Data Lake Storage è disponibile in anteprima pubblica ed è disponibile solo nelle aree **Stati Uniti occidentali 2** e **Stati Uniti centro-occidentali** . Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) . Per eseguire la registrazione nell'anteprima, vedere [Questa pagina](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Usare l'intero ecosistema di applicazioni, strumenti e servizi

Se si esegue la registrazione nell'anteprima dell'accesso con più protocolli su Data Lake Storage, è possibile usare tutti i dati usando l'intero ecosistema di strumenti, applicazioni e servizi. Sono inclusi i servizi di Azure, ad esempio [analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [Hub](https://docs.microsoft.com/azure/iot-hub/)Internet, [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)e molti altri. 

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




