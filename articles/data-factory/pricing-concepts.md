---
title: Determinazione dei prezzi di Azure Data Factory ed esempi | Microsoft Docs
description: Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: 168d977b9dc0ea6117796cf98a8562f168258d28
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387450"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Determinazione dei prezzi di Data Factory ed esempi

Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati.

> [!NOTE]
> I prezzi usati negli esempi seguenti sono ipotetici e non sono destinati a implicare prezzi effettivi.

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

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Uso del debug del flusso di dati di mapping per una giornata lavorativa normale (prezzi anteprima)

In qualità di ingegnere dei dati, l'utente è responsabile della progettazione, della compilazione e del testing di flussi di dati di mapping ogni giorno. Accedere all'interfaccia utente di ADF al mattino e abilitare la modalità di debug per i flussi di dati. Il valore TTL predefinito per le sessioni di debug è 60 minuti. Si lavora in tutto il giorno per 10 ore, quindi la sessione di debug non scade mai. L'addebito per la giornata sarà quindi:

**10 (ore) x 8 (Core) x $0,112 = $8,96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Trasformare i dati nell'archivio BLOB con i flussi di dati di mapping (prezzi anteprima)

In questo scenario si desidera trasformare i dati nell'archivio BLOB in modo visivo in ADF mapping dei flussi di dati in base a una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Un'attività flusso di dati con la logica di trasformazione.

2. Un set di dati di input per i dati in archiviazione di Azure.

3. Un set di dati di output per i dati in Archiviazione di Azure.

4. Un trigger di pianificazione per eseguire la pipeline ogni ora.

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 2 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 2 Esecuzioni di attività (1 per l'esecuzione di trigger, 1 per le esecuzioni di attività) |
| Presupposti del flusso di dati: tempo di esecuzione = 10 min + 10 min TTL | 10 \* 8 core di calcolo generale con TTL di 10 |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 2 Record di esecuzione monitoraggio ritentata (1 per l'esecuzione di pipeline, 1 per l'esecuzione di attività) |

**Prezzi dello scenario totale: $0,3011**

- Operazioni di Data Factory = **$ 0,0001**
  - Lettura/scrittura = 10\*00001 = $ 0,0001 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 2\*000005 = $ 0,00001 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Orchestrazione pipeline &amp; esecuzione = **$0,301**
  - Esecuzioni di attività = 001\*2 = 0,002 [1 esecuzione = $ 1/1000 = 0,001]
  - Attività flusso di dati = $0,299 propagate per 20 minuti (tempo di esecuzione di 10 minuti + TTL di 10 minuti). $0.112/hour in Azure Integration Runtime con 8 core calcolo generale

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i prezzi per Azure Data Factory, è possibile iniziare!

- [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introduzione al servizio Azure Data Factory](introduction.md)

- [Creazione di oggetti visivi in Azure Data Factory](author-visually.md)
