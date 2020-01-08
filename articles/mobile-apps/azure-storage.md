---
title: Archiviazione cloud per la creazione di app scalabili altamente sicure e durevoli con archiviazione di Azure
description: Informazioni sui servizi per archiviare dati di applicazioni mobili strutturati e non strutturati di grandi dimensioni nel cloud.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453258"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Archiviazione cloud per app altamente sicure, durevoli e scalabili con archiviazione di Azure
[Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è la soluzione di archiviazione cloud Microsoft per le applicazioni moderne che offre un archivio oggetti a scalabilità elevata per oggetti dati, un servizio file System per il cloud, un archivio di messaggistica per la messaggistica affidabile e un archivio NoSQL. Archiviazione di Azure presenta le caratteristiche seguenti:
- **Durevole e a disponibilità elevata:** La ridondanza garantisce la sicurezza dei dati in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Sicurezza:** Tutti i dati scritti in archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabile:** I servizi sono progettati per essere altamente scalabili per soddisfare le esigenze di archiviazione dei dati e di prestazioni delle applicazioni odierne.
- **Gestito:** Azure gestisce automaticamente la manutenzione dell'hardware, gli aggiornamenti e i problemi critici.
- **Accessibile:** I dati sono accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client in un'ampia gamma di linguaggi, ad esempio .NET, Java, node. js, Python, PHP, Ruby e go e un'API REST matura. Lo scripting è supportato in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer offrono semplici soluzioni visive per l'utilizzo dei dati.

Usare i servizi seguenti per abilitare l'archiviazione cloud nelle app per dispositivi mobili.

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
[Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) offre una soluzione di archiviazione di oggetti per il cloud. L'archiviazione BLOB è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati che non sono conformi a una definizione o un modello di dati specifico, ad esempio testo o binario. Supporta diversi linguaggi utilizzati dalle librerie client. L'archiviazione BLOB è progettata per:
- Consente di gestire immagini o documenti direttamente in un browser.
- Archiviare i file per l'accesso distribuito.
- Trasmettere video e audio.
- Scrivere nei file di log.
- Archiviare i dati per il backup e il ripristino, il ripristino di emergenza e l'archiviazione.
- Archiviare i dati per l'analisi da un servizio ospitato in locale o in Azure.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione di Archiviazione BLOB di Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Guide introduttive](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure
[Archiviazione tabelle di Azure](https://azure.microsoft.com/services/storage/tables/) è un servizio che archivia dati NoSQL strutturati nel cloud e fornisce un archivio di chiavi o attributi con una progettazione senza schema. Il servizio Archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un archivio dati NoSQL, che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati e non relazionali. L'archiviazione tabelle viene in genere utilizzata per:
- Archiviare terabyte di dati strutturati in grado di servire applicazioni su scala Web.
- I set di dati di archiviazione che non richiedono join complessi, chiavi esterne o stored procedure e possono essere denormalizzati per l'accesso rapido.
- Eseguire rapidamente query sui dati utilizzando un indice cluster.
- Accedere ai dati usando il protocollo OData e le query LINQ con le librerie .NET di servizi dati Windows Communication Foundation (WCF).

È possibile utilizzare l'archiviazione tabelle per archiviare ed eseguire query su grandi set di dati strutturati e non relazionali. Le tabelle vengono ridimensionate in modo da aumentare la richiesta.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione di archiviazione tabelle di Azure](/azure/storage/tables/table-storage-overview)
- [Esempi](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Guide introduttive](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>File di Azure
Con [file di Azure](https://azure.microsoft.com/services/storage/files/)è possibile configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere tramite il protocollo SMB (Server Message Block) standard. Più macchine virtuali possono condividere gli stessi file con accesso sia in lettura che in scrittura. È anche possibile leggere i file usando l'interfaccia REST o le librerie client di archiviazione. È possibile accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso (SAS). È possibile generare token SAS. Consentono l'accesso specifico a un asset privato per un determinato periodo di tempo.

Le condivisioni file di Azure possono essere usate per:
- **Sostituire o integrare i file server locali:** I sistemi operativi più diffusi, come Windows, macOS e Linux, possono montare direttamente le condivisioni file di Azure ovunque si trovino nel mondo. Le condivisioni file di Azure possono anche essere replicate nei server Windows con Sincronizzazione file di Azure, in locale o nel cloud, per ottenere prestazioni elevate e per un caching efficiente e distribuito dei dati nella posizione in cui vengono usati.
- **Applicazioni Lift-and-Shift:** Eseguire la migrazione delle applicazioni nel cloud che prevedono una condivisione file per archiviare i dati dell'applicazione o dell'utente del file.
- **Semplifica lo sviluppo cloud:** File di Azure possono anche essere usati in diversi modi per semplificare i nuovi progetti di sviluppo cloud. Ad esempio:
    - **Impostazioni dell'applicazione condivisa:** Un modello comune per le applicazioni distribuite consiste nel disporre di file di configurazione in una posizione centralizzata in cui è possibile accedervi da numerose istanze dell'applicazione. Le istanze dell'applicazione possono caricare la configurazione tramite l'API REST del file. Gli utenti possono accedere ai file in base alle esigenze montando la condivisione SMB localmente.
    - **Condivisione di diagnostica:** Una condivisione file di Azure è una soluzione ideale per le applicazioni cloud per scrivere log, metriche e dump di arresto anomalo del sistema. I log possono essere scritti dalle istanze dell'applicazione tramite l'API REST del file. Gli sviluppatori possono accedervi montando la condivisione file nel computer locale. Questa funzionalità offre una grande flessibilità. Gli sviluppatori possono adottare lo sviluppo cloud senza dover abbandonare gli strumenti esistenti che conoscono.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione di File di Azure](/azure/storage/files/storage-files-introduction)
- [Guide introduttive](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Archiviazione code di Azure
[Archiviazione code di Azure](https://azure.microsoft.com/services/storage/queues/) è un servizio per l'archiviazione di un numero elevato di messaggi. È possibile accedere ai messaggi da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione di archiviazione code di Azure](/azure/storage/queues/)
- [Guide introduttive](/azure/storage/queues/storage-quickstart-queues-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
