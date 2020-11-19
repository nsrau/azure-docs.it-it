---
title: includere il file
description: includere file
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 1e8d13e7cf302c486bab291ef6482216122b62a3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681262"
---
Azure Data Factory è un servizio multi-tenant che prevede i limiti predefiniti seguenti per fare in modo che le sottoscrizioni dei clienti siano protette da carichi di lavoro reciproci. Per aumentare i limiti fino al valore massimo consentito per la sottoscrizione, contattare il supporto tecnico.

### <a name="version-2"></a>Versione 2

| Risorsa | Limite predefinito | Limite massimo |
| -------- | ------------- | ------------- |
| Data factory in una sottoscrizione di Azure | 800 | 800 |
| Numero totale di entità, ad esempio pipeline, set di dati, trigger, servizi collegati, endpoint privati e runtime di integrazione, all'interno di una data factory | 5\.000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale di core CPU per istanze di Azure-SSIS Integration Runtime in un'unica sottoscrizione | 256 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni di pipeline simultanee per ogni data factory condivisa tra tutte le pipeline nella factory | 10,000  | 10,000 |
| Esecuzioni attività esterne simultanee per sottoscrizione per ogni [area Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Le attività esterne vengono gestite nel runtime di integrazione, ma vengono eseguite in servizi collegati, tra cui Databricks, stored procedure, HDInsights, Web e altri. Questo limite non si applica al runtime di integrazione self-hosted</small>. | 3,000 | 3.000 |
| Esecuzioni attività di pipeline simultanee per sottoscrizione per ogni [area Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Le attività della pipeline vengono eseguite nel runtime di integrazione, tra cui Lookup, GetMetadata e Delete. Questo limite non si applica al runtime di integrazione self-hosted</small>. | 1\.000 | 1\.000                                                        |
| Operazioni di creazione simultanee per sottoscrizione per ogni [area Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Sono incluse le operazioni di test connessione, esplorazione dell'elenco cartelle e dell'elenco tabelle ed anteprima dei dati. Questo limite non si applica al runtime di integrazione self-hosted</small>. | 200 | 200                                                          |
| Consumo di unità di integrazione dei dati simultanee<sup>1</sup> per sottoscrizione per ogni [are di Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Gruppo di aree 1<sup>2</sup>: 6000<br>Gruppo di aree 2<sup>2</sup>: 3,000<br>Gruppo di aree 3<sup>2</sup>: 1.500 | Gruppo di aree 1<sup>2</sup>: 6000<br/>Gruppo di aree 2<sup>2</sup>: 3,000<br/>Gruppo di aree 3<sup>2</sup>: 1.500 |
| Numero massimo di attività per pipeline, che include le attività interne per i contenitori | 40 | 40 |
| Numero massimo di runtime di integrazione collegati che possono essere creati su un singolo runtime di integrazione self-hosted | 100 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero massimo di parametri per pipeline | 50 | 50 |
| Elementi ForEach | 100,000 | 100,000 |
| Parallelismo ForEach | 20 | 50 |
| Numero massimo di esecuzioni in coda per pipeline | 100 | 100 |
| Caratteri per espressione | 8,192 | 8,192 |
| Intervallo minimo per il trigger di finestra a cascata | 15 min | 15 min |
| Timeout massimo per le esecuzioni attività di pipeline | 7 giorni | 7 giorni |
| Byte per oggetto per gli oggetti pipeline<sup>3</sup> | 200 kB | 200 kB |
| Byte per oggetto per oggetti set di dati e servizio collegato<sup>3</sup> | 100 kB | 2\.000 KB |
| Byte per payload per ogni esecuzione attività<sup>4</sup> | 896 KB | 896 KB |
| Unità di integrazione dati<sup>1</sup> per esecuzione attività di copia | 256 | 256 |
| Chiamate API in scrittura | 1\.200/ora | 1\.200/ora<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. |
| Chiamate API in lettura | 12.500/ora | 12.500/ora<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. |
| Monitoraggio delle query al minuto | 1\.000 | 1\.000 |
| Tempo massimo per la sessione di debug del flusso di dati | 8 ore | 8 ore |
| Numero di flussi di dati simultanei per runtime di integrazione | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero di sessioni di debug del flusso di dati simultanee per utente per ogni factory | 3 | 3 |
| Limite TTL di Azure IR per Flusso di dati | 4 ore |  4 ore |

<sup>1</sup> L'unità di integrazione dei dati viene usata in un'operazione di copia da cloud a cloud. Per altre informazioni, vedere [Unità di integrazione dei dati (versione 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Per informazioni sulla fatturazione, vedere [Prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) è [disponibile a livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e l'abbattimento dei costi in uscita della rete. 

| Gruppo di aree | Regioni |
| -------- | ------ |
| Gruppo di aree 1 | Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 |
| Gruppo di aree 2 | Australia orientale, Australia sud-orientale, Brasile meridionale, India centrale, Giappone orientale, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Asia sud-orientale, Stati Uniti centro-occidentali |
| Gruppo di aree 3 | Canada centrale, Asia orientale, Francia centrale, Corea centrale, Regno Unito meridionale |

<sup>3</sup> Gli oggetti servizio collegato, pipeline e set di dati rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non riguardano la quantità di dati che è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità e la gestione di petabyte di dati.

<sup>4</sup> Il payload per ogni esecuzione attività include la configurazione dell'attività, le configurazioni associate di set di dati e servizi collegati, se disponibili, e una piccola percentuale di proprietà di sistema generate per tipo di attività. Il limite per le dimensioni di questo payload non è correlato alla quantità di dati che è possibile spostare ed elaborare con Azure Data Factory. Se si raggiunge questo limite, vedere informazioni su [sintomi e raccomandazioni](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large).

### <a name="version-1"></a>Versione 1

| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Pipeline in una data factory |2\.500 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Set di dati in una data factory |5\.000 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Sezioni simultanee per set di dati |10 |10 |
| Byte per oggetto per gli oggetti pipeline<sup>1</sup> |200 kB |200 kB |
| Byte per oggetto per oggetti set di dati e servizio collegato<sup>1</sup> |100 kB |2\.000 KB |
| Memorie centrali del cluster Azure HDInsight su richiesta in una sottoscrizione<sup>2</sup> |60 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di spostamento dati cloud per esecuzione attività di copia<sup>3</sup> |32 |32 |
| Numero di tentativi delle esecuzioni di attività pipeline |1\.000 |MaxInt (32 bit) |

<sup>1</sup> Gli oggetti servizio collegato, pipeline e set di dati rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non riguardano la quantità di dati che è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità e la gestione di petabyte di dati.

<sup>2</sup> I core HDInsight su richiesta vengono allocati all'esterno della sottoscrizione che contiene la data factory. Di conseguenza, il limite precedente è il limite di core applicato da Data Factory per i core HDInsight su richiesta. Si tratta di un limite diverso da quello relativo ai core associato alla sottoscrizione di Azure.

<sup>3</sup> L'unità di spostamento dati cloud per la versione 1 viene usata in un'operazione di copia da cloud a cloud. Per altre informazioni, vedere [Unità di spostamento dati cloud (versione 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Per informazioni sulla fatturazione, vedere [Prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Risorsa** | **Limite inferiore predefinito** | **Limite minimo** |
| --- | --- | --- |
| Intervallo di pianificazione |15 minuti |15 minuti |
| Intervallo tra tentativi |1 secondo |1 secondo |
| Valore di timeout del tentativo |1 secondo |1 secondo |

#### <a name="web-service-call-limits"></a>Limiti di chiamata del servizio Web
Azure Resource Manager presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di [limiti API di gestione risorse di Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
