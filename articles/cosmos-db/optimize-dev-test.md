---
title: Ottimizzazione di sviluppo e test in Azure Cosmos DB
description: Questo articolo illustra le diverse opzioni offerte da Azure Cosmos DB per lo sviluppo e il test gratuiti del servizio.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754888"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Ottimizzare i costi di sviluppo e test in Azure Cosmos DB

Questo articolo descrive le diverse opzioni per l'uso gratuito di Azure Cosmos DB per lo sviluppo e il test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulatore di Azure Cosmos DB (versione scaricabile in locale)

L'[emulatore di Azure Cosmos DB](local-emulator.md) è una versione scaricabile in locale che emula il servizio cloud di Azure Cosmos DB. Consente di scrivere e testare codice che usa le API di Azure Cosmos DB, anche senza una connessione di rete, senza alcun costo. L'emulatore di Azure Cosmos DB rappresenta un ambiente locale estremamente fedele al servizio cloud per scopi di sviluppo. È possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure. Al momento di distribuire l'applicazione nel cloud, aggiornare la stringa di connessione per connettersi all'endpoint di Azure Cosmos DB nel cloud. Non sono necessarie altre modifiche. È anche possibile eseguire test [configurando una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB](tutorial-setup-ci-cd.md) in Azure DevOps. Per iniziare, è possibile vedere l'articolo [Emulatore di Azure Cosmos DB](local-emulator.md).

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

[Provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) è un'esperienza gratuita che consente di creare database e raccolte e provare a usare Azure Cosmos DB nel cloud. Non è necessario iscriversi ad Azure né pagare alcuna somma. Gli account di prova di Azure Cosmos DB sono disponibili per un periodo limitato, attualmente per 30 giorni. È possibile rinnovarli in qualsiasi momento. Gli account di prova di Azure Cosmos DB semplificano la valutazione di Azure Cosmos DB e la compilazione e il test di un'applicazione tramite argomenti di avvio rapido ed esercitazioni. È possibile creare una demo o eseguire testing unità senza sostenere costi. Con gli account gratuiti di prova di Azure Cosmos DB, è possibile valutare gratuitamente le funzionalità Premium di Azure Cosmos DB, inclusi i modelli chiavi in mano di distribuzione globale, di contratto di servizio e di coerenza. È possibile creare un database con un massimo di 25 contenitori di Azure Cosmos e 10.000 UR/sec di velocità effettiva. È possibile eseguire l'applicazione di esempio senza sottoscrivere alcun account di Azure né usare la carta di credito. Con un account gratuito Azure Cosmos DB di prova è possibile creare un account Azure Cosmos gratuito e usarlo per eseguire un'app in pochi minuti. Per iniziare, vedere la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Account Azure gratuito

Azure Cosmos DB è incluso nell'[account Azure gratuito](https://azure.microsoft.com/free), che offre gratuitamente crediti e risorse di Azure per un periodo di tempo specifico. Per Azure Cosmos DB in particolare, l'account gratuito offre 5 GB di archiviazione e 400 UR di velocità effettiva sottoposta a provisioning per l'intero anno. Questa esperienza consente a qualsiasi sviluppatore di testare facilmente le funzionalità di Azure Cosmos DB o di integrare questo strumento con altri servizi di Azure a costo zero. Con un account Azure gratuito, si ottiene un credito di 200 dollari da spendere nei primi 30 giorni. Anche se si inizia a usare i servizi, verranno applicati addebiti solo quando si sceglierà di eseguire l'aggiornamento. Per iniziare, visitare la pagina [Account Azure gratuito](https://azure.microsoft.com/free).

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare l'emulatore o gli account Azure Cosmos DB gratuiti con gli articoli seguenti:

* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multiarea](optimize-cost-regions.md)

