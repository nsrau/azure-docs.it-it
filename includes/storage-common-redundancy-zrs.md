---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5f7477671e7be4e6e8fd736ab94fa7e7b1e99a6e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184404"
---
L'archiviazione con ridondanza della zona (ZRS) replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità (AZ). Ogni zona di disponibilità, con il cluster di archiviazione con ridondanza della zona al suo interno, è autonoma, con funzionalità di rete e utilità separate.

L'archiviazione dei dati in un account di archiviazione con ridondanza della zona assicura la possibilità di accedere ai dati e gestirli nel caso in cui una zona smetta di essere disponibile. L'archiviazione con ridondanza della zona offre prestazioni eccellenti e bassa latenza. L'archiviazione con ridondanza della zona offre gli stessi [obiettivi di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'[archiviazione con ridondanza locale (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Prendere in considerazione l'archiviazione con ridondanza della zona per scenari che richiedono coerenza assoluta, livelli elevati di durabilità e disponibilità elevata, anche nel caso in cui un'interruzione o una calamità naturale renda non disponibile un data center di una zona. L'archiviazione con ridondanza della zona offre almeno il 99,9999999999% (12 9) di durabilità degli oggetti di archiviazione nell'arco di un anno.

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Copertura del supporto e disponibilità a livello di area
L'archiviazione con ridondanza della zona attualmente supporta gli account di tipo [Utilizzo generico v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) standard. L'archiviazione con ridondanza della zona è disponibile per BLOB in blocchi, BLOB di pagine non del disco, file, tabelle e code. Supporta inoltre tutti i log di [Analisi archiviazione](../articles/storage/common/storage-analytics.md) e le [metriche di archiviazione](../articles/storage/common/storage-enable-and-view-metrics.md)

L'archiviazione con ridondanza della zona è disponibile a livello generale nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali 2
- Stati Uniti centrali
- Europa settentrionale
- Europa occidentale
- Francia centrale
- Asia sud-orientale

Microsoft continua ad abilitare l'archiviazione con ridondanza della zona in aree di Azure aggiuntive. Fare riferimento regolarmente alla pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/) per informazioni sulle nuove aree.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Cosa accade quando una zona smette di essere disponibile?

Se una zona smette di essere disponibile, i dati rimangono resilienti. Microsoft consiglia di continuare a seguire le procedure per la gestione degli errori temporanei, ad esempio implementando criteri di ripetizione con backoff esponenziale. Quando una zona non è disponibile, Azure avvia gli aggiornamenti di rete, ad esempio la modifica del puntamento DNS. Questi aggiornamenti possono interessare l'applicazione se si accede ai dati prima del loro completamento.

L'archiviazione con ridondanza della zona potrebbe non proteggere i dati in caso di un'emergenza a livello di area in cui più zone sono interessate in modo permanente. L'archiviazione con ridondanza della zona offre invece resilienza per i dati qualora diventino temporaneamente non disponibili. Per la protezione in caso di emergenze a livello di area, Microsoft consiglia di usare l'archiviazione con ridondanza geografica (GRS). Per altre informazioni sull'archiviazione con ridondanza geografica, vedere [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversione nella replica di archiviazione con ridondanza della zona
Attualmente, è possibile usare il portale di Azure o l'API del provider di risorse di archiviazione per modificare il tipo di ridondanza dell'account, a condizione di eseguire la migrazione da o verso l'archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Con l'archiviazione con ridondanza della zona, tuttavia, la migrazione non è altrettanto lineare perché implica lo spostamento fisico dei dati da un singolo timbro di archiviazione a più timbri all'interno di un'area. 

Ci sono due opzioni principali per la migrazione da o verso l'archiviazione con ridondanza della zona. È possibile copiare o spostare manualmente i dati dall'account esistente a un nuovo account di archiviazione con ridondanza della zona. È anche possibile richiedere una migrazione in tempo reale. Microsoft consiglia vivamente di eseguire una migrazione manuale in quanto non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di una migrazione in tempo reale. Un percorso di migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale e permette di controllare i tempi della migrazione.

Per eseguire una migrazione manuale, sono disponibili numerose opzioni:
- Usare strumenti esistenti come AzCopy, Storage SDK, strumenti di terze parti affidabili e così via.
- Se si ha familiarità con Hadoop o HDInsight, è possibile collegare l'account di archiviazione con ridondanza della zona di origine e quello di destinazione al cluster e usare una soluzione come DistCp per una parallelizzazione massiccia del processo di copia dei dati.
- Creare strumenti personalizzati basati su Storage SDK.

Se, tuttavia, una migrazione manuale comporta tempi di inattività di alcune applicazioni che non è possibile gestire, Microsoft offre un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto che consente di continuare a usare l'account di archiviazione esistente mentre viene eseguita la migrazione dei dati tra i timbri di archiviazione di origine e di destinazione. Durante la migrazione, si hanno a disposizione gli stessi livelli di durabilità e disponibilità normalmente previsti dal contratto di servizio.

La migrazione in tempo reale prevede tuttavia alcune restrizioni, elencate di seguito.

- Sebbene Microsoft risponda tempestivamente alle richieste di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento della migrazione. Se è necessario che i dati si trovino in un'archiviazione con ridondanza della zona entro un determinato tempo, è necessario eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati. 
- È possibile eseguire una migrazione in tempo reale solo da un account che usa la replica dell'archiviazione con ridondanza locale o dell'archiviazione con ridondanza geografica. Se l'account usa l'archiviazione con ridondanza geografica e accesso in lettura, è prima necessario eseguire la migrazione a uno di questi tipi di replica. Questo passaggio intermedio garantisce che l'endpoint secondario di sola lettura fornito dall'archiviazione con ridondanza geografica e accesso in lettura venga rimosso prima della migrazione.
- L'account deve contenere dati.
- Sono supportate solo le migrazioni all'interno di un'area. Se si vuole eseguire la migrazione dei dati in un account di archiviazione con ridondanza della zona che si trova in un'area diversa rispetto all'account di origine, è necessario eseguire una migrazione manuale.
- Sono supportati solo gli account di archiviazione Standard. Non è possibile eseguire la migrazione da un account di archiviazione Premium.

Le richieste di migrazione in tempo reale vengono inviate tramite il portale del supporto tecnico di Azure. Nel portale selezionare l'account di archiviazione da convertire in archiviazione con ridondanza della zona.
1. Fare clic su **Nuova richiesta di supporto**
2. Verificare le informazioni di base. Fare clic su **Avanti**. 
3. Nella sezione **Problema**: 
    - Lasciare invariato il valore di Gravità.
    - Tipo di problema = **Migrazione dei dati**
    - Categoria = **Migrate to ZRS within a region** (Migrazione a ZRS in un'area)
    - Titolo = **ZRS account migration** (Migrazione account ZRS) o un testo descrittivo
    - Dettagli = Desidero eseguire la migrazione a ZRS da [LRS, GRS] nell'area ______. 
4. Fare clic su **Avanti**.
5. Verificare che le informazioni di contatto nel pannello Informazioni contatto siano corrette.
6. Fare clic su **Submit**.

Si verrà contattati da un addetto del supporto tecnico. Tale persona sarà disponibile per fornire tutta l'assistenza necessaria. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Archiviazione con ridondanza della zona (versione classica): opzione legacy per la ridondanza dei BLOB in blocchi
> [!NOTE]
> Gli account della versione classica dell'archiviazione con ridondanza della zona in futuro saranno deprecati e sarà necessario eseguirne la migrazione il 31 marzo 2021. Microsoft invierà altri dettagli ai clienti della versione classica dell'archiviazione con ridondanza della zona prima che siano deprecati. Microsoft prevede di fornire in futuro un processo di migrazione automatizzato dalla versione classica dell'archiviazione con ridondanza della zona all'archiviazione con ridondanza della zona.

>[!NOTE]
> Quando l'archiviazione con ridondanza della zona sarà [disponibile a livello generale](#support-coverage-and-regional-availability) in un'area, non sarà più possibile creare un account della versione classica dell'archiviazione con ridondanza della zona dal portale in tale area. Sarà tuttavia possibile crearne uno in altri modi, ad esempio tramite Microsoft PowerShell e l'interfaccia della riga di comando di Azure, fino a quando la versione classica dell'archiviazione con ridondanza della zona non verrà deprecata.

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono tra data center in una o due aree. Una replica potrebbe non essere disponibile a meno che Microsoft non avvii il failover all'area secondaria. La versione classica dell'archiviazione con ridondanza della zona è disponibile solo per i **BLOB in blocchi** negli account di archiviazione di tipo [Utilizzo generico V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts). Un account della versione classica dell'archiviazione con ridondanza della zona non può essere convertito da o verso l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica e non include funzionalità di registrazione o metrica.

Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.

Per eseguire manualmente la migrazione di dati di un account di archiviazione con ridondanza della zona in o da un account di archiviazione con ridondanza locale, archiviazione con ridondanza della zona (versione classica), archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura, usare AzCopy, Azure Storage Explorer, Azure PowerShell o l'interfaccia della riga di comando di Azure. È anche possibile creare una soluzione di migrazione personalizzata con una delle librerie client di Archiviazione di Azure.
