---
title: File di inclusione
description: File di inclusione
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6dc846ab6af0ed4d6d48c6f6db0abbce06427c36
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035884"
---
Azure Data Factory è un servizio multi-tenant con i limiti predefiniti seguenti per assicurarsi che le sottoscrizioni dei clienti siano protette tra loro. Per aumentare i limiti fino al valore massimo per la sottoscrizione, contattare il supporto tecnico.

### <a name="version-2"></a>Versione 2

| Resource | Limite predefinito | Limite massimo |
| -------- | ------------- | ------------- |
| Data factory in una sottoscrizione di Azure | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero totale di entità, ad esempio pipeline, set di dati, trigger, servizi collegati e runtime di integrazione, all'interno di un data factory | 5\.000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale core CPU per i runtime di integrazione SSIS di Azure in una sottoscrizione | 256 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni simultanee di pipeline per data factory condivise tra tutte le pipeline della Factory | 10,000  | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni di attività esterne simultanee per ogni sottoscrizione per [Azure Integration Runtime area](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Le attività esterne vengono gestite in Integration Runtime, ma vengono eseguite su servizi collegati, tra cui databricks, stored procedure, HDInsights e altri.</small> | 3000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni di attività pipeline simultanee per ogni sottoscrizione per [Azure Integration Runtime area](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Le attività della pipeline vengono eseguite in Integration Runtime, tra cui Lookup, GetMetadata ed Delete.</small>| 1000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operazioni di creazione simultanee per ogni sottoscrizione per [Azure Integration Runtime area](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Including test connection, sfogliare l'elenco di cartelle e l'elenco di tabelle, visualizzare l'anteprima dei dati. | 200 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di integrazione dati simultanee<sup>1</sup> consumo per sottoscrizione per [area Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Gruppo di aree 1<sup>2</sup>: 6000<br>Gruppo di aree<sup>2 2:</sup> 3000<br>Gruppo di aree 3<sup>2</sup>: 1500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero massimo di attività per pipeline, incluse le attività interne per i contenitori | 40 | 40 |
| Numero massimo di runtime di integrazione collegati che è possibile creare in un singolo runtime di integrazione self-hosted | 100 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero massimo di parametri per pipeline | 50 | 50 |
| Elementi ForEach | 100,000 | 100,000 |
| Parallelismo ForEach | 20 | 50 |
| Caratteri per espressione | 8,192 | 8,192 |
| Intervallo di trigger della finestra a cascata minima | 15 min | 15 min |
| Timeout massimo per le esecuzioni delle attività della pipeline | 7 giorni | 7 giorni |
| Byte per oggetto per oggetti pipeline<sup>3</sup> | 200 kB | 200 kB |
| Byte per oggetto per set di dati e oggetti servizio collegato<sup>3</sup> | 100 kB | 2\.000 KB |
| Unità di integrazione dati<sup>1</sup> per esecuzione dell'attività di copia | 256 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in scrittura | 1200/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in lettura | 12500/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitoraggio delle query al minuto | 1\.000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operazioni CRUD di entità al minuto | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tempo massimo per la sessione di debug del flusso di dati | 8 ore | 8 ore |
| Numero simultaneo di flussi di dati per Factory | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero simultaneo di sessioni di debug del flusso di dati per utente per Factory | 3 | 3 |
| Limite TTL Azure IR flusso di dati | 4 ore | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> l'unità di integrazione dati (DIU) viene usata in un'operazione di copia da cloud a cloud, altre informazioni dalle [unità di integrazione dati (versione 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Per informazioni sulla fatturazione, vedere [Azure Data Factory prezzi](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) è [disponibile a livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e i costi di uscita della rete ridotti. 

| Gruppo di aree | Regions | 
| -------- | ------ |
| Gruppo di aree 1 | Stati Uniti centrali, Stati Uniti orientali, East Uniti, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 |
| Gruppo di aree 2 | Australia orientale, Australia sudorientale, Brasile meridionale, India centrale, Giappone orientale, Northcentral US, del Stati Uniti, Asia sudorientale, Stati Uniti centro-occidentali |
| Gruppo di aree 3 | Canada centrale, Asia orientale, Francia centrale, Corea centrale, Regno Unito meridionale |

<sup>3</sup> gli oggetti pipeline, set di dati e servizio collegato rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non sono correlati alla quantità di dati che è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità in modo da gestire petabyte di dati.

### <a name="version-1"></a>Versione 1

| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Data factory in una sottoscrizione di Azure |50 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipeline in una data factory |2\.500 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Set di dati all'interno di un data factory |5\.000 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Sezioni simultanee per set di dati |10 |10 |
| Byte per oggetto per gli oggetti pipeline<sup>1</sup> |200 kB |200 kB |
| Byte per oggetto per set di dati e oggetti servizio collegato<sup>1</sup> |100 kB |2\.000 KB |
| Core del cluster su richiesta di Azure HDInsight all'interno di una sottoscrizione<sup>2</sup> |60 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di spostamento dati cloud per ogni attività di copia eseguita<sup>3</sup> |32 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero di tentativi delle esecuzioni di attività pipeline |1\.000 |MaxInt (32 bit) |

<sup>1</sup> gli oggetti pipeline, set di dati e servizio collegato rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non sono correlati alla quantità di dati che è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità in modo da gestire petabyte di dati.

<sup>2</sup> I core HDInsight su richiesta vengono allocati all'esterno della sottoscrizione che contiene la data factory. Di conseguenza, il limite precedente è il limite di core applicato Data Factory per i core HDInsight su richiesta. È diverso dal limite principale associato alla sottoscrizione di Azure.

<sup>3</sup> l'unità di spostamento dati cloud (spostamento dati) per la versione 1 viene usata in un'operazione di copia da cloud a cloud, altre informazioni dalle [unità di spostamento dati cloud (versione 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Per informazioni sulla fatturazione, vedere [Azure Data Factory prezzi](https://azure.microsoft.com/pricing/details/data-factory/).

| **Risorsa** | **Limite inferiore predefinito** | **Limite minimo** |
| --- | --- | --- |
| Intervallo di pianificazione |15 minuti |15 minuti |
| Intervallo tra tentativi |1 secondo |1 secondo |
| Valore di timeout del tentativo |1 secondo |1 secondo |

#### <a name="web-service-call-limits"></a>Limiti di chiamata del servizio Web
Azure Resource Manager presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di [limiti API di gestione risorse di Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
