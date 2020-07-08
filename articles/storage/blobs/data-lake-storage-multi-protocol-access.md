---
title: Accesso a più protocolli su Azure Data Lake Storage | Microsoft Docs
description: Usare le API BLOB e le applicazioni che usano le API BLOB con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77914859"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Accesso multi-protocollo in Azure Data Lake Storage

Le API blob ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Questo sblocca l'ecosistema di strumenti, applicazioni e servizi, nonché diverse funzionalità di archiviazione BLOB per gli account che hanno uno spazio dei nomi gerarchico.

Fino a poco tempo fa, potrebbe essere necessario mantenere soluzioni di archiviazione separate per l'archiviazione di oggetti e l'archiviazione di analisi. Questo perché Azure Data Lake Storage Gen2 aveva un supporto limitato per l'ecosistema. Ha anche accesso limitato alle funzionalità del servizio BLOB, ad esempio la registrazione diagnostica. Una soluzione di archiviazione frammentata è difficile da gestire perché è necessario spostare i dati tra gli account per realizzare diversi scenari. Questa operazione non è più necessario.

Con l'accesso a più protocolli su Data Lake Storage, è possibile utilizzare i dati utilizzando l'ecosistema di strumenti, applicazioni e servizi. Sono inclusi anche strumenti e applicazioni di terze parti. È possibile indirizzarli agli account che dispongono di uno spazio dei nomi gerarchico senza doverli modificare. Queste applicazioni funzionano *così come sono* anche se chiamano API blob, perché le API BLOB possono ora operare sui dati negli account che hanno uno spazio dei nomi gerarchico.

Le funzionalità di archiviazione BLOB come la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità importanti. 

> [!NOTE]
> L'accesso a più protocolli su Data Lake Storage è disponibile a livello generale ed è disponibile in tutte le aree geografiche. Alcune funzionalità di archiviazione BLOB e servizi di Azure abilitate per l'accesso a più protocolli rimangono in anteprima.  Questi articoli riepilogano il supporto corrente per le funzionalità di archiviazione BLOB e le integrazioni dei servizi di Azure. 
>
> [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funzionamento dell'accesso con più protocolli in data Lake storage

Le API BLOB e le API Data Lake Storage Gen2 possono operare sugli stessi dati negli account di archiviazione che hanno uno spazio dei nomi gerarchico. Data Lake Storage Gen2 instrada le API BLOB tramite lo spazio dei nomi gerarchico per poter ottenere i vantaggi delle operazioni di directory di prima classe e degli elenchi di controllo di accesso (ACL) conformi a POSIX. 

![Accesso a più protocolli su Data Lake Storage concettuale](./media/data-lake-storage-interop/interop-concept.png) 

Gli strumenti e le applicazioni esistenti che usano l'API blob ottengono automaticamente questi vantaggi. Gli sviluppatori non dovranno modificarli. Data Lake Storage Gen2 applica in modo coerente le directory e gli ACL a livello di file indipendentemente dal protocollo usato da strumenti e applicazioni per accedere ai dati. 

## <a name="see-also"></a>Vedere anche

- [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




