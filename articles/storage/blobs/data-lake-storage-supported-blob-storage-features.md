---
title: Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d25a44aa70b9638017856595c7431e897d035c33
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576116"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2

Le funzionalità di archiviazione BLOB, ad esempio [registrazione diagnostica](../common/storage-analytics-logging.md), [livelli di accesso](storage-blob-storage-tiers.md) e  [criteri di gestione del ciclo di vita dell'archiviazione BLOB](storage-lifecycle-management-concepts.md), ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità.

In questa tabella sono elencate le funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle verranno modificati nel corso del tempo man mano che il supporto continua ad espandersi. Per altre informazioni su problemi specifici associati allo stato di anteprima di una funzionalità, vedere l'articolo [Problemi noti](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui la funzionalità è supportata con Data Lake Storage Gen2. 
>
> [Gli account BlockBlobStorage per le prestazioni Premium](storage-blob-create-account-block-blob.md) per Data Lake Storage Gen2 sono attualmente in anteprima pubblica. I livelli di supporto per questi tipi di account vengono visualizzati nella colonna **BlockBlobStorage (Premium)** .

|Funzionalità di archiviazione BLOB |Utilizzo generico v2 |BlockBlobStorage (Premium) |Articoli correlati |
|---------------|-------------------|---|
|Livello di accesso frequente|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Livello di accesso sporadico|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Eventi|Disponibile a livello generale|Anteprima|[Reazione agli eventi di archiviazione BLOB](storage-blob-event-overview.md)|
|Metriche (versione classica)|Disponibile a livello generale|Non supportate|[Metriche di Analisi archiviazione di Azure (versione classica)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriche in Monitoraggio di Azure|Disponibile a livello generale|Anteprima|[Metriche di Archiviazione di Azure in Monitoraggio di Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandi PowerShell per l'archiviazione BLOB|Disponibile a livello generale|Anteprima|[Avvio rapido: Caricare, scaricare ed elencare BLOB con PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandi dell'interfaccia della riga di comando di Azure per Archiviazione BLOB|Disponibile a livello generale|Anteprima|[Avvio rapido: Creare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure](storage-quickstart-blobs-cli.md)|
|Api di archiviazione BLOB|Disponibile a livello generale|Anteprima|[Avvio rapido: Libreria client di Archiviazione BLOB di Azure v12 per .NET](storage-quickstart-blobs-dotnet.md)<br>[Avvio rapido: Gestire i BLOB con Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Avvio rapido: Gestire i BLOB con Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Avvio rapido: Gestire i BLOB con JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Log di diagnostica|Disponibile a livello generale|Anteprima <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Registrazione di Analisi di archiviazione di Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Livello di accesso archivio|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Criteri di gestione del ciclo di vita|Disponibile a livello generale|Non ancora supportato|[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)|
|Registrazione in Monitoraggio di Azure|Anteprima |Non ancora supportato|[Monitoraggio di archiviazione di Azure](../common/monitor-storage.md)|
|Snapshot|Anteprima|Non ancora supportato|[Snapshot BLOB](snapshots-overview.md)|
|Siti Web statici|Anteprima|Non ancora supportato|[Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)|
|Archiviazione non modificabile|Anteprima|Non ancora supportato|[Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md)|
|Eliminazione temporanea del contenitore|Anteprima|Anteprima|[Eliminazione temporanea per i contenitori (anteprima)](soft-delete-container-overview.md)|
|Eliminazione temporanea BLOB|Non ancora supportato|Non ancora supportato|[Eliminazione temporanea per i BLOB](storage-blob-soft-delete.md)|
|Blobfuse|Anteprima|Non ancora supportato|[Come montare l'archivio BLOB come file system con blobfuse](storage-how-to-mount-container-linux.md)|
|Failover dell'account|Non ancora supportato|Non ancora supportato|[Ripristino di emergenza e failover dell'account](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Elenco di controllo di accesso del contenitore BLOB|Non ancora supportato<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Non ancora supportato<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Chiavi gestite dal cliente|Non ancora supportato|Non ancora supportato|[Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md)|
|Domini personalizzati|Non ancora supportato|Non ancora supportato|[Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md)|
|Ambiti di crittografia|Non ancora supportato|Non ancora supportato|[Creare e gestire gli ambiti di crittografia (anteprima)](encryption-scope-manage.md)|
|Feed delle modifiche|Non ancora supportato|Non ancora supportato|[Supporto del feed di modifiche in Archiviazione BLOB di Azure](storage-blob-change-feed.md)|
|Replica di oggetti|Non ancora supportato|Non ancora supportato|[Configurare la replica di oggetti per i BLOB in blocchi (anteprima)](object-replication-configure.md)|
|Controllo delle versioni dei BLOB|Non ancora supportato|Non ancora supportato|[Abilitare e gestire il controllo delle versioni dei BLOB (anteprima)](versioning-enable.md)|

<div id="diagnostic-logging"><sup>1</sup> per gli account di archiviazione BLOB in blocchi Premium, i log di diagnostica (versione classica) non possono essere abilitati tramite il portale di Azure. È possibile abilitarli tramite PowerShell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> è possibile impostare gli ACL nella cartella radice del contenitore, ma non nel contenitore stesso.</div><br>

<div id="preview-form"><sup>3</sup> Per usare gli snapshot, l'archiviazione non modificabile o i siti web statici con Data Lake Storage Gen2, è necessario iscriversi all'anteprima completando il <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>modulo</a>.  </div>

## <a name="see-also"></a>Vedere anche

- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
