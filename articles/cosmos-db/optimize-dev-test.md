---
title: Ottimizzazione di sviluppo e test in Azure Cosmos DB
description: Questo articolo illustra le diverse opzioni offerte da Azure Cosmos DB per lo sviluppo e il test gratuiti del servizio.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246682"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Ottimizzare i costi di sviluppo e test in Azure Cosmos DB

Questo articolo descrive le diverse opzioni per usare Azure Cosmos DB per lo sviluppo e il test gratuitamente, nonché le tecniche per ottimizzare i costi negli account di sviluppo o di test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulatore di Azure Cosmos DB (versione scaricabile in locale)

L'[emulatore di Azure Cosmos DB](local-emulator.md) è una versione scaricabile in locale che emula il servizio cloud di Azure Cosmos DB. Consente di scrivere e testare codice che usa le API di Azure Cosmos DB, anche senza una connessione di rete, senza alcun costo. L'emulatore di Azure Cosmos DB rappresenta un ambiente locale estremamente fedele al servizio cloud per scopi di sviluppo. È possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure. Al momento di distribuire l'applicazione nel cloud, aggiornare la stringa di connessione per connettersi all'endpoint di Azure Cosmos DB nel cloud. Non sono necessarie altre modifiche. È anche possibile eseguire test [configurando una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB](tutorial-setup-ci-cd.md) in Azure DevOps. Per iniziare, è possibile vedere l'articolo [Emulatore di Azure Cosmos DB](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Livello gratuito Azure Cosmos DBAzure Cosmos DB free tier 
Il livello gratuito Azure Cosmos DB semplifica l'avvio, lo sviluppo e il test delle applicazioni o persino l'esecuzione gratuita di carichi di lavoro di produzione di piccole dimensioni. Quando il livello gratuito è abilitato su un account, otterrai i primi 400 RU/s e 5 GB di spazio di archiviazione nell'account gratuito. È inoltre possibile creare un database della velocità effettiva condivisa con 25 contenitori che condividono 400 RU/s a livello di database, tutti coperti dal livello gratuito (limite 5 database di velocità effettiva condivisa in un account di livello gratuito). Il livello gratuito dura a tempo indeterminato per tutta la durata dell'account e include tutti i [vantaggi e le funzionalità](introduction.md#key-benefits) di un normale account di database di Azure Cosmos, tra cui archiviazione e velocità effettiva illimitate, contratti di servizio, disponibilità elevata, distribuzione globale chiavi in tutte le aree di Azure e altro ancora. È possibile avere fino a un account di livello gratuito per ogni sottoscrizione di Azure e deve essere esplicitamente acconsentito durante la creazione dell'account. Per iniziare, [creare un nuovo account con il livello gratuito abilitato](create-cosmosdb-resources-portal.md). Per altri dettagli, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/). 

## <a name="try-azure-cosmos-db-for-free"></a>Prova gratuitamente Azure Cosmos DB

[Prova Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) è un'esperienza gratuita che consente di sperimentare con Azure Cosmos DB nel cloud senza registrarsi per un account Azure o usare la carta di credito. Gli account Try Azure Cosmos DB sono disponibili per un periodo di tempo limitato, attualmente 30 giorni. È possibile rinnovarli in qualsiasi momento. Provare gli account di database Cosmos di Azure semplifica la valutazione di Azure Cosmos DB, la compilazione e il test di un'applicazione o l'uso delle guide introduttive o delle esercitazioni. È anche possibile creare una demo, eseguire unit test o anche creare un account con più aree ed eseguire un'app su di esso senza incorrere in alcun costo. In un account try Azure Cosmos DB è possibile avere un database di velocità effettiva condiviso con un massimo di 25 contenitori e 20.000 RU/s di velocità effettiva o un contenitore con un massimo di 5000 RU/s. Per iniziare, vedere la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Account Azure gratuito

Azure Cosmos DB è incluso nell'[account Azure gratuito](https://azure.microsoft.com/free), che offre gratuitamente crediti e risorse di Azure per un periodo di tempo specifico. Per Azure Cosmos DB in particolare, l'account gratuito offre 5 GB di archiviazione e 400 UR di velocità effettiva sottoposta a provisioning per l'intero anno. Questa esperienza consente a qualsiasi sviluppatore di testare facilmente le funzionalità di Azure Cosmos DB o di integrare questo strumento con altri servizi di Azure a costo zero. Con un account Azure gratuito, si ottiene un credito di 200 dollari da spendere nei primi 30 giorni. Anche se si inizia a usare i servizi, verranno applicati addebiti solo quando si sceglierà di eseguire l'aggiornamento. Per iniziare, visitare la pagina [Account Azure gratuito](https://azure.microsoft.com/free).

## <a name="use-shared-throughput-databases"></a>Usare database di velocità effettiva condivisaUse shared throughput databases

In un [database con velocità effettiva condivisa,](set-throughput.md#set-throughput-on-a-database)tutti i contenitori all'interno del database condividono la velocità effettiva con provisioning (RU/s) del database. Ad esempio, se si esegue il provisioning di un database con 400 RU/s e si dispone di quattro contenitori, tutti e quattro i contenitori condivideranno le 400 RU/s. In un ambiente di sviluppo o test, in cui è possibile accedere a ogni contenitore con minore frequenza e quindi richiedere un numero inferiore a minime di 400 RU/s, l'inserimento di contenitori in un database con velocità effettiva condivisa consente di ottimizzare i costi. 

Si supponga, ad esempio, che l'account di sviluppo o di test disponga di quattro contenitori. Se si creano quattro contenitori con velocità effettiva dedicata (minimo 400 RU/s), le RU/s totali saranno 1600 RU/s. Al contrario, se si crea un database con velocità effettiva condivisa (minimo 400 RU/s) e si inserisceno i contenitori, le RU/s totali saranno solo 400 RU/s. In generale, i database della velocità effettiva condivisa sono ideali per gli scenari in cui non è necessaria una velocità effettiva garantita in un singolo contenitore.  Altre informazioni sui [database della velocità effettiva condivisa.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare l'emulatore o gli account Azure Cosmos DB gratuiti con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)

