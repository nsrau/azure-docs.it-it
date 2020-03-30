---
title: File di inclusione
description: File di inclusione
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086211"
---
Azure Data Factory è un servizio multi-tenant con i limiti predefiniti seguenti per assicurarsi che le sottoscrizioni dei clienti siano protette dai carichi di lavoro reciproci. Per aumentare i limiti fino al massimo per l'abbonamento, contattare il supporto tecnico.

### <a name="version-2"></a>Versione 2

| Risorsa | Limite predefinito | Limite massimo |
| -------- | ------------- | ------------- |
| Data factory in una sottoscrizione di Azure | 800 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero totale di entità, ad esempio pipeline, set di dati, trigger, servizi collegati e runtime di integrazione, all'interno di una data factory | 5.000 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale core CPU per runtime di integrazione Azure-SSIS in un'unica sottoscrizione | 256 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| La pipeline simultanea viene eseguita per ogni data factory condivisa tra tutte le pipeline della factoryConcurrent pipeline runs per data factory that's shared among all pipelines in the factory | 10,000  | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| L'attività esterna simultanea viene eseguita per ogni sottoscrizione per ogni area del runtime di [integrazione](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) di AzureConcurrent External activity runs per subscription per Azure Integration Runtime region<br><small>Le attività esterne vengono gestite in fase di esecuzione dell'integrazione, ma vengono eseguite su servizi collegati, tra cui Databrick, stored procedure, HDInsights, Web e altri.</small> | 3000 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| L'attività della pipeline simultanea viene eseguita per ogni sottoscrizione per ogni area del runtime di integrazione di [AzureConcurrent](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) Pipeline activity runs per subscription per Azure Integration Runtime region <br><small>Pipeline activities execute on integration runtime, including Lookup, GetMetadata, and Delete.</small>| 1000 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operazioni di creazione simultanee per sottoscrizione per ogni area del runtime di integrazione di [AzureConcurrent authoring](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) operations per subscription per azure Integration Runtime region<br><small>Compresa la connessione di prova, l'elenco delle cartelle di ricerca e l'elenco delle tabelle, l'anteprima dei dati. | 200 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di integrazione dati simultanee<sup>1</sup> consumo per sottoscrizione per ogni area del runtime di [integrazione di AzureConcurrent](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) Data Integration Units 1 consumption per subscription per Azure Integration Runtime region| Gruppo regione 1<sup>2</sup>: 6000<br>Gruppo regione 2<sup>2</sup>: 3000<br>Gruppo regione 3<sup>2</sup>: 1500 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero massimo di attività per pipeline, che include attività interne per i contenitoriMaximum activities per pipeline, which includes inner activities for containers | 40 | 40 |
| Numero massimo di runtime di integrazione collegati che possono essere creati in un singolo runtime di integrazione self-hosted | 100 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parametri massimi per pipeline | 50 | 50 |
| Elementi ForEach | 100,000 | 100,000 |
| Parallelismo ForEach | 20 | 50 |
| Numero massimo di esecuzioni in coda per pipelineMaximum queued runs per pipeline | 100 | 100 |
| Caratteri per espressione | 8,192 | 8,192 |
| Intervallo minimo di attivazione della finestra a cascata | 15 min | 15 min |
| Timeout massimo per l'esecuzione dell'attività della pipelineMaximum timeout for pipeline activity runs | 7 giorni | 7 giorni |
| Byte per oggetto per gli oggetti pipeline<sup>3</sup> | 200 kB | 200 kB |
| Byte per oggetto per set di dati e oggetti servizio collegati<sup>3</sup> | 100 kB | 2.000 KB |
| Unità di integrazione dati 1 per ogni esecuzione dell'attività di copiaData Integration Units<sup>1</sup> per copy activity run | 256 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in scrittura | 1.200/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in lettura | 12.500/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitoraggio delle query al minuto | 1.000 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operazioni CRUD di entità al minuto | 50 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tempo massimo di sessione di debug del flusso di datiMaximum time of data flow debug session | 8 ore | 8 ore |
| Numero simultaneo di flussi di dati per factoryConcurrent number of data flows per factory | 50 | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero simultaneo di sessioni di debug del flusso di dati per utente per factory | 3 | 3 |
| Limite TTL di Azure IR flusso di datiData Flow Azure IR TTL limit | 4 ore | [Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> L'unità di integrazione dei dati (DIU) viene utilizzata in un'operazione di copia cloud-cloud, per ulteriori informazioni, vedere Unità di [integrazione dei dati (versione 2).](../articles/data-factory/copy-activity-performance.md#data-integration-units) Per informazioni sulla fatturazione, vedere Prezzi di [Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/)

<sup>2</sup> Il runtime di [integrazione](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) di Azure è disponibile a [livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e la riduzione dei costi in uscita della rete. 

| Gruppo regione | Regioni | 
| -------- | ------ |
| Gruppo regione 1 | Stati Uniti centrali, Stati Uniti orientali, US2 orientale, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 |
| Gruppo di regioni 2 | Australia Orientale, Australia Sud-Est, Brasile Meridionale, India centrale, Giappone Est, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Sud-Est asiatico, Stati Uniti centro-occidentali |
| Gruppo di regioni 3 | Canada Centrale, Asia Orientale, Francia Centrale, Corea Centrale, Regno Unito Sud |

<sup>3</sup> Pipeline, set di dati e oggetti assistenza collegati rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non sono correlati alla quantità di dati che è possibile spostare ed elaborare con Azure Data Factory.Limits for these objects don't relate to the amount of data you can move and process with Azure Data Factory. Data Factory è progettato per la scalabilità per gestire petabyte di dati.

### <a name="version-1"></a>Versione 1

| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Pipeline in una data factory |2.500 |[Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Set di dati all'interno di una data factoryData sets within a data factory |5.000 |[Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Sezioni simultanee per set di datiConcurrent slices per data set |10 |10 |
| Byte per oggetto per gli oggetti pipeline<sup>1</sup> |200 kB |200 kB |
| Byte per oggetto per set di dati e oggetti servizio collegati<sup>1</sup> |100 kB |2.000 KB |
| Core del cluster su richiesta di Azure HDInsight all'interno di una sottoscrizione<sup>2Azure</sup> HDInsight on-demand cluster cores within a subscription 2 |60 |[Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Le unità di spostamento dei dati cloud per ogni attività di copia sono<sup>3</sup> |32 |[Contattare il supporto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero di tentativi delle esecuzioni di attività pipeline |1.000 |MaxInt (32 bit) |

<sup>1</sup> Pipeline, set di dati e oggetti servizio collegati rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non sono correlati alla quantità di dati che è possibile spostare ed elaborare con Azure Data Factory.Limits for these objects don't relate to the amount of data you can move and process with Azure Data Factory. Data Factory è progettato per la scalabilità per gestire petabyte di dati.

<sup>2</sup> I core HDInsight su richiesta vengono allocati all'esterno della sottoscrizione che contiene la data factory. Di conseguenza, il limite precedente è il limite di core imposto da Data Factory per i core HDInsight su richiesta. È diverso dal limite di core associato alla sottoscrizione di Azure.It's different from the core limit that's associated with your Azure subscription.

<sup>3</sup> L'unità di spostamento dei dati cloud (DMU) per la versione 1 viene utilizzata in un'operazione di copia cloud-to-cloud, per ulteriori informazioni, vedere Unità di [spostamento dati cloud (versione 1).](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) Per informazioni sulla fatturazione, vedere Prezzi di [Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/)

| **Risorsa** | **Limite inferiore predefinito** | **Limite minimo** |
| --- | --- | --- |
| Intervallo di pianificazione |15 minuti |15 minuti |
| Intervallo tra tentativi |1 secondo |1 secondo |
| Valore di timeout del tentativo |1 secondo |1 secondo |

#### <a name="web-service-call-limits"></a>Limiti di chiamata del servizio Web
Azure Resource Manager presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di [limiti API di gestione risorse di Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
