---
title: Confronto tra Azure Data Lake Storage Gen1 con archiviazione BLOB
description: Informazioni sulle differenze tra Azure Data Lake Storage Gen1 e l'archiviazione BLOB di Azure per alcuni aspetti chiave dell'elaborazione Big Data.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 170f20cbd3405ea6af8aef5650e4dd7ebeaeef7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458213"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Confronto tra Azure Data Lake Storage Gen1 e Archiviazione BLOB di Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Questo articolo propone una tabella di riepilogo delle differenze tra Azure Data Lake Storage Gen1 e Archiviazione BLOB di Azure, insieme ad alcuni aspetti fondamentali dell'elaborazione dei Big Data. L'archivio BLOB di Azure è un archivio di oggetti generico e scalabile, progettato per un'ampia gamma di scenari di archiviazione. Azure Data Lake Storage Gen1 è un repository su vasta scala ottimizzato per carichi di lavoro di analisi dei Big Data.

| Category | Azure Data Lake Storage Gen1 | Archiviazione BLOB di Azure |
| -------- | ---------------------------- | ------------------ |
| Scopo |Archiviazione ottimizzata per carichi di lavoro di analisi dei Big Data |Archivio di oggetti generico per un'ampia gamma di scenari di archiviazione, tra cui analisi per Big Data. |
| Casi d'uso |Dati batch, interattivi, di analisi di flusso e di apprendimento automatico come file di log, dati IoT, dati clickstream e set di dati di grandi dimensioni |Qualsiasi tipo di dati di testo o binari, come back-end di applicazioni, dati di backup, archiviazione di supporti per streaming e dati di utilizzo generico. Supporto completo aggiuntivo per carichi di lavoro di analisi. Dati batch, interattivi, di analisi di flusso e di Machine Learning come file di log, dati IoT, dati clickstream e set di dati di grandi dimensioni. |
| Concetti chiave |Un account di Data Lake Storage Gen1 contiene cartelle, che a loro volta contengono dati archiviati come file |Un account di archiviazione include contenitori, che a loro volta contengono dati sotto forma di BLOB |
| Struttura |File system gerarchico |Archivio di oggetti con spazio dei nomi flat |
| API |API REST su HTTPS |API REST su HTTP/HTTPS |
| API lato server |[API REST compatibile con WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[API REST dell'archivio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Client del file system Hadoop |Sì |Sì |
| Operazioni sui dati: autenticazione |Basata sulle [identità di Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Basata su segreti condivisi: [chiavi di accesso dell'account](../storage/common/storage-account-keys-manage.md) e [chiavi di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operazioni sui dati: protocollo di autenticazione |[OpenID Connect](https://openid.net/connect/). Le chiamate devono contenere un token Web JSON (JWT) valido emesso da Azure Active Directory.|Message Authentication Code basato su hash (HMAC). Le chiamate devono contenere un hash SHA-256 con codifica Base64 su una parte della richiesta HTTP. |
| Operazioni sui dati: autorizzazione |Elenchi di controllo di accesso (ACL) POSIX.  Gli ACL basati su identità di Azure Active Directory possono essere impostati a livello di file e cartella. |Per l'autorizzazione a livello di account: usare [Chiavi di accesso dell'account](../storage/common/storage-account-keys-manage.md)<br>Per l'autorizzazione relativa all'account, al contenitore o al BLOB: usare [Chiavi di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operazioni sui dati: controllo |Disponibile. Per informazioni, vedere [qui](data-lake-store-diagnostic-logs.md) . |Disponibile |
| Crittografia dei dati inattivi |<ul><li>Trasparente, lato server</li> <ul><li>Con chiavi gestite dal servizio</li><li>Con chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di Azure</li></ul></ul> |<ul><li>Trasparente, lato server</li> <ul><li>Con chiavi gestite dal servizio</li><li>Con chiavi gestite dal cliente in Azure Key Vault (anteprima)</li></ul><li>crittografia lato client</li></ul> |
| Operazioni di gestione (ad esempio, creazione di account) |[Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) per la gestione degli account |[Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) per la gestione degli account |
| SDK per sviluppatori |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Prestazioni del carico di lavoro di analisi |Prestazioni ottimizzate per carichi di lavoro di analisi parallela. IOPS e velocità effettiva elevata. |Prestazioni ottimizzate per carichi di lavoro di analisi parallela. |
| Limiti di dimensioni |Nessun limite per le dimensioni degli account, le dimensioni dei file o il numero di file |Per i limiti specifici, vedere [obiettivi di scalabilità per gli account di archiviazione standard](../storage/common/scalability-targets-standard-account.md) e [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../storage/blobs/scalability-targets.md). Per ottenere limiti più elevati per gli account, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/) |
| Ridondanza geografica |Con ridondanza locale (più copie di dati in un'area di Azure) |Archiviazione con ridondanza locale, archiviazione con ridondanza della zona, archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura. Per ulteriori informazioni, vedere [qui](../storage/common/storage-redundancy.md) . |
| Stato del servizio |Disponibile a livello generale |Disponibile a livello generale |
| Disponibilità a livello di area |Vedere [qui](https://azure.microsoft.com/regions/#services) |Disponibili in tutte le aree di Azure |
| Prezzo |Vedi i [prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/) |Vedi i [prezzi](https://azure.microsoft.com/pricing/details/storage/) |
