---
title: Archiviazione cloud per la creazione di app scalabili altamente sicure e durevoli con archiviazione di Azure
description: Informazioni sui servizi per archiviare dati di applicazioni mobili strutturati e non strutturati di grandi dimensioni nel cloud.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795653"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Archiviazione cloud per app altamente sicure, durevoli e scalabili con archiviazione di Azure
[Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è la soluzione di archiviazione cloud Microsoft per le applicazioni moderne che offre un archivio oggetti a scalabilità elevata per oggetti dati, un servizio file System per il cloud, un archivio di messaggistica per la messaggistica affidabile e un archivio NoSQL. Archiviazione di Azure presenta le caratteristiche seguenti:
- La ridondanza **durevole e a disponibilità elevata** garantisce la sicurezza dei dati in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Sicuro** : tutti i dati scritti in archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabile** : i servizi sono progettati per essere altamente scalabili per soddisfare le esigenze di archiviazione dei dati e di prestazioni delle applicazioni odierne.
- **Gestito** : Azure gestisce la manutenzione dell'hardware, gli aggiornamenti e i problemi critici.
- I dati sono **accessibili** da qualsiasi parte del mondo tramite http o HTTPS. Microsoft offre librerie client in un'ampia gamma di linguaggi, tra cui .NET, Java, node. js, Python, PHP, Ruby, go e altri, oltre a un'API REST avanzata. La creazione di script è supportata nell'interfaccia della riga di comando di Azure Azure PowerShell o nell'interfaccia della riga di comando di Azure e i portale di Azure e Azure Storage Explorer offrono semplici soluzioni visive per

Usare i servizi seguenti per abilitare l'archiviazione cloud nelle app per dispositivi mobili.

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
[Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) offre una soluzione di archiviazione degli oggetti per il cloud ed è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati che non sono conformi a un particolare modello o definizione di dati, ad esempio testo o binario. Supporta le librerie client diverse lingue ed è progettato per:
- Invio di immagini o documenti direttamente in un browser.
- Archiviazione di file per l'accesso distribuito.
- Flussi audio e video.
- Scrittura in file di log.
- Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
- Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione](/azure/storage/blobs/storage-blobs-introduction)
- [Guide introduttive](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure
[Archiviazione tabelle di Azure](https://azure.microsoft.com/services/storage/tables/) è un servizio che archivia dati NoSQL strutturati nel cloud, offrendo un archivio di chiavi/attributi con una progettazione senza schema. Il servizio Archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un archivio dati NoSQL, che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Di seguito sono riportati gli usi più comuni per il servizio Archiviazione tabelle:
- Archiviazione di TBs di dati strutturati in grado di servire applicazioni su scala Web.
- Archiviazione di set di impostazioni che non richiedono join complessi, chiavi esterne o stored procedure e possono essere denormalizzati per l'accesso rapido.
- Esecuzione rapida di query sui dati tramite un indice cluster.
- Accesso ai dati tramite il protocollo OData e le query LINQ con le librerie .NET di WCF Data Service.

È possibile usare il servizio Archiviazione tabelle per archiviare e interrogare grandi set di dati strutturati non relazionali, con tabelle scalabili in base all'aumento della domanda.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione](/azure/storage/tables/table-storage-overview)
- [Esempi](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Guide introduttive](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>File di Azure
[File di Azure](https://azure.microsoft.com/services/storage/files/) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere usando il protocollo Server Message Block (SMB) standard. Più macchine virtuali possono condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file usando l'interfaccia REST o le librerie dei client di archiviazione. È possibile accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso (SAS). È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file di Azure possono essere usate per:
- Sostituire o integrare file server locali: i sistemi operativi più diffusi, come Windows, macOS e Linux, possono montare direttamente le condivisioni file di Azure ovunque si trovino nel mondo. Le condivisioni file di Azure possono anche essere replicate nei server Windows con Sincronizzazione file di Azure, in locale o nel cloud, per ottenere prestazioni elevate e per un caching efficiente e distribuito dei dati nella posizione in cui vengono usati.
- Consente di spostare nel cloud **le applicazioni** che prevedono una condivisione file per archiviare i dati dell'applicazione o dell'utente del file.
- **Semplificare lo sviluppo cloud**: file di Azure possono essere usati in diversi modi per semplificare i nuovi progetti di sviluppo cloud. ad esempio:
    - Impostazioni dell'applicazione condivisa: un modello comune per le applicazioni distribuite consiste nel disporre di file di configurazione in una posizione centralizzata in cui è possibile accedervi da numerose istanze dell'applicazione. Le istanze dell'applicazione possono caricare la propria configurazione tramite l'API REST di File e gli utenti possono accedere alle istanze in base alle esigenze mondando la condivisione SMB in locale.
    - Condivisione di diagnostica: una condivisione file di Azure è una soluzione ideale per le applicazioni cloud per scrivere log, metriche e dump di arresto anomalo del sistema. I log possono essere scritti dalle istanze dell'applicazione tramite l'API REST di File e gli sviluppatori possono accedervi montando la condivisione file nel computer locale. Ciò consente una notevole flessibilità perché gli sviluppatori possono dedicarsi allo sviluppo cloud senza dover abbandonare gli strumenti che già conoscono e preferiscono.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione](/azure/storage/files/storage-files-introduction)
- [Guide introduttive](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Code di Azure
[Archiviazione code di Azure](https://azure.microsoft.com/services/storage/queues/) è un servizio per l'archiviazione di un numero elevato di messaggi. È possibile accedere ai messaggi ovunque ci si trovi con chiamate autenticate tramite HTTP o HTTPS. Un messaggio della coda può avere una dimensione massima di 64 KB e una coda può contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione](/azure/storage/queues/)
- [Guide introduttive](/azure/storage/queues/storage-quickstart-queues-portal)
- [Esempi](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
