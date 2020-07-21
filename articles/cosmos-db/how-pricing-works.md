---
title: Modello di determinazione dei prezzi di Azure Cosmos DB
description: Questo articolo illustra il modello di determinazione dei prezzi di Azure Cosmos DB e come questo semplifica la gestione e la pianificazione dei costi.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: d36b4fd433af716ebd97d88d05922d94bd74c309
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523537"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modello di determinazione dei prezzi di Azure Cosmos DB

Il modello di determinazione dei prezzi di Azure Cosmos DB semplifica la gestione e la pianificazione dei costi. Con Azure Cosmos DB viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione usate.

* **Velocità effettiva con provisioning**: la [velocità effettiva con provisioning](how-to-choose-offer.md) (detta anche velocità effettiva riservata) garantisce prestazioni elevate su qualsiasi scala. Specificando la velocità effettiva (UR/s) necessaria, Azure Cosmos DB dedica le risorse necessarie per garantire la velocità effettiva configurata. Verrà quindi addebitata su base oraria la velocità effettiva massima sottoposta a provisioning per un'ora specifica. Puoi eseguire manualmente il provisioning della velocità effettiva o usare la [scalabilità](provision-throughput-autoscale.md)automatica.

   > [!NOTE]
   > Poiché il modello di velocità effettiva con provisioning dedica risorse al contenitore o al database, verrà addebitata la velocità effettiva con provisioning anche se non vengono eseguiti carichi di lavoro.

* **Archiviazione utilizzata**: viene addebitata una tariffa fissa per la quantità totale di spazio di archiviazione (GB) utilizzata per i dati e gli indici per una determinata ora.

La velocità effettiva sottoposta a provisioning, specificata come [unità richiesta](request-units.md) al secondo o UR/s, consente di leggere o scrivere dati nei contenitori o database. È possibile [eseguire il provisioning della velocità effettiva in un database o in un contenitore](set-throughput.md). In base alle esigenze del carico di lavoro, è possibile ridurre o aumentare la velocità effettiva in qualsiasi momento. I prezzi di Azure Cosmos DB sono elastici e proporzionali alla velocità effettiva configurata in un database o contenitore. I valori minimi di velocità effettiva e archiviazione e gli incrementi di scalabilità offrono una gamma completa di prezzi rispetto a uno spettro di elasticità per tutti i segmenti di clienti, da contenitori su scala ridotta a contenitori su larga scala. Ogni database o contenitore viene fatturato su base oraria per la velocità effettiva sottoposta a provisioning nelle unità di 100 UR/s, con un minimo di 400 UR/s e spazio di archiviazione usato in GB. A differenza della velocità effettiva sottoposta a provisioning, l'archiviazione è fatturata in base al consumo, Ovvero, non è necessario riservare alcuna archiviazione in anticipo. Verranno addebitate le sole risorse di archiviazione usate.

