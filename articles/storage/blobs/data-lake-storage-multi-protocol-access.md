---
title: Accesso multi-protocollo in Archiviazione data lake di Azure Documenti Microsoft
description: Use Blob APIs and applications that use Blob APIs with Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914859"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Accesso multi-protocollo in Azure Data Lake Storage

Le API BLOB ora funzionano con gli account con uno spazio dei nomi gerarchico. In questo modo si sblocca l'ecosistema di strumenti, applicazioni e servizi, nonché diverse funzionalità di archiviazione BLOB per gli account con uno spazio dei nomi gerarchico.

Fino a poco tempo fa, potrebbe essere stato necessario mantenere soluzioni di archiviazione separate per l'archiviazione degli oggetti e l'archiviazione di analisi. Questo perché Azure Data Lake Storage Gen2 aveva un supporto limitato per l'ecosistema. Aveva anche accesso limitato alle funzionalità del servizio BLOB, ad esempio la registrazione diagnostica. Una soluzione di archiviazione frammentata è difficile da gestire perché è necessario spostare i dati tra gli account per eseguire vari scenari. Non devi più farlo.

Con l'accesso multiprotocollo su Data Lake Storage, è possibile lavorare con i dati utilizzando l'ecosistema di strumenti, applicazioni e servizi. Questo include anche strumenti e applicazioni di terze parti. È possibile puntare a account che hanno uno spazio dei nomi gerarchico senza doverli modificare. Queste applicazioni funzionano *allo stesso* modo anche se chiamano API BLOB, perché le API BLOB possono ora operare sui dati negli account con uno spazio dei nomi gerarchico.

Le funzionalità di archiviazione BLOB, ad esempio [la registrazione diagnostica,](../common/storage-analytics-logging.md) [i livelli](storage-blob-storage-tiers.md)di accesso e i criteri di gestione del ciclo di vita dell'archiviazione [BLOB,](storage-lifecycle-management-concepts.md) ora funzionano con gli account con uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste importanti funzionalità. 

> [!NOTE]
> L'accesso multiprotocollo su Data Lake Storage è generalmente disponibile ed è disponibile in tutte le aree geografiche. Alcuni servizi di Azure o funzionalità di archiviazione BLOB abilitati dall'accesso multiprotocollo rimangono in anteprima.  Questi articoli riepilogano il supporto corrente per le funzionalità di archiviazione BLOB e le integrazioni del servizio di Azure.These articles summarize the current support for Blob storage features and Azure service integrations. 
>
> [Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funzionamento dell'accesso multiprotocollo all'archiviazione di data lake

Le API BLOB e le API Gen2 di Data Lake Storage possono operare sugli stessi dati negli account di archiviazione con uno spazio dei nomi gerarchico. Data Lake Storage Gen2 indirizza le API BLOB attraverso lo spazio dei nomi gerarchico in modo da poter ottenere i vantaggi delle operazioni di directory di prima classe e degli elenchi di controllo di accesso (ACL) conformi a POSIX. 

![Accesso multiprotocollo su Data Lake Storage concettuale](./media/data-lake-storage-interop/interop-concept.png) 

Gli strumenti e le applicazioni esistenti che usano l'API BLOB ottengono automaticamente questi vantaggi. Gli sviluppatori non dovranno modificarli. Data Lake Storage Gen2 applica in modo coerente gli ACL a livello di directory e di file, indipendentemente dal protocollo utilizzato dagli strumenti e dalle applicazioni per accedere ai dati. 

## <a name="see-also"></a>Vedere anche

- [Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemi noti con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




