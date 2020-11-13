---
title: Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b670ec3cee2ebd1cc98ea2e04fb2b0dfd90e4e1a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594216"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2

Le funzionalità di archiviazione BLOB come la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità.

In questa tabella sono elencate le funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle verranno modificati nel corso del tempo man mano che il supporto continua ad espandersi. Per altre informazioni su problemi specifici associati allo stato di anteprima di una funzionalità, vedere l'articolo [Problemi noti](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

La tabella seguente illustra in che modo ogni funzionalità di archiviazione BLOB è supportata con Data Lake Storage Gen2. Esiste una colonna per i livelli di [prestazioni](premium-tier-for-data-lake-storage.md) standard e Premium. 

|Funzionalità di archiviazione BLOB |Standard |Premium |Articoli correlati |
|---------------|-------------------|---|
|Livello di accesso frequente|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Livello di accesso sporadico|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Eventi|Disponibile a livello generale|Disponibile a livello generale|[Reazione agli eventi di archiviazione BLOB](storage-blob-event-overview.md)|
|Metriche (versione classica)|Disponibile a livello generale|Disponibile a livello generale|[Metriche di Analisi archiviazione di Azure (versione classica)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriche in Monitoraggio di Azure|Disponibile a livello generale|Anteprima|[Metriche di Archiviazione di Azure in Monitoraggio di Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandi PowerShell per l'archiviazione BLOB|Disponibile a livello generale|Disponibile a livello generale|[Avvio rapido: Caricare, scaricare ed elencare BLOB con PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandi dell'interfaccia della riga di comando di Azure per Archiviazione BLOB|Disponibile a livello generale|Disponibile a livello generale|[Avvio rapido: Creare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure](storage-quickstart-blobs-cli.md)|
|Api di archiviazione BLOB|Disponibile a livello generale|Disponibile a livello generale|[Avvio rapido: Libreria client di Archiviazione BLOB di Azure v12 per .NET](storage-quickstart-blobs-dotnet.md)<br>[Avvio rapido: Gestire i BLOB con Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Avvio rapido: Gestire i BLOB con Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Avvio rapido: Gestire i BLOB con JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Log di diagnostica|Disponibile a livello generale|Anteprima |[Registrazione di Analisi di archiviazione di Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Livello di accesso archivio|Disponibile a livello generale|Non supportate|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Criteri di gestione del ciclo di vita (suddivisione in livelli)|Disponibile a livello generale|Non ancora supportato|[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)|
|Criteri di gestione del ciclo di vita (eliminazione di BLOB)|Disponibile a livello generale|Disponibile a livello generale|[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)|
|Registrazione in Monitoraggio di Azure|Anteprima |Anteprima|[Monitoraggio di archiviazione di Azure](../common/monitor-storage.md)|
|Snapshot|Anteprima|Anteprima|[Snapshot BLOB](snapshots-overview.md)|
|Siti Web statici|Anteprima|Anteprima|[Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)|
|Archiviazione non modificabile|Anteprima|Anteprima|[Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md)|
|Eliminazione temporanea del contenitore|Anteprima|Anteprima|[Eliminazione temporanea per i contenitori (anteprima)](soft-delete-container-overview.md)|
|Eliminazione temporanea BLOB|Non ancora supportato|Non ancora supportato|[Eliminazione temporanea per i BLOB](storage-blob-soft-delete.md)|
|Blobfuse|Disponibile a livello generale|Disponibile a livello generale|[Come montare l'archivio BLOB come file system con blobfuse](storage-how-to-mount-container-linux.md)|
|Failover dell'account|Non ancora supportato|Non ancora supportato|[Ripristino di emergenza e failover dell'account](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Elenco di controllo di accesso del contenitore BLOB|Non supportato<div role="complementary" aria-labelledby="blob-container-ACL"><sup>1</sup></div>|Non supportato<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Vedere la nota correlata sotto questa tabella.|
|Chiavi fornite dal cliente|Non ancora supportato|Non ancora supportato|[Fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](encryption-customer-provided-keys.md)|
|Domini personalizzati|Non ancora supportato|Non ancora supportato|[Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md)|
|Ambiti di crittografia|Non ancora supportato|Non ancora supportato|[Creare e gestire gli ambiti di crittografia (anteprima)](encryption-scope-manage.md)|
|Feed delle modifiche|Non ancora supportato|Non ancora supportato|[Supporto del feed di modifiche in Archiviazione BLOB di Azure](storage-blob-change-feed.md)|
|Replica di oggetti|Non ancora supportato|Non ancora supportato|[Configurare la replica di oggetti per i BLOB in blocchi](object-replication-configure.md)|
|Controllo delle versioni dei BLOB|Non ancora supportato|Non ancora supportato|[Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)|

<div id="blob-container-ACL"><sup>1</sup> è possibile impostare gli ACL nella cartella radice del contenitore, ma non nel contenitore stesso.</div><br>

<div id="preview-form"><sup>2</sup> Per usare gli snapshot, l'archiviazione non modificabile o i siti web statici con Data Lake Storage Gen2, è necessario iscriversi all'anteprima completando il <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>modulo</a>.  </div>

## <a name="see-also"></a>Vedere anche

- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
