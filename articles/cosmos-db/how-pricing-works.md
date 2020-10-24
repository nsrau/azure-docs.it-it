---
title: Modello di determinazione dei prezzi di Azure Cosmos DB
description: Questo articolo illustra il modello di determinazione dei prezzi di Azure Cosmos DB e come questo semplifica la gestione e la pianificazione dei costi.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: b7d54dfe2efa82aaa650168c476da03d89fe4527
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490493"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modello di determinazione dei prezzi di Azure Cosmos DB

Il modello di determinazione dei prezzi di Azure Cosmos DB semplifica la gestione e la pianificazione dei costi. Con Azure Cosmos DB si paga per le operazioni eseguite sul database e per lo spazio di archiviazione utilizzato dai dati.

- **Operazioni di database**: il modo in cui vengono addebitate le operazioni di database dipende dal tipo di account Azure Cosmos usato.

  - **Velocità effettiva con provisioning**: la [velocità effettiva con provisioning](set-throughput.md) (detta anche velocità effettiva riservata) fornisce prestazioni elevate su qualsiasi scala. È possibile specificare la velocità effettiva necessaria in [unità richiesta](request-units.md) al secondo (UR/sec) e Azure Cosmos DB dedicare le risorse necessarie per fornire la velocità effettiva configurata. È possibile [eseguire il provisioning della velocità effettiva in un database o in un contenitore](set-throughput.md). In base alle esigenze del carico di lavoro, è possibile ridimensionare la velocità effettiva in qualsiasi momento o usare la [scalabilità](provision-throughput-autoscale.md) automatica (anche se è necessaria una velocità effettiva minima in un database o in un contenitore per garantire i contratti di contratto). Verrà quindi addebitata su base oraria la velocità effettiva massima sottoposta a provisioning per un'ora specifica.

   > [!NOTE]
   > Poiché il modello di velocità effettiva con provisioning dedica risorse al contenitore o al database, verrà addebitata la velocità effettiva di cui è stato effettuato il provisioning, anche se non vengono eseguiti carichi di lavoro.

  - Senza **Server**: in modalità senza [Server](serverless.md) non è necessario effettuare il provisioning di una velocità effettiva durante la creazione di risorse nell'account Azure Cosmos. Alla fine del periodo di fatturazione, viene addebitata la quantità di unità richiesta che è stata utilizzata dalle operazioni del database.

- **Archiviazione**: viene addebitata una tariffa fissa per la quantità totale di spazio di archiviazione (in GB) utilizzata dai dati e dagli indici per un'ora specifica. L'archiviazione viene fatturata in base a un consumo, quindi non è necessario riservare alcuna archiviazione in anticipo. Verranno addebitate le sole risorse di archiviazione usate.

Il modello di prezzi in Azure Cosmos DB è coerente in tutte le API. Per ulteriori informazioni, vedere la [pagina relativa ai prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), informazioni sulla [fattura Azure Cosmos DB](understand-your-bill.md) e sul [modo in cui Azure Cosmos DB modello di determinazione prezzi è conveniente per i clienti](total-cost-ownership.md).

Se si distribuisce l'account Azure Cosmos DB in un'area non governativa degli Stati Uniti, è previsto un prezzo minimo per la velocità effettiva del database e del contenitore in modalità di velocità effettiva con provisioning. Non esiste alcun prezzo minimo in modalità senza server. I prezzi variano a seconda dell'area in uso. per informazioni aggiornate sui prezzi, vedere la pagina relativa ai [prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

Azure Cosmos DB offre molte opzioni gratuite per gli sviluppatori. Tali opzioni includono:

