---
title: Ottimizzazione di sviluppo e test in Azure Cosmos DB
description: Questo articolo illustra le diverse opzioni offerte da Cosmos DB per lo sviluppo e il test gratuiti del servizio.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: e6ccf70a085950ebd6b520fa4991220981e4f018
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263646"
---
# <a name="optimizing-for-development-and-testing-in-azure-cosmos-db"></a>Ottimizzazione di sviluppo e test in Azure Cosmos DB

Questo articolo descrive le diverse opzioni per l'uso gratuito di Azure Cosmos DB per lo sviluppo e il test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulatore di Azure Cosmos DB (versione scaricabile in locale)

L'[emulatore di Azure Cosmos DB](local-emulator.md) è una versione scaricabile in locale che emula il servizio cloud di Azure Cosmos DB. Consente di scrivere e testare codice che usa le API di Azure Cosmos DB, anche senza una connessione di rete, senza alcun costo. L'emulatore di Azure Cosmos DB rappresenta un ambiente locale estremamente fedele al servizio cloud per scopi di sviluppo. È possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure. Al momento di distribuire l'applicazione nel cloud, aggiornare la stringa di connessione per connettersi all'endpoint di Azure Cosmos DB nel cloud. Non sono necessarie altre modifiche. È anche possibile eseguire test [configurando una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB](tutorial-setup-ci-cd.md) in Azure DevOps. Per iniziare, è possibile vedere l'articolo [Emulatore di Cosmos DB](local-emulator.md).

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

[Provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) è un'esperienza gratuita che consente di creare database e raccolte e provare a usare Azure Cosmos DB nel cloud. Non è necessario iscriversi ad Azure né pagare alcuna somma. Gli account di prova di Azure Cosmos DB sono disponibili per un periodo limitato, attualmente per 30 giorni. È possibile rinnovarli in qualsiasi momento. Gli account di prova di Azure Cosmos DB semplificano la valutazione di Azure Cosmos DB e la compilazione e il test di un'applicazione tramite argomenti di avvio rapido ed esercitazioni. È possibile creare una demo o eseguire testing unità senza sostenere costi. Con gli account gratuiti di prova di Cosmos DB, è possibile valutare gratuitamente le funzionalità Premium di Cosmos DB, inclusi i modelli chiavi in mano di distribuzione globale, di contratto di servizio e di coerenza. È possibile creare un database con un massimo di 25 contenitori di Azure Cosmos e 10.000 UR/sec di velocità effettiva. È possibile eseguire l'applicazione di esempio senza sottoscrivere un account di Azure e senza usare la carta di credito. Con un account gratuito Cosmos DB di prova è possibile creare un account Azure Cosmos gratuito e usarlo per eseguire un'app in pochi minuti. Per iniziare, vedere la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Account Azure gratuito

Azure Cosmos DB è incluso nell'[account Azure gratuito](https://azure.microsoft.com/free), che offre gratuitamente crediti e risorse di Azure per un periodo di tempo specifico. Per Azure Cosmos DB in particolare, l'account gratuito offre 5 GB di archiviazione e 400 UR di velocità effettiva con provisioning per l'intero anno. Questa esperienza consente a qualsiasi sviluppatore di testare facilmente le funzionalità di Azure Cosmos DB o di integrare questo strumento con altri servizi di Azure a costo zero. Con un account Azure gratuito, si ottiene un credito di 200 dollari da spendere nei primi 30 giorni. Anche se si inizia a usare i servizi, verranno applicati addebiti solo quando si sceglierà di eseguire l'aggiornamento. Per iniziare, visitare la pagina [Account Azure gratuito](https://azure.microsoft.com/free).

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare l'emulatore o gli account Azure Cosmos DB gratuiti con gli articoli seguenti:

* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni [sulla fattura di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione dei costi delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione dei costi delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multiarea](optimize-cost-regions.md)

