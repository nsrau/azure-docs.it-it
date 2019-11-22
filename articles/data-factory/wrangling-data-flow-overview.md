---
title: Dispute dei flussi di dati in Azure Data Factory
description: Cenni preliminari sulla disputa dei flussi di dati in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e36cc044e6a4160d16f15b93d8a88d946f476c89
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287074"
---
# <a name="what-are-wrangling-data-flows"></a>Che cosa sono i flussi di dati?

Le organizzazioni devono eseguire la preparazione dei dati e la disputa per un'analisi accurata dei dati complessi che continuano a crescere ogni giorno. La preparazione dei dati è necessaria in modo che le organizzazioni possano usare i dati in vari processi aziendali e ridurre il tempo di valore.

La disputa dei flussi di dati in Azure Data Factory consente di eseguire in modo iterativo la preparazione dei dati senza codice a livello di cloud. L'integrazione di flussi di dati con [Power query online](https://docs.microsoft.com/power-query/) e rende disponibili funzioni Power query M per gli utenti di data factory.

La verifica del flusso di dati converte i M generati dall'editor di mashup di Power Query online in codice Spark per l'esecuzione su scala cloud.

La disputa dei flussi di dati è particolarmente utile per gli ingegneri di dati o per gli integratori di dati Citizen.

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
| [Archivio BLOB di Azure](connector-azure-blob-storage.md) | CSV | Chiave account |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entità servizio |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV | Chiave dell'account, entità servizio |
| [Database SQL di Azure](connector-azure-sql-database.md) | - | Autenticazione in SQL |
| [Analisi delle sinapsi di Azure](connector-azure-sql-data-warehouse.md) | - | Autenticazione in SQL |

## <a name="the-mashup-editor"></a>Editor mashup

Quando si crea un flusso di dati disputato, tutti i set di dati di origine diventano query del set di dati e vengono inseriti nella cartella **ADFResource** . Per impostazione predefinita, UserQuery punterà alla prima query del set di dati. Tutte le trasformazioni devono essere eseguite in UserQuery in quanto le modifiche alle query del set di dati non sono supportate né verranno rese permanente. La ridenominazione, l'aggiunta e l'eliminazione di query non è attualmente supportata.

![Dispute](media/wrangling-data-flow/editor.png)

Attualmente non tutte le funzioni Power Query M sono supportate per data wrangling nonostante siano disponibili durante la creazione. Quando si compilano i flussi di dati in corso, verrà visualizzato il messaggio di errore seguente se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Per ulteriori informazioni sulle trasformazioni supportate, vedere la pagina relativa alle [funzioni flusso di dati](wrangling-data-flow-functions.md).

Il flusso di dati attualmente in corso supporta solo la scrittura in un sink.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati](wrangling-data-flow-tutorial.md)in confronto.