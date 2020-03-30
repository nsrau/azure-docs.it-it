---
title: Cloud storage to build highly secure, durable, scalable apps with Azure Storage
description: Informazioni sui servizi per archiviare dati di applicazioni mobili strutturate e non strutturate di grandi dimensioni nel cloud.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240981"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloud storage for highly secure, durable, scalable apps with Azure Storage
[Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è la soluzione di archiviazione cloud di Microsoft per le applicazioni moderne che offre un archivio oggetti scalabile per gli oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica per la messaggistica affidabile e un archivio NoSQL. Archiviazione di Azure presenta le caratteristiche seguenti:
- **Durevole e altamente disponibile:** La ridondanza garantisce la sicurezza dei dati in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Sicuro:** Tutti i dati scritti in Archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabile:** I servizi sono progettati per essere massicciamente scalabili per soddisfare le esigenze di archiviazione dei dati e prestazioni delle applicazioni odierne.
- **Gestito:** Azure gestisce automaticamente la manutenzione hardware, gli aggiornamenti e i problemi critici.
- **Accessibile:** I dati sono accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client in diversi linguaggi, ad esempio .NET, Java, Node.js, Python, PHP, Ruby e Go e un'API REST matura. Scripting is supported in Azure PowerShell or the Azure CLI. Il portale di Azure e Azure Storage Explorer offrono soluzioni visive semplici per l'uso dei dati.

Usa i servizi seguenti per abilitare l'archiviazione cloud nelle tue app per dispositivi mobili.

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
[L'archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) di Azure offre una soluzione di archiviazione degli oggetti per il cloud. L'archiviazione BLOB è ottimizzata per l'archiviazione di enormi quantità di dati non strutturati che non sono aderenti a una definizione o a un modello di dati specifico, ad esempio testo o binario. Supporta una varietà di linguaggi utilizzati da librerie client. L'archiviazione BLOB è progettata per:Blob storage is designed to:
- Pubblicare immagini o documenti direttamente su un browser.
- Archiviare i file per l'accesso distribuito.
- Trasmetti video e audio in streaming.
- Scrivere nei file di registro.
- Archiviare i dati per il backup e il ripristino, il ripristino di emergenza e l'archiviazione.
- Archiviare i dati per l'analisi da un servizio locale o ospitato in Azure.Store data for analysis by an on-premises or Azure-hosted service.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com)
- [Documentazione di Archiviazione BLOB di Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Avvi rapidi](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure
[L'archiviazione tabelle](https://azure.microsoft.com/services/storage/tables/) di Azure è un servizio che archivia dati NoSQL strutturati nel cloud e fornisce un archivio chiavi o attributi con una progettazione senza schema. Il servizio Archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un archivio dati NoSQL, che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure.The service is a NoSQL data store, which accepts authenticated calls from inside and outside the Azure cloud. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati e non relazionali. L'archiviazione tabelle viene in genere usata per:Table storage is typically used to:
- Archivia terabyte di dati strutturati in grado di servire applicazioni su scala web.
- Archiviare set di dati che non richiedono join complessi, chiavi esterne o stored procedure e che possono essere denormalizzati per un accesso rapido.
- Eseguire rapidamente query sui dati utilizzando un indice cluster.
- Accedere ai dati utilizzando il protocollo OData e le query LINQ con le librerie .NET di Windows Communication Foundation (WCF) di Data Services.Access data by using the OData protocol and LINQ queries with Windows Communication Foundation (WCF) Data Services .NET libraries.

È possibile usare l'archiviazione tabelle per archiviare ed eseguire query su enormi set di dati strutturati e non relazionali. Le tabelle vengono ridimensionate con l'aumentare della domanda.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com)
- [Documentazione di archiviazione tabelle di Azure](/azure/storage/tables/table-storage-overview)
- [Esempi](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Avvi rapidi](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>File di Azure
Con File di [Azure](https://azure.microsoft.com/services/storage/files/)è possibile configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere utilizzando il protocollo SMB (Server Message Block) standard. Più macchine virtuali possono condividere gli stessi file con l'accesso in lettura e scrittura. È anche possibile leggere i file usando l'interfaccia REST o le librerie client di archiviazione. È possibile accedere ai file da qualsiasi parte del mondo utilizzando un URL che punta al file e include un token firma di accesso condiviso (SAS). È possibile generare token di firma di accesso condiviso. Consentono l'accesso specifico a un bene privato per un periodo di tempo specifico.

Le condivisioni file di Azure possono essere usate per:
- **Sostituire o integrare i file server locali:** I sistemi operativi più diffusi come Windows, macOS e Linux possono montare direttamente le condivisioni file di Azure ovunque si trovino nel mondo. Le condivisioni file di Azure possono anche essere replicate nei server Windows con Sincronizzazione file di Azure, in locale o nel cloud, per ottenere prestazioni elevate e per un caching efficiente e distribuito dei dati nella posizione in cui vengono usati.
- **Applicazioni di sollevamento e spostamento:** Eseguire la migrazione delle applicazioni nel cloud che prevedono che una condivisione file memorizzi i dati dell'applicazione file o dell'utente.
- **Semplifica lo sviluppo del cloud:** File di Azure può anche essere usato in diversi modi per semplificare nuovi progetti di sviluppo cloud. Ad esempio:
    - **Impostazioni dell'applicazione condivisa:** Un modello comune per le applicazioni distribuite consiste nel disporre di file di configurazione in una posizione centralizzata in cui è possibile accedervi da molte istanze dell'applicazione. Le istanze dell'applicazione possono caricare la configurazione tramite l'API REST File.Application instances can load their configuration through the File REST API. Gli utenti possono accedervi in base alle esigenze montando la condivisione SMB in locale.
    - **Condivisione diagnostica:** Una condivisione file di Azure è una posizione comoda per le applicazioni cloud per scrivere i log, le metriche e i dump di arresto anomalo del sistema. I log possono essere scritti dalle istanze dell'applicazione tramite l'API REST File.Logs can be written by the application instances via the File REST API. Gli sviluppatori possono accedervi montando la condivisione file nel computer locale. Questa funzionalità offre una grande flessibilità. Gli sviluppatori possono adottare lo sviluppo cloud senza dover abbandonare gli strumenti esistenti che conoscono.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com)
- [Documentazione di File di Azure](/azure/storage/files/storage-files-introduction)
- [Avvi rapidi](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Archiviazione code di Azure
[Archiviazione delle code](https://azure.microsoft.com/services/storage/queues/) di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. È possibile accedere ai messaggi da qualsiasi parte del mondo tramite chiamate autenticate tramite HTTP o HTTPS. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda potrebbe contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com)
- [Documentazione dell'archiviazione code di Azure](/azure/storage/queues/)
- [Avvi rapidi](/azure/storage/queues/storage-quickstart-queues-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
