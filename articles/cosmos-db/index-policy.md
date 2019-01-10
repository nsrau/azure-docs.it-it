---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni sull'indicizzazione in Azure Cosmos DB. Informazioni su come configurare e modificare i criteri di indicizzazione per l'indicizzazione automatica e per ottenere prestazioni migliori.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 0fb2c3daf19ce07d9641cbc5504cb3b598ad5b0d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034456"
---
# <a name="indexing-policy-in-azure-cosmos-db"></a>Criteri di indicizzazione in Azure Cosmos DB

È possibile eseguire l'override dei criteri di indicizzazione predefiniti in un contenitore di Azure Cosmos configurando i parametri seguenti:

* **Includere o escludere elementi e percorsi dall’indicizzazione**: è possibile escludere o includere elementi specifici dall’indicizzazione quando si inseriscono o sostituiscono gli elementi all'interno di un contenitore. È anche possibile includere o escludere proprietà/percorsi specifici nell'indicizzazione tra contenitori. I percorsi possono includere caratteri jolly, ad esempio *.

* **Configurare i tipi di indicizzazione**: oltre a ordinare percorsi indicizzati, è possibile aggiungere altri tipi di indicizzazione, ad esempio spaziale.

* **Configurare le modalità di indicizzazione**: tramite i criteri di indicizzazione in un contenitore, è possibile configurare diverse modalità di indicizzazione, ad esempio *Coerente* oppure *Nessuna*.

## <a name="indexing-modes"></a>Modalità di indicizzazione 

Azure Cosmos DB supporta due modalità di indicizzazione che è possibile configurare in un contenitore di Azure Cosmos. È possibile configurare le due modalità di indicizzazione seguenti tramite i criteri di indicizzazione: 

* **Coerente**: se i criteri di un contenitore di Azure Cosmos sono impostati su Coerente, le query su un determinato contenitore seguono lo stesso livello di coerenza di quello specificato per le letture punto (ad esempio assoluta, con obsolescenza associata, di sessione o finale). 

  L'indice viene aggiornato in modo sincrono man mano che si aggiornano gli elementi. Ad esempio, le operazioni di inserimento, sostituzione, aggiornamento ed eliminazione su un elemento comporteranno l'aggiornamento dell'indice. L'indicizzazione coerente supporta query coerenti con effetti sulla velocità effettiva di scrittura. La riduzione della velocità effettiva di scrittura dipende dai "percorsi inclusi nell'indicizzazione" e dal "livello di coerenza". La modalità di indicizzazione coerente è progettata per carichi di lavoro in cui si richiede la scrittura rapida e l'esecuzione immediata delle query.

* **Nessuna**: un contenitore in modalità Nessuna non è associato ad alcun indicizzazione. Questa modalità viene usata in genere se il database di Azure Cosmos funge da archivio di coppie chiave/valore e l'accesso agli elementi avviene solo tramite il rispettivo ID proprietà.

  > [!NOTE]
  > La configurazione della modalità Nessuna indicizzazione ha l'effetto collaterale di eliminare eventuali indici esistenti. Usare questa opzione se i criteri di accesso richiedono solo un ID o un collegamento automatico.

I livelli di coerenza delle query vengono mantenuti simili alle normali operazioni di lettura. Il database di Azure Cosmos restituisce un errore se si esegue una query sul contenitore che ha Nessuna indicizzazione come modalità. Le query possono essere eseguite come analisi tramite l'intestazione  **x-ms-documentdb-enable-scan**  esplicita nell'API REST o l'opzione di richiesta **EnableScanInQuery** usando l'SDK .NET. Alcune funzionalità delle query, ad esempio ORDER BY, non sono attualmente supportate con **EnableScanInQuery**, in quanto richiedono un indice corrispondente.

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

In Azure Cosmos DB, è possibile aggiornare i criteri di indicizzazione di un contenitore in qualsiasi momento. Una modifica dei criteri di indicizzazione in un contenitore di Azure Cosmos può comportare una modifica nella forma dell'indice. Questa modifica influisce sui percorsi che possono essere indicizzati, sulla loro precisione e sul modello di coerenza dell'indice stesso. Una modifica nei criteri di indicizzazione richiede una trasformazione dell'indice precedente in uno nuovo.

