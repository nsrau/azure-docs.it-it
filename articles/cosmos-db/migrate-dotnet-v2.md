---
title: Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB .NET SDK 2,0 (Microsoft. Azure. Cosmos)
description: Informazioni su come aggiornare l'applicazione .NET esistente da V1 SDK a .NET SDK v2 per l'API di base (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550085"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> È importante notare che la versione V3 di .NET SDK è attualmente disponibile e che un piano di migrazione da V2 a V3 è disponibile [qui](migrate-dotnet-v3.md). Per informazioni su Azure Cosmos DB .NET SDK v2, vedere le [Note sulla versione](sql-api-sdk-dotnet.md), il [repository .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2), i [suggerimenti sulle prestazioni](performance-tips.md)di .NET SDK v2 e la [Guida alla risoluzione dei problemi](troubleshoot-dot-net-sdk.md).
>

Questo articolo illustra alcune considerazioni per l'aggiornamento dell'applicazione .NET v1 esistente a Azure Cosmos DB .NET SDK v2 per l'API di base (SQL). Azure Cosmos DB .NET SDK v2 corrisponde allo `Microsoft.Azure.DocumentDB` spazio dei nomi. È possibile usare le informazioni fornite in questo documento se si esegue la migrazione dell'applicazione da una delle seguenti Azure Cosmos DB piattaforme .NET per usare l'SDK v2 `Microsoft.Azure.Cosmos` :

* Azure Cosmos DB .NET Framework V1 SDK per l'API SQL
* Azure Cosmos DB .NET Core SDK V1 per l'API SQL

## <a name="whats-available-in-the-net-v2-sdk"></a>Funzionalità disponibili in .NET v2 SDK

L'SDK v2 contiene molti miglioramenti di usabilità e prestazioni, tra cui:

* Supporto per la modalità diretta TCP per client non Windows
* Supporto per la scrittura in più aree
* Miglioramenti delle prestazioni delle query
* Supporto per le raccolte geospaziali/Geometry e l'indicizzazione
* Miglioramenti migliorati per la diagnostica del trasporto diretta/TCP
* Aggiornamenti nello stack di trasporto TCP diretto per ridurre il numero di connessioni stabilite
* Miglioramenti della riduzione della latenza nel RequestTimeout

La maggior parte della logica di ripetizione dei tentativi e dei livelli inferiori dell'SDK rimane sostanzialmente invariata.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Vantaggi della migrazione a .NET v2 SDK

Oltre ai numerosi miglioramenti apportati alle prestazioni, i nuovi investimenti per le funzionalità apportati nell'SDK più recente non verranno restituiti a versioni precedenti.

Inoltre, gli SDK precedenti verranno sostituiti dalle versioni più recenti e l'SDK V1 verrà in [modalità di manutenzione](sql-api-sdk-dotnet.md). Per un'esperienza di sviluppo ottimale, è consigliabile eseguire la migrazione dell'applicazione a una versione più recente dell'SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Principali modifiche da V1 SDK a V2 SDK

### <a name="direct-mode--tcp"></a>Modalità diretta + TCP

.NET v2 SDK supporta ora sia la modalità diretta che quella del gateway. La modalità diretta supporta la connettività tramite il protocollo TCP e offre prestazioni migliori in quanto si connette direttamente alle repliche back-end con un minor numero di hop di rete.

Per ulteriori informazioni, vedere la [Guida alle modalità di connettività di Azure Cosmos DB SQL SDK](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formattazione del token di sessione

V2 SDK non usa più il formato del token di sessione *semplice* usato nella versione 1, ma l'SDK usa la formattazione *vettoriale* . Il formato deve essere convertito quando si passa all'applicazione client con versioni diverse, poiché i formati non sono intercambiabili.

Per ulteriori informazioni, vedere [conversione di formati di token di sessione in .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Uso dell'SDK del processore dei feed delle modifiche .NET

La libreria del processore dei feed delle modifiche .NET 2.1. x richiede `Microsoft.Azure.DocumentDB` 2,0 o versione successiva.

La libreria 2.1. x presenta le modifiche principali seguenti:

* Miglioramenti alla stabilità e alla diagnostica
* Gestione migliorata degli errori e delle eccezioni
* Supporto aggiuntivo per le raccolte di lease partizionate
* Estensioni avanzate per implementare l' `ChangeFeedDocument` interfaccia e la classe per la gestione e la traccia aggiuntive degli errori
* Aggiunta del supporto per l'uso di un archivio personalizzato per la persistenza dei token di continuazione per partizione

Per ulteriori informazioni, vedere le [Note sulla versione](sql-api-sdk-dotnet-changefeed.md)della libreria del processore dei feed delle modifiche.

### <a name="using-the-bulk-executor-library"></a>Uso della libreria dell'esecutore bulk

La libreria di Executor bulk V2 presenta attualmente una dipendenza da Azure Cosmos DB .NET SDK 2.5.1 o versione successiva.

Per ulteriori informazioni, vedere le note sulla versione [Azure Cosmos DB bulk Executor Library](bulk-executor-overview.md) e le note sulla [versione](sql-api-sdk-bulk-executor-dot-net.md)di .NET bulk Executor Library.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sui [suggerimenti sulle prestazioni](sql-api-get-started.md) usando Azure Cosmos DB per l'API SQL V2 per ottimizzare l'applicazione per ottenere prestazioni massime
* Altre informazioni sulle [operazioni che è possibile eseguire con l'SDK v2](sql-api-dotnet-samples.md)