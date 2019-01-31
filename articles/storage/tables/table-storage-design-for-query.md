---
title: Progettare tabelle di archiviazione di Azure per le query | Microsoft Docs
description: Progettare le tabelle per le query nell'archiviazione tabelle di Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 97373f6f0138d3ed8028ed4327b7e6cf90ad76a7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470740"
---
# <a name="design-for-querying"></a>Progettazione per le query
Le soluzioni di servizio tabelle possono eseguire un'intensa attività di lettura, di scrittura o una combinazione di entrambe. Questo articolo è incentrato sugli aspetti da prendere in considerazione quando si progetta un servizio tabelle in grado di supportare in modo efficiente le operazioni di lettura. Una progettazione che supporta in modo efficiente le operazioni di lettura è in genere efficiente anche nelle operazioni di scrittura. Esistono però altri aspetti da considerare per una progettazione che supporti le operazioni di scrittura, come illustrato nell'articolo [Progettazione per la modifica dei dati](table-storage-design-for-modification.md).

Quando si inizia a progettare una soluzione di servizio tabelle che consenta di leggere i dati in modo efficiente, è importante chiedersi quali query dovrà eseguire l'applicazione per recuperare i dati necessari dal servizio tabelle.  

> [!NOTE]
> Con il servizio tabelle, è fondamentale realizzare una progettazione corretta fin dall'inizio perché cambiarla in seguito è complesso e costoso. In un database relazionale, ad esempio, spesso è possibile risolvere i problemi di prestazioni semplicemente aggiungendo degli indici a un database esistente, ma questa opzione non è applicabile al servizio tabelle.  
> 
> 

Questa sezione è incentrata sui problemi chiave che è necessario affrontare quando si progettano le tabelle per le query. Gli argomenti trattati in questa sezione includono:

