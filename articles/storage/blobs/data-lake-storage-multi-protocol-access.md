---
title: Accesso a più protocolli su Azure Data Lake Storage | Microsoft Docs
description: Usare le API BLOB e le applicazioni che usano le API BLOB con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: aebd7b58a2107ac109a03d3ce12f27d65ba0ab90
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769813"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Accesso a più protocolli su Azure Data Lake Storage

Le API blob ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Questo sblocca l'ecosistema di strumenti, applicazioni e servizi, nonché diverse funzionalità di archiviazione BLOB per gli account che hanno uno spazio dei nomi gerarchico.

Fino a poco tempo fa, potrebbe essere necessario mantenere soluzioni di archiviazione separate per l'archiviazione di oggetti e l'archiviazione di analisi. Questo perché Azure Data Lake Storage Gen2 aveva un supporto limitato per l'ecosistema. Ha anche accesso limitato alle funzionalità del servizio BLOB, ad esempio la registrazione diagnostica. Una soluzione di archiviazione frammentata è difficile da gestire perché è necessario spostare i dati tra gli account per realizzare diversi scenari. Questa operazione non è più necessario.

Con l'accesso a più protocolli su Data Lake Storage, è possibile utilizzare i dati utilizzando l'ecosistema di strumenti, applicazioni e servizi. Sono inclusi anche strumenti e applicazioni di terze parti. È possibile indirizzarli agli account che dispongono di uno spazio dei nomi gerarchico senza doverli modificare. Queste applicazioni funzionano *così come sono* anche se chiamano API blob, perché le API BLOB possono ora operare sui dati negli account che hanno uno spazio dei nomi gerarchico.

Le funzionalità di archiviazione BLOB come la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità importanti. 

> [!NOTE]
> L'accesso a più protocolli su Data Lake Storage è disponibile a livello generale ed è disponibile in tutte le aree geografiche. Alcune funzionalità di archiviazione BLOB e servizi di Azure abilitate per l'accesso a più protocolli rimangono in anteprima. Per ulteriori informazioni, vedere le tabelle in ogni sezione di questo articolo. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funzionamento dell'accesso con più protocolli in data Lake storage

Le API BLOB e le API Data Lake Storage Gen2 possono operare sugli stessi dati negli account di archiviazione che hanno uno spazio dei nomi gerarchico. Data Lake Storage Gen2 instrada le API BLOB tramite lo spazio dei nomi gerarchico per poter ottenere i vantaggi delle operazioni di directory di prima classe e degli elenchi di controllo di accesso (ACL) conformi a POSIX. 

![Accesso a più protocolli su Data Lake Storage concettuale](./media/data-lake-storage-interop/interop-concept.png) 

Gli strumenti e le applicazioni esistenti che usano l'API blob ottengono automaticamente questi vantaggi. Gli sviluppatori non dovranno modificarli. Data Lake Storage Gen2 applica in modo coerente le directory e gli ACL a livello di file indipendentemente dal protocollo usato da strumenti e applicazioni per accedere ai dati. 

## <a name="blob-storage-feature-support"></a>Supporto delle funzionalità di archiviazione BLOB

L'accesso a più protocolli su Data Lake Storage consente di usare più funzionalità di archiviazione BLOB con le Data Lake Storage. Questa tabella elenca le funzionalità abilitate dall'accesso a più protocolli su Data Lake Storage. 

Gli elementi visualizzati in questa tabella cambieranno nel tempo in quanto il supporto per le funzionalità di archiviazione BLOB continuerà ad espandersi. 

> [!NOTE]
> Anche se l'accesso a più protocolli su Data Lake Storage è disponibile a livello generale, il supporto per alcune di queste funzionalità rimane in anteprima. 

|Funzionalità di archiviazione BLOB | Livello di supporto |
|---|---|
|[Livello accesso sporadico](storage-blob-storage-tiers.md)|Disponibile a livello generale|
|API REST BLOB|Disponibile a livello generale|
|SDK per BLOB |Disponibile a livello generale|
|[PowerShell (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Disponibile a livello generale|
|[INTERFACCIA della riga di comando (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Disponibile a livello generale|
|SDK di BLOB con semantica di file system ([.net](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|Preview|
|[PowerShell con semantica di file system](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|Preview|
|[INTERFACCIA della riga di comando con semantica di file system](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|Preview|
|[Log di diagnostica](../common/storage-analytics-logging.md)| Preview|
|[Criteri di gestione del ciclo di vita](storage-lifecycle-management-concepts.md)| Preview|
|[Notifiche tramite griglia di eventi di Azure](data-lake-storage-events.md)|Preview|
|[Livello di accesso archivio](storage-blob-storage-tiers.md)| Preview|
|[blobfuse](storage-how-to-mount-container-linux.md)|Non ancora supportato|
|[Archiviazione non modificabile](storage-blob-immutable-storage.md)|Non ancora supportato|
|[Snapshot](storage-blob-snapshots.md)|Non ancora supportato|
|[Eliminazione temporanea](storage-blob-soft-delete.md)|Non ancora supportato|
|[Siti web statici](storage-blob-static-website.md)|Non ancora supportato|

Per ulteriori informazioni sui problemi noti e sulle limitazioni generali con Azure Data Lake Storage Gen2, vedere [problemi noti](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Supporto ecosistema di Azure

L'accesso a più protocolli su Data Lake Storage consente anche di connettere più servizi di Azure con la Data Lake Storage. Questa tabella elenca i servizi abilitati per l'accesso a più protocolli su Data Lake Storage. 

Analogamente all'elenco delle funzionalità di archiviazione BLOB supportate, gli elementi visualizzati in questa tabella cambiano nel tempo in quanto il supporto per i servizi di Azure continua ad espandersi. 

> [!NOTE]
> Anche se l'accesso a più protocolli su Data Lake Storage è disponibile a livello generale, il supporto per alcuni di questi servizi rimane in anteprima. 

|Servizio di Azure | Livello di supporto |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Disponibile a livello generale|
|[Acquisizione di hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Disponibile a livello generale|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Disponibile a livello generale|
|[Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Disponibile a livello generale|
|[App per la logica](https://azure.microsoft.com/services/logic-apps/)|Disponibile a livello generale|
|[ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Preview|

Per l'elenco completo del supporto ecosistema di Azure per Data Lake Storage Gen2, vedere [integrare Azure Data Lake storage con i servizi di Azure](data-lake-storage-integrate-with-azure-services.md).

Per ulteriori informazioni sui problemi noti e sulle limitazioni generali con Azure Data Lake Storage Gen2, vedere [problemi noti](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere i [problemi noti](data-lake-storage-known-issues.md)




