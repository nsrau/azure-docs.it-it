---
title: Ottimizzazione di sviluppo e test in Azure Cosmos DB
description: Questo articolo illustra le diverse opzioni offerte da Azure Cosmos DB per lo sviluppo e il test gratuiti del servizio.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 3fe5ea98f8db633eed7ce4e2c0ac0cafa56408ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82194517"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Ottimizzare i costi di sviluppo e test in Azure Cosmos DB

Questo articolo descrive le diverse opzioni per usare Azure Cosmos DB per lo sviluppo e il test senza costi aggiuntivi, oltre a tecniche per ottimizzare i costi negli account di sviluppo o di test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulatore di Azure Cosmos DB (versione scaricabile in locale)

L'[emulatore di Azure Cosmos DB](local-emulator.md) è una versione scaricabile in locale che emula il servizio cloud di Azure Cosmos DB. Consente di scrivere e testare codice che usa le API di Azure Cosmos DB, anche senza una connessione di rete, senza alcun costo. L'emulatore di Azure Cosmos DB rappresenta un ambiente locale estremamente fedele al servizio cloud per scopi di sviluppo. È possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure. Al momento di distribuire l'applicazione nel cloud, aggiornare la stringa di connessione per connettersi all'endpoint di Azure Cosmos DB nel cloud. Non sono necessarie altre modifiche. È anche possibile eseguire test [configurando una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB](tutorial-setup-ci-cd.md) in Azure DevOps. Per iniziare, è possibile vedere l'articolo [Emulatore di Azure Cosmos DB](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Livello gratuito Azure Cosmos DB

Azure Cosmos DB livello gratuito semplifica la creazione, lo sviluppo e il test delle applicazioni o anche l'esecuzione gratuita di piccoli carichi di lavoro di produzione. Quando il livello gratuito è abilitato per un account, otterrai le prime 400 ur/sec e 5 GB di spazio di archiviazione gratuitamente nell'account. È anche possibile creare un database con velocità effettiva condivisa con 25 contenitori che condividono 400 ur/s a livello di database, tutti coperti da livello gratuito (limite 5 database di velocità effettiva condivisa in un account di livello gratuito). Il livello gratuito dura per un periodo illimitato per la durata dell'account ed è dotato di tutti i [vantaggi e le funzionalità](introduction.md#key-benefits) di un normale account Azure Cosmos DB, tra cui archiviazione e velocità effettiva illimitate (UR/sec), SLA, disponibilità elevata, distribuzione globale chiavi in mano in tutte le aree di Azure e altro ancora. È possibile avere fino a un account di livello gratuito per ogni sottoscrizione di Azure ed è necessario acconsentire esplicitamente al momento della creazione dell'account. Per iniziare, [creare un nuovo account in portale di Azure con il livello gratuito abilitato](create-cosmosdb-resources-portal.md) o usare un [modello ARM](manage-sql-with-resource-manager.md#free-tier). Per altri dettagli, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

[Prova Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) gratuitamente è un'esperienza gratuita che ti permette di sperimentare Azure Cosmos DB nel cloud senza iscriverti per ottenere un account Azure o usare la tua carta di credito. Gli account di prova Azure Cosmos DB sono disponibili per un periodo di tempo limitato, attualmente 30 giorni. È possibile rinnovarli in qualsiasi momento. Prova Azure Cosmos DB account semplifica la valutazione Azure Cosmos DB, la compilazione e il test di un'applicazione o l'uso delle guide introduttive o delle esercitazioni. È anche possibile creare una demo, eseguire unit test o persino creare un account a più aree ed eseguire un'app su di essa senza sostenere costi. In un account try Azure Cosmos DB è possibile avere un database con velocità effettiva condivisa con un massimo di 25 contenitori e 20.000 UR/s di velocità effettiva oppure un contenitore con un massimo di 5000 ur/sec. Per iniziare, vedere la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Account Azure gratuito

Azure Cosmos DB è incluso nell'[account Azure gratuito](https://azure.microsoft.com/free), che offre gratuitamente crediti e risorse di Azure per un periodo di tempo specifico. Per Azure Cosmos DB in particolare, l'account gratuito offre 5 GB di archiviazione e 400 UR di velocità effettiva sottoposta a provisioning per l'intero anno. Questa esperienza consente a qualsiasi sviluppatore di testare facilmente le funzionalità di Azure Cosmos DB o di integrare questo strumento con altri servizi di Azure a costo zero. Con un account Azure gratuito, si ottiene un credito di 200 dollari da spendere nei primi 30 giorni. Anche se si inizia a usare i servizi, verranno applicati addebiti solo quando si sceglierà di eseguire l'aggiornamento. Per iniziare, visitare la pagina [Account Azure gratuito](https://azure.microsoft.com/free).

## <a name="use-shared-throughput-databases"></a>Usare i database con velocità effettiva condivisa

In un [database con velocità effettiva condivisa](set-throughput.md#set-throughput-on-a-database)tutti i contenitori all'interno del database condividono la velocità effettiva con provisioning (UR/sec) del database. Se, ad esempio, si esegue il provisioning di un database con 400 ur/s e si dispone di quattro contenitori, tutti e quattro i contenitori condivideranno il 400 ur/sec. In un ambiente di sviluppo o di testing, in cui è possibile accedere a ogni contenitore con minore frequenza e quindi richiedere un valore inferiore al minimo di 400 ur/sec, l'inserimento di contenitori in un database di velocità effettiva condivisa può contribuire a ottimizzare i costi.

Si supponga, ad esempio, che l'account di sviluppo o test disponga di quattro contenitori. Se si creano quattro contenitori con una velocità effettiva dedicata (almeno 400 ur/sec), le UR/sec totali saranno 1600 ur/sec. Al contrario, se si crea un database con velocità effettiva condivisa (minimo 400 ur/sec) e si inseriscono i contenitori, il totale delle UR/sec sarà solo 400 ur/sec. In generale, i database con velocità effettiva condivisa sono ottimi per gli scenari in cui non è necessaria una velocità effettiva garantita per un singolo contenitore.  Altre informazioni sui [database con velocità effettiva condivisa.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare l'emulatore o gli account Azure Cosmos DB gratuiti con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)
