---
title: Modello di determinazione dei prezzi di Azure Cosmos DB
description: Questo articolo illustra il modello di determinazione dei prezzi di Azure Cosmos DB e come questo semplifica la gestione e la pianificazione dei costi.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 5651e7ee8196eec9d1b1bf20e84ed73e484afb4b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542398"
---
# <a name="pricing-model-of-azure-cosmos-db"></a>Modello di determinazione dei prezzi di Azure Cosmos DB 

Il modello di determinazione dei prezzi di Azure Cosmos DB semplifica la gestione e la pianificazione dei costi. Con Azure Cosmos DB viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione usate.

* **Velocità effettiva sottoposta a provisioning**: La velocità effettiva sottoposta a provisioning (denominata anche velocità effettiva riservata) garantisce prestazioni elevate su qualsiasi scala. Specificando la velocità effettiva (UR/s) necessaria, Azure Cosmos DB dedica le risorse necessarie per garantire la velocità effettiva configurata. Verrà quindi addebitata su base oraria la velocità effettiva massima sottoposta a provisioning per un'ora specifica.

   > [!NOTE]
   > Poiché il modello di velocità effettiva sottoposta a provisioning dedica risorse per il contenitore o database, la velocità effettiva sottoposta a provisioning viene addebitata anche se non vengono eseguiti carichi di lavoro.

* **Risorse di archiviazione usate**: Viene addebitata una tariffa fissa per la quantità totale di spazio di archiviazione (in GB) usato per dati e indici per una determinata ora.

La velocità effettiva sottoposta a provisioning, specificata come [unità richiesta](request-units.md) al secondo o UR/s, consente di leggere o scrivere dati nei contenitori o database. È possibile [eseguire il provisioning della velocità effettiva in un database o in un contenitore](set-throughput.md). In base alle esigenze del carico di lavoro, è possibile ridurre o aumentare la velocità effettiva in qualsiasi momento. I prezzi di Azure Cosmos DB sono elastici e proporzionali alla velocità effettiva configurata in un database o contenitore. I valori minimi di velocità effettiva e archiviazione e gli incrementi di scalabilità offrono una gamma completa di prezzi rispetto a uno spettro di elasticità per tutti i segmenti di clienti, da contenitori su scala ridotta a contenitori su larga scala. Ogni database o contenitore viene fatturato su base oraria per la velocità effettiva sottoposta a provisioning nelle unità di 100 UR/s, con un minimo di 400 UR/s e spazio di archiviazione usato in GB. A differenza della velocità effettiva sottoposta a provisioning, l'archiviazione è fatturata in base al consumo, per cui non è necessario riservare in anticipo alcuna risorsa di archiviazione. Verranno addebitate le sole risorse di archiviazione usate.