### <a name="index-transformations"></a>Trasformazioni dell'indice

Le trasformazioni dell'indice vengono eseguite online. Gli elementi indicizzati in base ai vecchi criteri vengono trasformati in modo efficiente in base ai nuovi criteri senza modificare la disponibilità di scrittura o la velocità effettiva di provisioning del contenitore. La coerenza delle operazioni di lettura e scrittura eseguite tramite l'API REST, gli SDK o all'interno di stored procedure e trigger non cambia durante la trasformazione dell'indice.

La modifica dei criteri di indicizzazione è un'operazione asincrona e il tempo necessario per completare l'operazione dipende dal numero di elementi, dalla velocità effettiva sottoposta a provisioning e dalle dimensioni degli elementi. Durante la reindicizzazione, la query potrebbe non restituire tutti i risultati corrispondenti se usa l'indice che si sta modificando. Inoltre, le query non restituiranno alcun guasto/errore. Durante la reindicizzazione, le query sono alla fine coerenti indipendentemente dalla configurazione della modalità indicizzazione. Una volta completata la trasformazione dell'indice, si continueranno a vedere risultati coerenti. Questo si applica alle query emesse da interfacce come SDK, API REST o stored procedure e trigger. La trasformazione dell'indice viene eseguita in modo asincrono in background nelle repliche usando le risorse di riserva disponibili per una replica specifica.

Tutte le trasformazioni degli indici vengono eseguite sul posto. Azure Cosmos DB non conserva due copie dell'indice. Questo significa che non è necessario, né viene usato ulteriore spazio su disco nei contenitori durante la trasformazione dell'indice.

Quando si modificano i criteri di indicizzazione, le modifiche applicate per il passaggio dall'indice precedente a quello nuovo si basano prevalentemente sulle configurazioni delle modalità di indicizzazione. Le configurazioni delle modalità di indicizzazione svolgono un ruolo cruciale rispetto ad altre proprietà come i percorsi inclusi/esclusi, i tipi di indice e la precisione.

Se entrambi i criteri (precedente e nuovo) usano l'indicizzazione **Coerente**, il database di Azure Cosmos esegue una trasformazione di indici online. Non è possibile applicare un'altra modifica ai criteri di indicizzazione in modalità di indicizzazione coerente mentre è in corso la trasformazione. Quando si passa alla modalità Nessuna indicizzazione, l'indice viene eliminato immediatamente. Il passaggio a nessuna indicizzazione è utile quando si vuole annullare una trasformazione in corso e iniziare da capo con criteri di indicizzazione diversi.

Per completare una trasformazione dell'indice con successo, assicurarsi che il contenitore disponga dello spazio di archiviazione sufficiente. Se il contenitore raggiunge la quota di archiviazione, la trasformazione dell'indice viene sospesa. La trasformazione dell'indice viene ripresa automaticamente quando è disponibile spazio di archiviazione, ad esempio quando si eliminano alcuni elementi.

## <a name="modifying-the-indexing-policy---examples"></a>Modifica dei criteri di indicizzazione - Esempi

I seguenti sono i casi d'uso più comuni in cui si aggiorna un criterio di indicizzazione:

* Si desidera ottenere risultati coerenti durante il normale funzionamento, ma si ritorna alla modalità **Nessuna indicizzazione** durante le importazioni di dati in blocco.

* Si desidera iniziare a usare nuove funzionalità di indicizzazione contenitori di Azure Cosmos correnti. Ad esempio, è possibile usare query geospaziali, che richiedono il tipo di indice spaziale, oppure query di intervallo di stringhe/ORDER BY, che richiedono il tipo di indice di intervallo di stringhe.

* Si desidera selezionare manualmente le proprietà da indicizzare e modificarle nel tempo per adattarle ai propri carichi di lavoro.

* Si desidera ottimizzare la precisione di indicizzazione per migliorare le prestazioni delle query o ridurre l'uso dell'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

* [Panoramica dell'indicizzazione](index-overview.md)
* [Tipi di indice](index-types.md)
* [Percorsi di indice](index-paths.md)
* [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
