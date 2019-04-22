---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni sull'indicizzazione in Azure Cosmos DB. Informazioni su come configurare e modificare i criteri di indicizzazione per l'indicizzazione automatica e per ottenere prestazioni migliori.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278565"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Criteri di indicizzazione in Azure Cosmos DB

È possibile eseguire l'override dei criteri di indicizzazione predefiniti in un contenitore di Azure Cosmos configurando i parametri seguenti:

* **Includere o escludere elementi e percorsi dall’indicizzazione**: È possibile escludere o includere elementi specifici in corrispondenza dell'indice, quando si inseriscono o sostituiscono gli elementi all'interno di un contenitore. È anche possibile includere o escludere proprietà/percorsi specifici nell'indicizzazione tra contenitori. I percorsi possono includere caratteri jolly, ad esempio *.

* **Configurare i tipi di indicizzazione**: Inoltre per intervallo percorsi indicizzati, è possibile aggiungere altri tipi di indici, ad esempio spaziale.

* **Configurare le modalità di indicizzazione**: tramite i criteri di indicizzazione in un contenitore, è possibile configurare diverse modalità di indicizzazione, ad esempio *Coerente* oppure *Nessuna*.

## <a name="indexing-modes"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione che è possibile configurare in un contenitore di Azure Cosmos tramite criteri di indicizzazione:

* **Coerente**: Se criterio di un contenitore Azure Cosmos è impostato su *Consistent*, le query su un determinato contenitore seguono lo stesso livello di coerenza a quello specificato per letture punto (ad esempio, assoluta, decadimento ristretto, sessione o finale). 

  L'indice viene aggiornato in modo sincrono man mano che si aggiornano gli elementi. Ad esempio, le operazioni di inserimento, sostituzione, aggiornamento ed eliminazione su un elemento comporteranno l'aggiornamento dell'indice. L'indicizzazione coerente supporta query coerenti con effetti sulla velocità effettiva di scrittura. La riduzione della velocità effettiva di scrittura dipende i percorsi"inclusi nell'indice" e "livello di coerenza". Modalità di indicizzazione coerente è progettata per mantenere aggiornati con tutti gli aggiornamenti dell'indice e per eseguire immediatamente le query.

* **Nessuna**: un contenitore in modalità Nessuna non è associato ad alcun indicizzazione. Questa modalità viene usata in genere se il database di Azure Cosmos funge da archivio di coppie chiave/valore e l'accesso agli elementi avviene solo tramite il rispettivo ID proprietà.

  > [!NOTE]
  > Configurare la modalità di indicizzazione come un *None* ha l'effetto collaterale di eliminare tutti gli indici esistenti. Usare questa opzione se i criteri di accesso richiedono solo un ID o un collegamento automatico.

I livelli di coerenza delle query vengono mantenuti simili alle normali operazioni di lettura. Il database Cosmos Azure restituisce un errore se si esegue una query il contenitore che include un' *None* la modalità di indicizzazione. È possibile eseguire la query come analisi tramite l'impostazione esplicita **x-ms-documentdb-enable-scan** intestazione nell'API REST o il **EnableScanInQuery** opzione di richiesta tramite .NET SDK. Alcune funzionalità delle query, ad esempio ORDER BY, non sono attualmente supportate con **EnableScanInQuery**, in quanto richiedono un indice corrispondente.

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

In Azure Cosmos DB, è possibile aggiornare i criteri di indicizzazione di un contenitore in qualsiasi momento. Una modifica nei criteri di indicizzazione in un contenitore di Azure Cosmos può comportare una modifica nella forma dell'indice. Questa modifica influisce sui percorsi che possono essere indicizzati, sulla loro precisione e sul modello di coerenza dell'indice stesso. Una modifica nei criteri di indicizzazione richiede una trasformazione dell'indice precedente in uno nuovo.

### <a name="index-transformations"></a>Trasformazioni dell'indice

Le trasformazioni dell'indice vengono eseguite online. Gli elementi indicizzati per i criteri precedenti vengono trasformati in modo efficiente per il nuovo criterio senza modificare la disponibilità di scrittura o la velocità effettiva di provisioning per il contenitore. La coerenza di lettura e scrittura delle operazioni eseguite usando l'API REST, SDK, o stored procedure e trigger non cambia durante la trasformazione dell'indice.

Modifica dei criteri di indicizzazione è un'operazione asincrona e il tempo necessario per completare l'operazione dipende dal numero di elementi, velocità effettiva con provisioning e la dimensione degli elementi. Durante la reindicizzazione è in corso, la query non può restituire tutti i risultati corrispondenti, se si utilizza l'indice che si sta modificando le query e le query non restituisce eventuali errori di. Durante la reindicizzazione è in corso, le query sono comunque coerenti indipendentemente dalla configurazione della modalità indicizzazione. Una volta completata la trasformazione dell'indice, si continueranno a vedere risultati coerenti. Questo si applica alle query emesse da interfacce come SDK, API REST o stored procedure e trigger. Trasformazione dell'indice viene eseguita in modo asincrono in background nelle repliche usando le risorse di riserva disponibili per le repliche specifiche.

Tutte le trasformazioni degli indici vengono eseguite sul posto. Azure Cosmos DB non conserva due copie dell'indice. Questo significa che non è necessario, né viene usato ulteriore spazio su disco nei contenitori durante la trasformazione dell'indice.

Quando si modificano i criteri di indicizzazione, le modifiche vengono applicate per spostare dall'indice precedente al nuovo indice e dipendono principalmente le configurazioni di modalità di indicizzazione. Le configurazioni delle modalità di indicizzazione svolgono un ruolo cruciale rispetto ad altre proprietà come i percorsi inclusi/esclusi, i tipi di indice e la precisione.

Se entrambi i criteri (precedente e nuovo) usano l'indicizzazione **Coerente**, il database di Azure Cosmos esegue una trasformazione di indici online. Non è possibile applicare un'altra modifica ai criteri di indicizzazione in modalità di indicizzazione coerente mentre è in corso la trasformazione. Quando si passa alla modalità Nessuna indicizzazione, l'indice viene eliminato immediatamente. Il passaggio a nessuna indicizzazione è utile quando si vuole annullare una trasformazione in corso e iniziare da capo con criteri di indicizzazione diversi.

## <a name="modifying-the-indexing-policy---examples"></a>Modifica dei criteri di indicizzazione - Esempi

Di seguito sono i casi d'uso più comune quando si desidera aggiornare un criterio di indicizzazione:

* Se si desidera avere risultati coerenti durante il normale funzionamento, ma per eseguire il fallback il **None** la modalità di indicizzazione durante le importazioni di dati bulk.

* Si desidera iniziare a usare nuove funzionalità di indicizzazione contenitori di Azure Cosmos correnti. Ad esempio, è possibile usare query geospaziali, che richiedono il tipo di indice spaziale, oppure query di intervallo di stringhe/ORDER BY, che richiedono il tipo di indice di intervallo di stringhe.

* Si desidera selezionare manualmente le proprietà da indicizzare e modificarle nel tempo per adattarle ai propri carichi di lavoro.

* Si desidera ottimizzare la precisione di indicizzazione per migliorare le prestazioni delle query o ridurre l'uso dell'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

* [Panoramica dell'indicizzazione](index-overview.md)
* [Tipi di indice](index-types.md)
* [Percorsi di indice](index-paths.md)
* [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