* **Azure Cosmos dB livello gratuito**: Azure Cosmos dB livello gratuito semplifica l'avvio, lo sviluppo e il test delle applicazioni o anche l'esecuzione gratuita di carichi di lavoro di produzione di piccole dimensioni. Quando il livello gratuito è abilitato per un account, si otterranno le prime 400 ur/sec e 5 GB di spazio di archiviazione nell'account gratuito, per la durata dell'account. È possibile avere fino a un account di livello gratuito per ogni sottoscrizione di Azure ed è necessario acconsentire esplicitamente al momento della creazione dell'account. Per iniziare, [creare un nuovo account in portale di Azure con il livello gratuito abilitato](create-cosmosdb-resources-portal.md) o usare un [modello ARM](./manage-with-templates.md#free-tier).

* **Account Azure gratuito**: Azure offre un [livello gratuito](https://azure.microsoft.com/free/) che ti offre $200 di crediti di Azure per i primi 30 giorni e una quantità limitata di servizi gratuiti per 12 mesi. Per altre informazioni, vedere [Account gratuito di Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB è incluso nell'account Azure gratuito. In particolare per Azure Cosmos DB, questo account gratuito offre archiviazione da 5 GB e 400 ur/sec di velocità effettiva con provisioning per l'intero anno.

* **Prova gratuitamente Azure Cosmos DB**: Azure Cosmos DB offre un'esperienza limitata al tempo usando try Azure Cosmos DB per gli account gratuiti. È possibile creare un account Azure Cosmos DB, creare database e raccolte ed eseguire un'applicazione di esempio usando le opzioni di avvio rapido e le esercitazioni offerte. È possibile eseguire l'applicazione di esempio senza sottoscrivere alcun account di Azure né usare la carta di credito. L'opzione [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) consente di usare Azure Cosmos DB per un mese, con la possibilità di rinnovare l'account un numero illimitato di volte.

* **Azure Cosmos DB Emulator**: Azure Cosmos DB Emulator fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. L'emulatore è offerto senza costi aggiuntivi e con un'elevata fedeltà al servizio cloud. Usando l'emulatore di Azure Cosmos DB è possibile sviluppare e testare le applicazioni in locale, senza creare una sottoscrizione di Azure né sostenere alcun costo. È possibile sviluppare applicazioni usando l'emulatore in locale prima del passaggio in produzione. Quando si è soddisfatti delle funzionalità dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud e risparmiare in modo significativo sui costi. Per altre informazioni e dettagli sull'emulatore, vedere l'articolo [Uso di Azure Cosmos DB per sviluppo e test](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Prezzi con capacità riservata

Azure Cosmos DB [capacità riservata](cosmos-db-reserved-capacity.md) ti permette di risparmiare denaro quando usi la modalità di velocità effettiva con provisioning prepagando le risorse Azure Cosmos DB per un anno o tre anni. È possibile ridurre significativamente i costi con impegni inziali di un anno o di tre anni e risparmiare tra il 20 e il 65% rispetto al prezzo normale. La capacità riservata di Azure Cosmos DB aiuta a ridurre i costi pagando anticipatamente per la velocità effettiva con provisioning (UR/s) per un periodo di uno o tre anni e ottenendo uno sconto sulla velocità effettiva con provisioning. 

La capacità riservata consente di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato del runtime delle risorse di Azure Cosmos DB. La capacità riservata è disponibile in modo coerente per tutte le API, incluso MongoDB, Cassandra, SQL, Gremlin e Azure Tables, per tutte le aree geografiche del mondo. Altre informazioni sulla capacità riservata sono disponibili nell'articolo [Prepay for Azure Cosmos DB resources with reserved capacity](cosmos-db-reserved-capacity.md) (Pagamento anticipato per le risorse di Azure Cosmos DB con capacità riservata). È possibile anche acquistare capacità riservata sul [portale di Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come ottimizzare i costi per le risorse di Azure Cosmos DB sono contenute negli articoli seguenti:

* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](./optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Cosmos multi-area](optimize-cost-regions.md)
* Informazioni sulla [capacità riservata di Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Informazioni sull'[emulatore di Azure Cosmos DB](local-emulator.md)