Per altre informazioni, vedere la [pagina dei prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [Informazioni sulla fattura di Azure Cosmos DB](understand-your-bill.md).

Il modello di prezzi in Azure Cosmos DB è coerente in tutte le API. Per altre informazioni, vedere [Costo totale di proprietà (TCO) con Azure Cosmos DB](total-cost-ownership.md). È prevista una velocità effettiva minima necessaria in un database o un contenitore per garantire il rispetto dei contratti di servizio e assicurarsi che l'utente possa aumentare o diminuire la velocità effettiva sottoposta a provisioning di $6 per ogni 100 UR/s.

Il prezzo minimo per la velocità effettiva basata sia su contenitori che su database è attualmente di $24/mese (vedere la [pagina dei prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) per informazioni aggiornate). Se il carico di lavoro usa più contenitori, è possibile ottimizzare i costi tramite la velocità effettiva a livello di database, che consente di disporre di un numero qualsiasi di contenitori in un database che condividono la velocità effettiva. La tabella seguente riepiloga la velocità effettiva sottoposta a provisioning e i costi per diverse entità:

|**Entità**  | **Velocità effettiva minima e costi** |**Incrementi per il ridimensionamento e costi** |**Ambito di provisioning** |
|---------|---------|---------|-------|
|Database    | 400 UR/s ($24/mese)    | 100 UR/s ($6/mese)   |La velocità effettiva viene riservata per il database e viene condivisa dai contenitori all'interno del database |
|Contenitore     | 400 UR/s ($24/mese)    | 100 UR/s ($6/mese)  |La velocità effettiva viene riservata per un contenitore specifico |

Come illustrato nella tabella precedente, la velocità effettiva minima in Azure Cosmos DB inizia a un prezzo di $24/mese. Se si inizia con la velocità effettiva minima, eseguendo un ridimensionamento nel corso del tempo per supportare i carichi di lavoro di produzione, i costi saliranno in modo graduale, con incrementi di $6 al mese. Il modello tariffario<di prezzi di Azure Cosmos DB è elastico e prevede aumenti o riduzioni graduali man mano che si esegue un ridimensionamento verso l'alto o verso il basso.

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB 

Azure Cosmos DB offre diverse opzioni gratuite per gli sviluppatori. Tali opzioni includono:

* **Account Azure gratuito**: Azure prevede un [livello gratuito](https://azure.microsoft.com/free/) che offre $200 sotto forma di crediti di Azure per i primi 30 giorni e una quantità limitata di servizi gratuiti per 12 mesi. Per altre informazioni, vedere [Account gratuito di Azure](../billing/billing-avoid-charges-free-account.md). Azure Cosmos DB è incluso nell'account Azure gratuito. Per Azure Cosmos DB in particolare, l'account gratuito offre 5 GB di archiviazione e 400 UR di velocità effettiva sottoposta a provisioning per l'intero anno. 

* **Try Azure Cosmos DB for free** (Prova gratuitamente Azure Cosmos DB): È possibile provare Azure Cosmos DB per un periodo di tempo limitato tramite l'opzione Try Azure Cosmos DB for free» (Prova gratuitamente Azure Cosmos DB). È possibile creare un account Azure Cosmos DB, creare database e raccolte ed eseguire un'applicazione di esempio usando le opzioni di avvio rapido e le esercitazioni offerte. È possibile eseguire l'applicazione di esempio senza sottoscrivere alcun account di Azure né usare la carta di credito. L'opzione [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) consente di usare Azure Cosmos DB per un mese, con la possibilità di rinnovare l'account un numero illimitato di volte.

* **Emulatore di Azure Cosmos DB**: L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB a fini di sviluppo. L'emulatore è offerto senza costi aggiuntivi e con un'elevata fedeltà al servizio cloud. Usando l'emulatore di Azure Cosmos DB è possibile sviluppare e testare le applicazioni in locale, senza creare una sottoscrizione di Azure né sostenere alcun costo. È possibile sviluppare applicazioni usando l'emulatore in locale prima del passaggio in produzione. Quando si è soddisfatti delle funzionalità dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud e risparmiare in modo significativo sui costi. Per altre informazioni e dettagli sull'emulatore, vedere l'articolo [Uso di Azure Cosmos DB per sviluppo e test](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Prezzi con capacità riservata

La [capacità riservata](cosmos-db-reserved-capacity.md) di Azure Cosmos DB consente di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per uno o tre anni. È possibile ridurre significativamente i costi con impegni inziali di un anno o di tre anni e risparmiare tra il 20 e il 65% rispetto al prezzo normale. La capacità riservata di Azure Cosmos DB aiuta a ridurre i costi pagando anticipatamente per la velocità effettiva con provisioning (UR/s) per un periodo di uno o tre anni e ottenendo uno sconto sulla velocità effettiva con provisioning. 

La capacità riservata consente di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato del runtime delle risorse di Azure Cosmos DB. La capacità riservata è disponibile in modo coerente per tutte le API, incluso MongoDB, Cassandra, SQL, Gremlin e Azure Tables, per tutte le aree geografiche del mondo. Altre informazioni sulla capacità riservata sono disponibili nell'articolo [Prepay for Azure Cosmos DB resources with reserved capacity](cosmos-db-reserved-capacity.md) (Pagamento anticipato per le risorse di Azure Cosmos DB con capacità riservata). È possibile anche acquistare capacità riservata sul [portale di Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come ottimizzare i costi per le risorse di Azure Cosmos DB sono contenute negli articoli seguenti:

* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[Ottimizzazione dei costi delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Cosmos multi-area](optimize-cost-regions.md)
* Informazioni sulla [capacità riservata di Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Informazioni sull'[emulatore di Azure Cosmos DB](local-emulator.md)
