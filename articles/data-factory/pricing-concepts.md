---
title: Informazioni sui prezzi Azure Data Factory tramite esempi
description: Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: cea8ae07585f09c644f0ef6e1e6142998ddc7f08
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497504"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Determinazione dei prezzi di Data Factory ed esempi

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo spiega e illustra il modello di determinazione dei prezzi di Azure Data Factory con esempi dettagliati.

> [!NOTE]
> I prezzi usati negli esempi seguenti sono ipotetici e non suggeriscono in alcun modo i prezzi effettivi.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiare i dati da AWS S3 in Archiviazione BLOB di Azure ogni ora

In questo scenario si desidera copiare dati da AWS S3 in Archiviazione BLOB di Azure secondo una pianificazione oraria.

Per eseguire lo scenario è necessario creare una pipeline con gli elementi seguenti:

1. Un'attività di copia con un set di dati di input per i dati da copiare da AWS S3.

2. Un set di dati di output per i dati in Archiviazione di Azure.

3. Un trigger di pianificazione per eseguire la pipeline ogni ora.

   ![Il diagramma mostra una pipeline con un trigger di pianificazione. Nella pipeline, l'attività di copia passa a un set di dati di input, che passa a un servizio collegato a W S S3 e l'attività di copia passa anche a un set di dati di output, che scorre in un servizio collegato di archiviazione di Azure.](media/pricing-concepts/scenario1.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 2 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 2 Esecuzioni di attività (1 per l'esecuzione di trigger, 1 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 2 monitoraggio dei record eseguiti recuperati (1 per l'esecuzione della pipeline, 1 per l'esecuzione dell'attività) |

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

![Il diagramma mostra una pipeline con un trigger di pianificazione. Nella pipeline, l'attività di copia scorre in un set di dati di input, un set di dati di output e un'attività databricks, che viene eseguita in Azure Databricks. Il set di dati di input passa a un servizio collegato A W S S3. Il set di dati di output passa a un servizio collegato di archiviazione di Azure.](media/pricing-concepts/scenario2.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 3 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 3 Esecuzioni di attività (1 per l'esecuzione di trigger, 2 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 3 record di esecuzione del monitoraggio recuperati (1 per l'esecuzione della pipeline, 2 per l'esecuzione dell'attività) |
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

![Il diagramma mostra una pipeline con un trigger di pianificazione. Nella pipeline, l'attività di copia scorre in un set di dati di input, un set di dati di output e un'attività di ricerca che passano a un'attività di databricks, che viene eseguita su Azure Databricks. Il set di dati di input passa a un servizio collegato A W S S3. Il set di dati di output passa a un servizio collegato di archiviazione di Azure.](media/pricing-concepts/scenario3.png)

| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 3 Entità di lettura/scrittura  |
| Creare set di dati | 4 Entità di lettura/scrittura (2 per la creazione di set di dati, 2 per i riferimenti a servizi collegati) |
| Creare una pipeline | 3 Entità di lettura/scrittura (1 per la creazione di pipeline, 2 per i riferimenti a set di dati) |
| Ottenere la pipeline | 1 Entità di lettura/scrittura |
| Eseguire la pipeline | 4 Esecuzioni di attività (1 per l'esecuzione di trigger, 3 per le esecuzioni di attività) |
| Presupposto di copia dei dati: tempo di esecuzione = 10 min | 10 \* 4 Azure Integration Runtime (impostazione DIU predefinita = 4) Per altre informazioni sulle unità di integrazione dati e l'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 4 record di esecuzione del monitoraggio recuperati (1 per l'esecuzione della pipeline, 3 per l'esecuzione dell'attività) |
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

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Uso del debug del flusso di dati di mapping per una giornata lavorativa normale

In qualità di ingegnere dei dati, Sam è responsabile della progettazione, della compilazione e del testing di flussi di dati di mapping ogni giorno. Sam accede all'interfaccia utente di ADF al mattino e Abilita la modalità di debug per i flussi di dati. Il valore TTL predefinito per le sessioni di debug è 60 minuti. Sam funziona nel corso della giornata per 8 ore, quindi la sessione di debug non scade mai. Quindi, gli addebiti per il giorno di Sam saranno:

**8 (ore) x 8 (Core ottimizzati per il calcolo) x $0,193 = $12,35**

Allo stesso tempo, Chris, un altro ingegnere di dati, accede anche all'interfaccia utente del browser ADF per il profiling dei dati e il lavoro di progettazione ETL. Chris non funziona in ADF tutti i giorni come Sam. Chris deve solo usare il debugger del flusso di dati per un'ora nello stesso periodo e nello stesso giorno di Sam sopra. Questi sono gli addebiti che Chris comporta per l'utilizzo del debug:

**1 (ora) x 8 (core per utilizzo generico) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Trasformare i dati nell'archivio BLOB con i flussi di dati di mapping

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
| Presupposti del flusso di dati: tempo di esecuzione = 10 min + 10 min TTL | 10 \* 16 core di calcolo generale con TTL di 10 |
| Presupposto di monitoraggio della pipeline: solo 1 esecuzione effettuata | 2 monitoraggio dei record eseguiti recuperati (1 per l'esecuzione della pipeline, 1 per l'esecuzione dell'attività) |

**Prezzi dello scenario totale: $1,4631**

- Operazioni di Data Factory = **$ 0,0001**
  - Lettura/scrittura = 10\*00001 = $ 0,0001 [1 L/S = $ 0,50/50000 = 0,00001]
  - Monitoraggio = 2\*000005 = $ 0,00001 [1 monitoraggio = $ 0,25/50000 = 0,000005]
- Esecuzione orchestrazione pipeline &amp; = **$1,463**
  - Esecuzioni di attività = 001\*2 = 0,002 [1 esecuzione = $ 1/1000 = 0,001]
  - Attività flusso di dati = $1,461 propagate per 20 minuti (tempo di esecuzione di 10 minuti + TTL di 10 minuti). $0.274/hour in Azure Integration Runtime con 16 core calcolo generale

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Integrazione dei dati in Azure Data Factory Managed VNET
In questo scenario si vogliono eliminare i file originali nell'archivio BLOB di Azure e copiare i dati dal database SQL di Azure all'archivio BLOB di Azure. Questa esecuzione verrà eseguita due volte su pipeline diverse. Il tempo di esecuzione di queste due pipeline è sovrapposto.
![Scenario4 ](media/pricing-concepts/scenario-4.png) per eseguire lo scenario, è necessario creare due pipeline con gli elementi seguenti:
  - Attività pipeline-attività di eliminazione.
  - Un'attività di copia con un set di dati di input per i dati da copiare dall'archiviazione BLOB di Azure.
  - Un set di dati di output per i dati nel database SQL di Azure.
  - Un trigger di pianificazione per eseguire la pipeline.


| **Operazioni** | **Tipi e unità** |
| --- | --- |
| Creare i servizi collegati | 4 entità di lettura/scrittura |
| Creare set di dati | 8 entità di lettura/scrittura (4 per la creazione del set di dati, 4 per i riferimenti ai servizi collegati) |
| Creare una pipeline | 6 entità di lettura/scrittura (2 per la creazione della pipeline, 4 per i riferimenti al set di dati) |
| Ottenere la pipeline | 2 Entità di lettura/scrittura |
| Eseguire la pipeline | 6 esecuzioni attività (2 per l'esecuzione del trigger, 4 per le esecuzioni di attività) |
| Esegui attività di eliminazione: ogni tempo di esecuzione = 5 min. L'esecuzione dell'attività Delete nella prima pipeline è da 10:00 UTC a 10:05 AM UTC. L'esecuzione dell'attività Delete nella seconda pipeline è da 10:02 UTC a 10:07 AM UTC.|Totale di 7 minuti di esecuzione dell'attività pipeline in VNET gestiti. L'attività della pipeline supporta fino a 50 di concorrenza nei VNET gestiti. |
| Copia dati presupposto: ogni tempo di esecuzione = 10 min. L'esecuzione della copia nella prima pipeline è da 10:06 UTC a 10:15 AM UTC. L'esecuzione dell'attività Delete nella seconda pipeline è da 10:08 UTC a 10:17 AM UTC. | 10 * 4 Azure Integration Runtime (impostazione predefinita DIU = 4) per ulteriori informazioni sulle unità di integrazione dei dati e sull'ottimizzazione delle prestazioni di copia, vedere [questo articolo](copy-activity-performance.md) . |
| Presupposto della pipeline di monitoraggio: sono state eseguite solo 2 esecuzioni | 6 monitoraggio dei record eseguiti recuperati (2 per l'esecuzione della pipeline, 4 per l'esecuzione dell'attività) |


**Prezzi dello scenario totale: $0,45523**

- Operazioni Data Factory = $0,00023
  - Lettura/scrittura = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoraggio = 6 * 000005 = $0,00003 [1 monitoraggio = $0,25/50000 = 0,000005]
- Esecuzione & orchestrazione pipeline = $0,455
  - Esecuzioni attività = 0,001 * 6 = 0,006 [1 esecuzione = $1/1000 = 0,001]
  - Attività di spostamento dei dati = $0,333 (ripercentuale per 10 minuti di tempo di esecuzione. $ 0,25/ora in Azure Integration Runtime)
  - Attività pipeline = $0,116 (ripercentuale per 7 minuti di tempo di esecuzione. $1/ora Azure Integration Runtime)

> [!NOTE]
> Questi prezzi sono solo a scopo esemplificativo.

**Domande frequenti**

D: se si desidera eseguire più di 50 attività della pipeline, le attività possono essere eseguite contemporaneamente?

R: saranno consentite le attività di pipeline simultanee max 50.  L'attività della pipeline 51th verrà accodata finché non viene aperto uno slot "Free slot". Uguale per l'attività esterna. Sarà consentito il numero massimo di 800 attività esterne simultanee.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i prezzi per Azure Data Factory, è possibile iniziare!

- [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introduzione al servizio Azure Data Factory](introduction.md)

- [Creazione di oggetti visivi in Azure Data Factory](author-visually.md)
