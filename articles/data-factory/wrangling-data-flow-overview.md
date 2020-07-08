---
title: Dispute dei flussi di dati in Azure Data Factory
description: Cenni preliminari sulla disputa dei flussi di dati in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 203e5fcaa99f7aebeda34e6968f636ed72851ec8
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921531"
---
# <a name="what-are-wrangling-data-flows"></a>Che cosa sono i flussi di dati?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Le organizzazioni devono eseguire la preparazione dei dati e la disputa per un'analisi accurata dei dati complessi che continuano a crescere ogni giorno. La preparazione dei dati è necessaria in modo che le organizzazioni possano usare i dati in vari processi aziendali e ridurre il tempo di valore.

La disputa dei flussi di dati in Azure Data Factory consente di eseguire in modo iterativo la preparazione dei dati senza codice a livello di cloud. L'integrazione di flussi di dati con [Power query online](https://docs.microsoft.com/power-query/) e rende disponibili funzioni Power query M per gli utenti di data factory.

La verifica del flusso di dati converte i M generati dall'editor di mashup di Power Query online in codice Spark per l'esecuzione su scala cloud.

La disputa dei flussi di dati è particolarmente utile per gli ingegneri di dati o per gli integratori di dati Citizen.

> [!NOTE]
> Il flusso di dati in corso è attualmente avilable in anteprima pubblica

## <a name="use-cases"></a>Casi d'uso

### <a name="fast-interactive-data-exploration-and-preparation"></a>Esplorazione e preparazione rapida di dati interattivi

Più data Engineers e gli integratori di dati Citizen possono esplorare e preparare in modo interattivo i set di dati a livello di cloud. Con l'aumentare del volume, della varietà e della velocità dei dati nei data Lake, gli utenti devono disporre di un modo efficace per esplorare e preparare i set di dati. Potrebbe essere necessario, ad esempio, creare un set di dati che "disponga di tutte le informazioni demografiche sui clienti per i nuovi clienti dal 2017". Non è possibile eseguire il mapping a una destinazione nota. Si stanno esplorando, litigando e preparando i set di impostazioni per soddisfare un requisito prima di pubblicarlo nel Lake. La disputa dei flussi di dati viene spesso usata per scenari di analisi meno formali. I set di impostazioni possono essere usati per eseguire trasformazioni e operazioni di machine learning a valle.

### <a name="code-free-agile-data-preparation"></a>Preparazione dei dati agile senza codice

Gli integratori di dati Citizen dedicano più del 60% del tempo alla ricerca e alla preparazione dei dati. Si sta cercando di eseguire questa operazione in un codice libero per migliorare la produttività operativa. Consentire agli integratori di dati dei cittadini di arricchire, modellare e pubblicare i dati usando strumenti noti come Power Query online in modo scalabile, migliora notevolmente la produttività. Il flusso di dati in Azure Data Factory consente all'editor di mashup familiare Power Query online di consentire agli integratori di dati Citizen di correggere rapidamente gli errori, standardizzare i dati e produrre dati di alta qualità per supportare le decisioni aziendali.

### <a name="data-validation"></a>Convalida dei dati

Analizza visivamente i dati in modo senza codice per rimuovere eventuali outlier, anomalie e conformi a una forma per l'analisi veloce.

## <a name="supported-sources"></a>Origini supportate

| Connettore | Formato dati | Tipo di autenticazione |
| -- | -- | --|
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md) | CSV, parquet | Chiave account |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entità servizio |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, parquet | Chiave dell'account, entità servizio |
| [Database SQL di Azure](connector-azure-sql-database.md) | - | Autenticazione SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticazione SQL |

## <a name="the-mashup-editor"></a>Editor mashup

Quando si crea un flusso di dati disputato, tutti i set di dati di origine diventano query del set di dati e vengono inseriti nella cartella **ADFResource** . Per impostazione predefinita, UserQuery punterà alla prima query del set di dati. Tutte le trasformazioni devono essere eseguite in UserQuery in quanto le modifiche alle query del set di dati non sono supportate né verranno rese permanente. La ridenominazione, l'aggiunta e l'eliminazione di query non è attualmente supportata.

![Dispute](media/wrangling-data-flow/editor.png)

Attualmente non tutte le funzioni Power Query M sono supportate per data wrangling nonostante siano disponibili durante la creazione. Quando si compilano i flussi di dati in corso, verrà visualizzato il messaggio di errore seguente se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Per ulteriori informazioni sulle trasformazioni supportate, vedere la pagina relativa alle [funzioni flusso di dati](wrangling-data-flow-functions.md).

Il flusso di dati attualmente in corso supporta solo la scrittura in un sink.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati](wrangling-data-flow-tutorial.md)in confronto.