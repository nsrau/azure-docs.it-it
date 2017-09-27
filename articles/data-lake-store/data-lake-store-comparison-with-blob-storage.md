---
title: Confronto tra Azure Data Lake Store e il BLOB del servizio di archiviazione di Azure | Microsoft Docs
description: Confronto tra Azure Data Lake Store e il BLOB del servizio di archiviazione di Azure
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 29f074f1ced5a3f4305d961a98f45e4a3b7792ef
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Confronto tra Azure Data Lake Store e l'archivio BLOB di Azure
Questo articolo propone una tabella di riepilogo delle differenze tra Azure Data Lake Store e l'archivio BLOB di Azure, insieme ad alcuni aspetti fondamentali dell'elaborazione dei Big Data. L'archivio BLOB di Azure è un archivio di oggetti generico e scalabile, progettato per un'ampia gamma di scenari di archiviazione. Azure Data Lake Store è un repository su vasta scala ottimizzato per carici di lavoro di analisi dei Big Data.

|  | Archivio Azure Data Lake | Archivio BLOB di Azure |
| --- | --- | --- |
| Scopo |Archiviazione ottimizzata per carichi di lavoro di analisi dei Big Data |Archivio di oggetti generico per un'ampia gamma di scenari di archiviazione |
| Casi di utilizzo |Dati batch, interattivi, di analisi di flusso e di apprendimento automatico come file di log, dati IoT, dati clickstream e set di dati di grandi dimensioni |Qualsiasi tipo di dati di testo o binari, come back-end di applicazioni, dati di backup, archiviazione di supporti per dati di streaming e generici |
| Concetti principali |Un account di Data Lake Store contiene cartelle, che a loro volta contengono dati archiviati come file |Un account di archiviazione include contenitori, che a loro volta contengono dati sotto forma di BLOB |
| Structure |File system gerarchico |Archivio di oggetti con spazio dei nomi flat |
| API |API REST su HTTPS |API REST su HTTP/HTTPS |
| API lato server |[API REST compatibile con WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[API REST dell'archivio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Client del file system Hadoop |Sì |Sì |
| Operazioni sui dati: autenticazione |Basata sulle [identità di Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) |Basata su segreti condivisi: [chiavi di accesso dell'account](../storage/common/storage-create-storage-account.md#manage-your-storage-account) e [chiavi di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operazioni sui dati: protocollo di autenticazione |OAuth 2.0. Le chiamate devono contenere un token JSON Web (JWT) valido rilasciato da Azure Active Directory. |Hash-based Message Authentication Code (HMAC). Le chiamate devono contenere un hash SHA-256 con codifica Base64 su una parte della richiesta HTTP. |
| Operazioni sui dati: autorizzazione |Elenchi di controllo di accesso (ACL) POSIX.  Gli elenchi di controllo di accesso basati sulle identità di Azure Active Directory possono essere impostati a livello di file e di cartelle. |Per l'autorizzazione a livello di account: usare [Chiavi di accesso dell'account](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>Per l'autorizzazione relativa all'account, al contenitore o al BLOB: usare [Chiavi di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operazioni sui dati: controllo |Disponibile. Per altre informazioni, fare clic [qui](data-lake-store-diagnostic-logs.md) . |Disponibile |
| Crittografia dei dati inattivi |<ul><li>Trasparente, lato server</li> <ul><li>Con chiavi gestite dal servizio</li><li>Con chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di Azure</li></ul></ul> |<ul><li>Trasparente, lato server</li> <ul><li>Con chiavi gestite dal servizio</li><li>Con chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di Azure (presto disponibile)</li></ul><li>Crittografia lato client</li></ul> |
| Operazioni di gestione, come la creazione di account |[Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) (RBAC) fornito da Azure per la gestione degli account. |[Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) (RBAC) fornito da Azure per la gestione degli account. |
| SDK per sviluppatori |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby |
| Prestazioni del carico di lavoro di analisi |Prestazioni ottimizzate per carichi di lavoro di analisi parallela. IOPS e velocità effettiva elevata. |Non è ottimizzato per carichi di lavoro di analisi. |
| Limiti di dimensioni |Nessun limite di dimensioni per l'account, i file o il numero di file |Limiti specifici documentati [qui](../azure-subscription-service-limits.md#storage-limits) |
| Ridondanza geografica |Archiviazione con ridondanza locale (più copie di dati in un'area di Azure). |Archiviazione con ridondanza locale, archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni, fare clic [qui](../storage/common/storage-redundancy.md) . |
| Stato del servizio |Disponibile a livello generale |Disponibile a livello generale |
| Disponibilità internazionale |Vedere [qui](https://azure.microsoft.com/regions/#services) |Vedere [qui](https://azure.microsoft.com/regions/#services) |
| Prezzo |Vedere i [prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/) |Vedere i [prezzi](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md)
* [Introduzione ad Archivio Data Lake](data-lake-store-get-started-portal.md)