* [Come la scelta di PartitionKey e RowKey compromette le prestazioni delle query](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Scelta di un valore PartitionKey appropriato](#choosing-an-appropriate-partitionkey)
* [Ottimizzazione delle query per il servizio tabelle](#optimizing-queries-for-the-table-service)
* [Ordinamento dei dati nel servizio tabelle](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Come la scelta di PartitionKey e RowKey compromette le prestazioni delle query
I seguenti esempi presuppongono che nel servizio tabelle vengano archiviate entità dipendente con la struttura seguente (per maggiore chiarezza, nella maggior parte degli esempi viene omessa la proprietà **Timestamp** ):  

| *Nome colonna* | *Tipo di dati* |
| --- | --- |
| **PartitionKey** (nome del reparto) |string |
| **RowKey** (ID dipendente) |string |
| **FirstName** |string |
| **LastName** |string |
| **Age** |Integer |
| **EmailAddress** |string |

L'articolo [Panoramica di Archiviazione tabelle di Azure](table-storage-overview.md) descrive alcune delle principali funzionalità del servizio tabelle di Azure che influiscono direttamente sulla progettazione per le query. Se ne possono ricavare le seguenti linee guida generali per la progettazione di query del servizio tabelle. Si noti che la sintassi del filtro usata negli esempi seguenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](https://docs.microsoft.com/rest/api/storageservices/Query-Entities) (Entità query).  

* Una ***query di tipo punto*** è il tipo di ricerca più efficiente da usare ed è consigliata per le ricerche con volumi elevati o per le ricerche che richiedono una latenza molto bassa. Una query di questo tipo può usare gli indici per trovare in modo molto efficiente una singola entità specificando entrambi i valori **PartitionKey** e **RowKey**. Ad esempio, $filter=(PartitionKey eq 'Sales') e (RowKey eq '2')  
* La seconda miglior ricerca è la ***query di intervallo***, che usa **PartitionKey** e applica il filtro a un intervallo di valori **RowKey** per restituire più di un'entità. Il valore **PartitionKey** identifica una partizione specifica e i valori **RowKey** identificano un subset delle entità in quella partizione. Ad esempio, $filter=PartitionKey eq 'Sales' e RowKey ge 'S' e RowKey lt 'T'  
* La terza miglior ricerca è l'***analisi della partizione***, che usa **PartitionKey** e applica un filtro in base a un'altra proprietà non chiave e che potrebbe restituire più di un'entità. Il valore **PartitionKey** identifica una partizione specifica e i valori della proprietà selezionano un subset delle entità in quella partizione. Ad esempio: $filter=PartitionKey eq 'Sales' e LastName eq 'Smith'  
* Una ***scansione di tabella*** non include **PartitionKey** ed è molto inefficiente perché cerca le entità corrispondenti in tutte le partizioni della tabella, una alla volta. Una scansione di tabella viene eseguita indipendentemente dal fatto che il filtro usi **RowKey**o meno. Ad esempio: $filter = LastName eq 'Jones'  
* Le query che restituiscono più entità le ordinano in base a **PartitionKey** e **RowKey**. Per non dover riordinare le entità nel client, scegliere un valore **RowKey** che definisca l'ordinamento più comune.  

Si noti che, se si usa "**or**" per specificare un filtro basato su valori **RowKey**, si ottiene un'analisi della partizione che non viene considerata come query di intervallo. Pertanto, è consigliabile evitare query che utilizzano filtri ad esempio: $filter = PartitionKey eq "Sales" e (RowKey '121' o RowKey eq '322')  

Per esempi di codice lato client che usano la libreria client di archiviazione per eseguire query efficienti, vedere:  

* [Eseguire una query di tipo punto mediante la libreria client di archiviazione](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Recuperare più entità usando LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Proiezione lato server](table-storage-design-patterns.md#server-side-projection)  

Per esempi di codice lato client che possono gestire più tipi di entità archiviati nella stessa tabella, vedere:  

* [Usare tipi di entità eterogenei](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Scelta di un valore PartitionKey appropriato
La scelta di **PartitionKey** deve soddisfare sia la necessità di abilitare l'uso di transazioni EGT (per assicurare la coerenza) sia il requisito di distribuzione delle entità tra più partizioni (per assicurare una soluzione scalabile).  

Da una parte, pur essendo possibile archiviare tutte le entità in una singola partizione, questo potrebbe limitare la scalabilità della soluzione e impedirebbe al servizio tabelle di bilanciare il carico delle richieste. D'altra parte, pur essendo possibile archiviare un'entità per partizione, ottenendo così la scalabilità elevata e consentendo al servizio tabelle di bilanciare il carico delle richieste, questo impedirebbe di usare le transazioni del gruppo di entità.  

Il valore **PartitionKey** ideale consente di usare query efficienti e ha un numero sufficiente di partizioni per garantire la scalabilità della soluzione. Di solito le entità dispongono una proprietà apposita che le distribuisce in un numero sufficiente di partizioni.

> [!NOTE]
> Ad esempio, in un sistema che archivia le informazioni sugli utenti o i dipendenti, l'ID utente può essere un valore PartitionKey valido. È possibile avere più entità che utilizzano un ID utente specificato come chiave di partizione. Ogni entità che archivia i dati su un utente è raggruppata in una singola partizione e quindi queste entità sono accessibili tramite gruppi di entità, mantenendo la scalabilità elevata.
> 
> 

Gli altri aspetti da considerare per la scelta di **PartitionKey** riguardano l'inserimento, l'aggiornamento e l'eliminazione delle entità. Per altre informazioni, vedere [Progettare tabelle per la modifica dei dati](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Ottimizzazione delle query per il servizio tabelle
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey** in un singolo indice cluster. È per questo che le query di tipo punto sono le più efficienti da usare. Tuttavia, non esistono altri indici oltre a quello nell'indice cluster in **PartitionKey** e **RowKey**.

Molte progettazioni devono soddisfare alcuni requisiti per abilitare la ricerca di entità in base a più criteri, ad esempio trovare le entità dipendente in base a indirizzo di posta elettronica, ID dipendente o cognome. I modelli descritti in [Modelli di progettazione tabelle](table-storage-design-patterns.md) soddisfano questi tipi di requisiti e descrivono come ovviare al fatto che il servizio tabelle non fornisca indici secondari:  

* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori **RowKey** diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
* [Modello per entità di indice](table-storage-design-patterns.md#index-entities-pattern) - Mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

## <a name="sorting-data-in-the-table-service"></a>Ordinamento dei dati nel servizio tabelle
Il servizio tabelle restituisce le entità in ordine crescente in base a **PartitionKey** e quindi a **RowKey**. Queste chiavi sono valori stringa e, per essere certi che i valori numerici siano ordinati correttamente, è consigliabile convertirli in una lunghezza fissa aggiungendo degli zeri se necessario. Se, ad esempio, il valore dell'ID dipendente usato come **RowKey** è un valore intero, è consigliabile convertire l'ID dipendente **123** in **00000123**.  

In molte applicazioni è necessario usare i dati ordinandoli in modo diverso, ad esempio ordinando i dipendenti per nome o per data di assunzione. I modelli seguenti descrivono come alternare l'ordinamento per le entità:  

* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori.  
* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori RowKey.
* [Modello della parte finale del log](table-storage-design-patterns.md#log-tail-pattern) - Recupera le entità *n* aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.  

## <a name="next-steps"></a>Passaggi successivi

- [Modelli di progettazione tabella](table-storage-design-patterns.md)
- [Modellazione di relazioni](table-storage-design-modeling.md)
- [Crittografia dei dati delle tabelle](table-storage-design-encrypt-data.md)
- [Progettazione per la modifica dei dati](table-storage-design-for-modification.md)
