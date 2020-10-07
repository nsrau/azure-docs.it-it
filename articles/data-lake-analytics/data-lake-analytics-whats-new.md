---
title: Modifiche recenti apportate a Data Lake Analytics
description: Questo articolo include un elenco in continuo aggiornamento delle modifiche recenti apportate al servizio Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88263181"
---
# <a name="whats-new-in-data-lake-analytics"></a>Novità di Data Lake Analytics

Il servizio Azure Data Lake Analytics non viene aggiornato periodicamente, ma solo per determinati componenti. Per rimanere aggiornati con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Notifica della versione beta dell'anteprima del componente chiave
- Informazioni importanti sulla versione del componente, ad esempio l'elenco delle versioni disponibili del componente, la versione predefinita corrente e così via.


## <a name="notification-of-key-component-beta-preview"></a>Notifica dell'anteprima beta del componente chiave

Nessuna versione beta del componente chiave disponibile per l'anteprima. 

## <a name="u-sql-runtime"></a>Runtime U-SQL

Il runtime U-SQL di Azure Data Lake, tra cui compilatore, ottimizzatore e gestore di processi, si occupa di elaborare il codice U-SQL.

Il processo di Azure Data Lake Analytics inviato da qualsiasi strumento userà il runtime predefinito attualmente disponibile nell'ambiente di produzione. 

La versione del runtime verrà aggiornata non periodicamente. Il runtime precedente verrà mantenuto disponibile per un certo periodo di tempo. Quando una nuova versione beta è pronta per l'anteprima, sarà resa disponibile.

> [!CAUTION]
> - La scelta di un runtime diverso da quello predefinito potrebbe interrompere i processi U-SQL. È consigliabile evitare di usare queste versioni non predefinite per l'ambiente di produzione. Usarle solo per i test.
> - La versione del runtime non predefinita ha un ciclo di vita fisso. Scadrà automaticamente.

La versione seguente è la versione del runtime predefinita corrente.

- release-20200124live_adl_16283022_2

Per informazioni su come risolvere gli errori del runtime U-SQL, vedere [Risolvere gli errori del runtime U-SQL](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics ora usa **.NET Framework v4.7.2**. 

Se il codice di script U-SQL di Azure Data Lake Analytics usa assembly personalizzati basati su librerie .NET, verificare la presenza di eventuali interruzioni nel codice.

Per informazioni su come risolvere i problemi relativi a un aggiornamento di .NET, vedere [Risolvere i problemi relativi a un aggiornamento di .NET](runtime-troubleshoot.md).

## <a name="release-note"></a>Note sulla versione

Per i dettagli sugli aggiornamenti recenti, vedere le [note sulla versione di Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Passaggi successivi

* Introduzione ad Azure Data Lake Analytics con [portale di Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Interfaccia della riga di comando](data-lake-analytics-get-started-cli.md)

