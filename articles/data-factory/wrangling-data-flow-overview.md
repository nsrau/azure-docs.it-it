---
title: Wrangling data flows in Azure Data Factory
description: Panoramica dei flussi di dati wrangling in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408996"
---
# <a name="what-are-wrangling-data-flows"></a>Che cosa sono i flussi di dati wrangling?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le organizzazioni devono eseguire la preparazione e il wrangling dei dati per un'analisi accurata di dati complessi che continuano a crescere ogni giorno. La preparazione dei dati è necessaria in modo che le organizzazioni possano utilizzare i dati in vari processi aziendali e ridurre il time-to-value.

I flussi di dati wrangling in Azure Data Factory consentono di eseguire una preparazione dei dati senza codice su scala cloud in modo iterativo. I flussi di dati wrangling si integrano con [Power Query Online](https://docs.microsoft.com/power-query/) e rendono disponibili le funzioni di Power Query M per gli utenti di data factory.

Il flusso di dati di wrangling converte M generato da Power Query Online Mashup Editor in codice spark per l'esecuzione della scalabilità cloud.

I flussi di dati wrangling sono particolarmente utili per i data engineer o per gli "integratori di dati dei cittadini".

## <a name="use-cases"></a>Casi d'uso

### <a name="fast-interactive-data-exploration-and-preparation"></a>Esplorazione e preparazione interattiva rapida dei dati

Più data engineer e integratori di dati per i cittadini possono esplorare e preparare in modo interattivo i set di dati su scala cloud. Con l'aumento del volume, la varietà e la velocità dei dati nei data lake, gli utenti hanno bisogno di un modo efficace per esplorare e preparare i set di dati. Ad esempio, potrebbe essere necessario creare un set di dati che "ha tutte le informazioni demografiche dei clienti per i nuovi clienti dal 2017". Non si esegue il mapping a una destinazione nota. Si stanno esplorando, disputando e preparando i set di dati per soddisfare un requisito prima di pubblicarlo nel lago. I flussi di dati wrangling vengono spesso utilizzati per scenari di analisi meno formali. I set di dati preparati possono essere usati per eseguire trasformazioni e operazioni di apprendimento automatico a valle.

### <a name="code-free-agile-data-preparation"></a>Preparazione dei dati agile senza codice

Gli integratori di dati dei cittadini dedicano più del 60% del loro tempo alla ricerca e alla preparazione dei dati. Stanno cercando di farlo in modo senza codice per migliorare la produttività operativa. Consentire agli integratori di dati dei cittadini di arricchire, modellare e pubblicare dati utilizzando strumenti noti come Power Query Online in modo scalabile migliora drasticamente la produttività. Il flusso di dati in Azure Data Factory consente al familiare editor di mashup di Power Query Online di consentire agli integratori di dati dei cittadini di correggere rapidamente gli errori, standardizzare i dati e produrre dati di alta qualità per supportare le decisioni aziendali.

### <a name="data-validation"></a>Convalida dei dati

Analizza visivamente i tuoi dati in modo privo di codice per rimuovere eventuali outlier, anomalie e conformarlo a una forma per un'analisi rapida.

## <a name="supported-sources"></a>Fonti supportate

| Connettore | Formato dati | Tipo di autenticazione |
| -- | -- | --|
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md) | CSV, Parquet | Chiave account |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entità servizio |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Chiave account, entità servizio |
| [Database SQL di Azure](connector-azure-sql-database.md) | - | Autenticazione SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticazione SQL |

## <a name="the-mashup-editor"></a>L'editor mashup

Quando si crea un flusso di dati di wrangling, tutti i set di dati di origine diventano query del set di dati e vengono inseriti nella cartella **ADFResource.** Per impostazione predefinita, UserQuery punterà alla prima query del set di dati. Tutte le trasformazioni devono essere eseguite in UserQuery poiché le modifiche alle query del set di dati non sono supportate né verranno rese persistenti. La ridenominazione, l'aggiunta e l'eliminazione di query non è attualmente supportata.

![Dispute](media/wrangling-data-flow/editor.png)

Attualmente non tutte le funzioni di Power Query M sono supportate per il wrangling dei dati nonostante siano disponibili durante la creazione. Durante la compilazione dei flussi di dati wrangling, verrà visualizzato il seguente messaggio di errore se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Per ulteriori informazioni sulle trasformazioni supportate, consultate [Wrangling delle funzioni del flusso](wrangling-data-flow-functions.md)di dati.

Attualmente il flusso di dati di wrangling supporta solo la scrittura in un sink.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un flusso di [dati di wrangling.](wrangling-data-flow-tutorial.md)