Per altre informazioni, vedere la [pagina dei prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [Informazioni sulla fattura di Azure Cosmos DB](understand-your-bill.md).

Il modello di prezzi in Azure Cosmos DB è coerente in tutte le API. Per altre informazioni, vedere [Costo totale di proprietà (TCO) con Azure Cosmos DB](total-cost-ownership.md). È necessaria una velocità effettiva minima in un database o in un contenitore per garantire i contratti di contratto ed è possibile aumentare o ridurre la velocità effettiva con provisioning per ogni 100 ur/sec.

Se si distribuisce l'account Azure Cosmos DB in un'area non governativa degli Stati Uniti, attualmente il prezzo minimo per entrambi i database e la velocità effettiva basata su contenitori è di circa 24 dollari al mese. I prezzi variano a seconda dell'area in uso. per informazioni aggiornate sui prezzi, vedere la pagina relativa ai [prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Se il carico di lavoro usa più contenitori, è possibile ottimizzare i costi tramite la velocità effettiva a livello di database, che consente di disporre di un numero qualsiasi di contenitori in un database che condividono la velocità effettiva. La tabella seguente riepiloga la velocità effettiva sottoposta a provisioning e i costi per diverse entità:

|**Entità**  | **Velocità effettiva minima** |**Incrementi di scala** |**Ambito di provisioning** |
|---------|---------|---------|-------|
|Database    | 400 UR/sec    | 100 ur/sec   |La velocità effettiva viene riservata per il database e viene condivisa dai contenitori all'interno del database |
|Contenitore     | 400 UR/sec   | 100 ur/sec  |La velocità effettiva viene riservata per un contenitore specifico |

Come illustrato nella tabella precedente, la velocità effettiva minima in Azure Cosmos DB inizia a un prezzo di circa 24 dollari al mese. Se si inizia con la velocità effettiva minima e si aumentano le prestazioni nel tempo per supportare i carichi di lavoro di produzione, i costi aumenteranno senza problemi, con incrementi di circa 6 dollari al mese. I prezzi variano a seconda dell'area in uso. per informazioni aggiornate sui prezzi, vedere la pagina relativa ai [prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Il modello tariffario<di prezzi di Azure Cosmos DB è elastico e prevede aumenti o riduzioni graduali man mano che si esegue un ridimensionamento verso l'alto o verso il basso.

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

Azure Cosmos DB offre molte opzioni gratuite per gli sviluppatori. Tali opzioni includono:

* **Azure Cosmos dB livello gratuito**: Azure Cosmos dB livello gratuito semplifica l'avvio, lo sviluppo e il test delle applicazioni o anche l'esecuzione gratuita di carichi di lavoro di produzione di piccole dimensioni. Quando il livello gratuito è abilitato per un account, si otterranno le prime 400 ur/sec e 5 GB di spazio di archiviazione nell'account gratuito, per la durata dell'account. È possibile avere fino a un account di livello gratuito per ogni sottoscrizione di Azure ed è necessario acconsentire esplicitamente al momento della creazione dell'account. Per iniziare, [creare un nuovo account in portale di Azure con il livello gratuito abilitato](create-cosmosdb-resources-portal.md) o usare un [modello ARM](manage-sql-with-resource-manager.md#free-tier).

* **Account Azure gratuito**: Azure offre un [livello gratuito](https://azure.microsoft.com/free/) che ti offre $200 di crediti di Azure per i primi 30 giorni e una quantità limitata di servizi gratuiti per 12 mesi. Per altre informazioni, vedere [Account gratuito di Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB è incluso nell'account Azure gratuito. In particolare per Azure Cosmos DB, questo account gratuito offre archiviazione da 5 GB e 400 ur/sec di velocità effettiva con provisioning per l'intero anno.

* **Prova gratuitamente Azure Cosmos DB**: Azure Cosmos DB offre un'esperienza limitata al tempo usando try Azure Cosmos DB per gli account gratuiti. È possibile creare un account Azure Cosmos DB, creare database e raccolte ed eseguire un'applicazione di esempio usando le opzioni di avvio rapido e le esercitazioni offerte. È possibile eseguire l'applicazione di esempio senza sottoscrivere alcun account di Azure né usare la carta di credito. L'opzione [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) consente di usare Azure Cosmos DB per un mese, con la possibilità di rinnovare l'account un numero illimitato di volte.

* **Azure Cosmos DB Emulator**: Azure Cosmos DB Emulator fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. L'emulatore è offerto senza costi aggiuntivi e con un'elevata fedeltà al servizio cloud. Usando l'emulatore di Azure Cosmos DB è possibile sviluppare e testare le applicazioni in locale, senza creare una sottoscrizione di Azure né sostenere alcun costo. È possibile sviluppare applicazioni usando l'emulatore in locale prima del passaggio in produzione. Quando si è soddisfatti delle funzionalità dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud e risparmiare in modo significativo sui costi. Per altre informazioni e dettagli sull'emulatore, vedere l'articolo [Uso di Azure Cosmos DB per sviluppo e test](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Prezzi con capacità riservata

La [capacità di riserva](cosmos-db-reserved-capacity.md) di Azure Cosmos DB permette di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per un anno o tre anni. È possibile ridurre significativamente i costi con impegni inziali di un anno o di tre anni e risparmiare tra il 20 e il 65% rispetto al prezzo normale. La capacità riservata di Azure Cosmos DB aiuta a ridurre i costi pagando anticipatamente per la velocità effettiva con provisioning (UR/s) per un periodo di uno o tre anni e ottenendo uno sconto sulla velocità effettiva con provisioning. 

La capacità riservata consente di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato del runtime delle risorse di Azure Cosmos DB. La capacità riservata è disponibile in modo coerente per tutte le API, incluso MongoDB, Cassandra, SQL, Gremlin e Azure Tables, per tutte le aree geografiche del mondo. Altre informazioni sulla capacità riservata sono disponibili nell'articolo [Prepay for Azure Cosmos DB resources with reserved capacity](cosmos-db-reserved-capacity.md) (Pagamento anticipato per le risorse di Azure Cosmos DB con capacità riservata). È possibile anche acquistare capacità riservata sul [portale di Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come ottimizzare i costi per le risorse di Azure Cosmos DB sono contenute negli articoli seguenti:

* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Cosmos multi-area](optimize-cost-regions.md)
* Informazioni sulla [capacità riservata di Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Informazioni sull'[emulatore di Azure Cosmos DB](local-emulator.md)
