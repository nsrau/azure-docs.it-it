---
title: Informazioni sui prezzi di Azure Data Factory tramite esempiUnderstanding Azure Data Factory pricing through examples
description: Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414933"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Determinazione dei prezzi di Data Factory ed esempi

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati.

> [!NOTE]
> I prezzi utilizzati in questi esempi sono ipotetici e non hanno lo scopo di implicare prezzi effettivi.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiare i dati da AWS S3 in Archiviazione BLOB di Azure ogni ora

In questo scenario si desidera copiare dati da AWS S3 in Archiviazione BLOB di Azure secondo una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Un'attività di copia con un set di dati di input per i dati da copiare da AWS S3.

2. Un set di dati di output per i dati in Archiviazione di Azure.

3. Un trigger di pianificazione per eseguire la pipeline ogni ora.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 2 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 2 Esecuzioni di attività (1 per l'esecuzione di trigger, 1 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 2 Record di esecuzione monitoraggio ritentata (1 per l'esecuzione di pipeline, 1 per l'esecuzione di attività) |

**Prezzo totale scenario: $ 0,16811**

- Operazioni di Data Factory = **$ 0,0001**
  - Lettura/scrittura = 10\*00001 = $ 0,0001 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 2\*000005 = $ 0,00001 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Orchestrazione &amp; esecuzione di pipeline = **$ 0,168**
  - Esecuzioni di attività = 001\*2 = 0,002 [1 esecuzione = $ 1/1000 = 0,001]
  - Attività di spostamento dati = $ 0,166 (in quote per 10 minuti di tempo di esecuzione; $ 0,25/ora in Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiare i dati e trasformarli con Azure Databricks ogni ora

In questo scenario si desidera copiare dati da AWS S3 in Archiviazione BLOB di Azure e trasformare i dati con Azure Databricks secondo una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Una sola attività di copia con un set di dati di input per i dati da copiare da AWS S3 e un set di dati di output per i dati in Archiviazione di Azure.
2. Una sola attività di Azure Databricks per la trasformazione dei dati.
3. Un solo trigger di pianificazione per eseguire la pipeline ogni ora.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 3 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 3 Esecuzioni di attività (1 per l'esecuzione di trigger, 2 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 3 Record di esecuzione monitoraggio ritentata (1 per l'esecuzione di pipeline, 2 per l'esecuzione di attività) |
| Presupposto di esecuzione di attività Databricks: tempo di esecuzione = 10 min | 10 min Esecuzione di attività di pipeline esterna |

**Prezzo totale scenario: $ 0,16916**

- Operazioni di Data Factory = **$ 0,00012**
  - Lettura/scrittura = 11\*00001 = $ 0,00011 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 3\*000005 = $ 0,00001 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Orchestrazione &amp; esecuzione di pipeline = **$ 0,16904**
  - Esecuzioni di attività = 001\*3 = 0,003 [1 esecuzione = $1/1000 = 0,001]
  - Attività di spostamento dati = $ 0,166 (in quote per 10 minuti di tempo di esecuzione; $ 0,25/ora in Azure Integration Runtime)
  - Attività di pipeline esterna = $ 0,000041 (in quote per 10 minuti di tempo di esecuzione; $ 0,00025/ora in Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copiare i dati e trasformarli con parametri dinamici ogni ora

In questo scenario si desidera copiare dati da AWS S3 in Archiviazione BLOB di Azure e trasformarli con Azure Databricks mediante parametri dinamici nello script secondo una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Una sola attività di copia con un set di dati di input per i dati da copiare da AWS S3 e un set di dati di output per i dati in Archiviazione di Azure.
2. Una sola attività di ricerca per passare i parametri in modo dinamico allo script di trasformazione.
3. Una sola attività di Azure Databricks per la trasformazione dei dati.
4. Un solo trigger di pianificazione per eseguire la pipeline ogni ora.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 3 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 4 Esecuzioni di attività (1 per l'esecuzione di trigger, 3 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 4 Record di esecuzione monitoraggio ritentata (1 per l'esecuzione di pipeline, 3 per l'esecuzione di attività) |
| Presupposto di esecuzione di attività di ricerca: tempo di esecuzione = 1 min | 1 min Esecuzione di attività di pipeline |
| Presupposto di esecuzione di attività Databricks: tempo di esecuzione = 10 min | 10 min Esecuzione di attività di pipeline esterna |

**Prezzo totale scenario: $ 0,17020**

- Operazioni di Data Factory = **$ 0,00013**
  - Lettura/scrittura = 11\*00001 = $ 0,00011 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 4\*000005 = $ 0,00002 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Orchestrazione &amp; esecuzione di pipeline = **$ 0,17007**
  - Esecuzioni di attività = 001\*4 = 0,004 [1 esecuzione = $1/1000 = 0,001]
  - Attività di spostamento dati = $ 0,166 (in quote per 10 minuti di tempo di esecuzione; $ 0,25/ora in Azure Integration Runtime)
  - Attività di pipeline = $ 0,00003 (in quote per 1 minuto di tempo di esecuzione; $ 0,002/ora in Azure Integration Runtime)
  - Attività di pipeline esterna = $ 0,000041 (in quote per 10 minuti di tempo di esecuzione; $ 0,00025/ora in Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Utilizzo del debug del flusso di dati di mapping per una giornata lavorativa normaleUsing mapping data flow debug for a normal workday

In qualità di data engineer, sei responsabile della progettazione, della creazione e del test della mappatura dei flussi di dati ogni giorno. Accedere all'interfaccia utente di ADF al mattino e attivare la modalità di debug per i flussi di dati. Il valore TTL predefinito per le sessioni di debug è 60 minuti. Si lavora tutto il giorno per 8 ore, in modo che la sessione di Debug non scade mai. Pertanto, l'addebito per il giorno sarà:

**8 (ore) x 8 (core ottimizzati per il calcolo) x 0,193 USD**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Trasformare i dati nell'archivio BLOB con il mapping dei flussi di datiTransform data in blob store with mapping data flows

In questo scenario si vuole trasformare i dati nell'archivio BLOB visivamente nei flussi di dati di mapping di ADF in base a una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Un'attività flusso di dati con la logica di trasformazione.

2. Un set di dati di input per i dati in Archiviazione di Azure.An input dataset for the data on Azure Storage.

3. Un set di dati di output per i dati in Archiviazione di Azure.

4. Un trigger di pianificazione per eseguire la pipeline ogni ora.

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 2 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 2 Esecuzioni di attività (1 per l'esecuzione di trigger, 1 per le esecuzioni di attività) |
| Presupposti del flusso di dati: tempo di esecuzione : 10 min - 10 min TTL | 10 \* 16 core di Calcolo Generale con TTL di 10 |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 2 Record di esecuzione monitoraggio ritentata (1 per l'esecuzione di pipeline, 1 per l'esecuzione di attività) |

**Prezzi totali dello scenario: 1.4631 USD**

- Operazioni di Data Factory = **$ 0,0001**
  - Lettura/scrittura = 10\*00001 = $ 0,0001 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 2\*000005 = $ 0,00001 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Esecuzione dell'orchestrazione &amp; della pipeline - **1,463 USD**
  - Esecuzioni di attività = 001\*2 = 0,002 [1 esecuzione = $ 1/1000 = 0,001]
  - Attività del flusso di dati - 1,461 USD ripartito proporzionalmente per 20 minuti (10 minuti di tempo di esecuzione e 10 minuti TTL). 0,274 USD all'ora in Azure Integration Runtime con calcolo generale di 16 core

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i prezzi per Azure Data Factory, è possibile iniziare!

- [Creare una data factory usando l'interfaccia utente di Azure Data FactoryCreate a data factory by using the Azure Data Factory UI](quickstart-create-data-factory-portal.md)

- [Introduzione a Data factory di Azure](introduction.md)

- [Creazione visiva in Azure Data FactoryVisual authoring in Azure Data Factory](author-visually.md)
