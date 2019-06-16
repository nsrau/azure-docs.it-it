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
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427594"
---
Azure Data Factory è un servizio multi-tenant che presenta i seguenti limiti predefiniti in modo che le sottoscrizioni dei clienti siano protette da ognuno degli altri carichi di lavoro. Per aumentare i limiti fino al valore massimo per la sottoscrizione, contattare il supporto tecnico.

### <a name="version-2"></a>Versione 2

| Resource | Limite predefinito | Limite massimo |
| -------- | ------------- | ------------- |
| Data factory in una sottoscrizione di Azure | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero totale di entità, ad esempio le pipeline, set di dati, trigger, servizi collegati e i runtime di integrazione, all'interno di una data factory | 5\.000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale di core CPU per i runtime di integrazione SSIS di Azure in una sottoscrizione | 256 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni di pipeline simultanee per ogni data factory in cui viene condiviso tra tutte le pipeline nella factory | 10,000  | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni simultanee dell'attività esterne per sottoscrizione per [area del Runtime di integrazione di Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Attività esterne vengono gestite nel runtime di integrazione, ma eseguire nei servizi collegati, tra cui Databricks, stored procedure, HDInsights e ad altri utenti.</small> | 3000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Esecuzioni simultanee dell'attività della Pipeline per sottoscrizione per [area del Runtime di integrazione di Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Attività della pipeline eseguita nel runtime di integrazione, tra cui la ricerca, GetMetadata ed eliminare. </small>| 1000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Simultanee per sottoscrizione per operazioni di creazione [area del Runtime di integrazione di Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Tra cui test della connessione, elenco Sfoglia cartella ed elenco di tabelle, visualizzare in anteprima i dati. | 200 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di integrazione dati simultanei<sup>1</sup> utilizzo per sottoscrizione per [area del Runtime di integrazione di Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 1 gruppo di aree<sup>2</sup>: 6000<br>Gruppo dell'area 2<sup>2</sup>: 3000<br>Gruppo dell'area 3<sup>2</sup>: 1500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero massima attività per pipeline, che include le attività interne per i contenitori | 40 | 40 |
| Numero massimo di runtime di integrazione collegato che possono essere creati da un runtime di integrazione self-hosted singolo | 100 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parametri massimi per ogni pipeline | 50 | 50 |
| Elementi ForEach | 100,000 | 100,000 |
| Parallelismo ForEach | 20 | 50 |
| Caratteri per espressione | 8,192 | 8,192 |
| Intervallo di trigger di finestra a cascata minimo | 15 min | 15 min |
| Timeout massimo per l'attività della pipeline viene eseguita | 7 giorni | 7 giorni |
| Byte per oggetto per gli oggetti pipeline<sup>3</sup> | 200 kB | 200 kB |
| Byte per oggetto per il set di dati e oggetti servizio collegato<sup>3</sup> | 100 kB | 2\.000 KB |
| Integrazione di Data Unit<sup>1</sup> per ogni esecuzione attività di copia | 256 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in scrittura | 2\.500/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in lettura | 12\.500/h<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitoraggio delle query al minuto | 1\.000 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operazioni CRUD di entità al minuto | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> l'unità di integrazione dei dati (DIU) viene usato in un'operazione di copia da cloud a cloud, altre informazioni, vedere [unità di integrazione di dati (versione 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Per informazioni sulla fatturazione, vedere [prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) viene [disponibile a livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e rete ridotto dati in uscita dei costi. 

| Gruppo di aree | Regioni | 
| -------- | ------ |
| Gruppo dell'area 1 | Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 |
| Gruppo dell'area 2 | Australia orientale, Australia sud-orientale, Brasile meridionale, India centrale, Giappone orientale, Northcentral degli Stati Uniti, Southcentral degli Stati Uniti, Asia sud-orientale, Stati Uniti centro occidentali |
| Gruppo dell'area 3 | Canada centrale, Asia orientale, Francia centrale, Corea centrale, Regno Unito meridionale |

<sup>3</sup> pipeline, set di dati e oggetti servizio collegato rappresentano un raggruppamento logico del carico di lavoro. Limiti per questi oggetti non riguardano la quantità di dati è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità per gestire petabyte di dati.

### <a name="version-1"></a>Versione 1

| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Data factory in una sottoscrizione di Azure |50 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipeline in una data factory |2\.500 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Set di dati all'interno di una data factory |5\.000 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Sezioni simultanee per ogni set di dati |10 |10 |
| Byte per oggetto per gli oggetti pipeline<sup>1</sup> |200 kB |200 kB |
| Byte per oggetto per i dati impostare e collegati gli oggetti servizio<sup>1</sup> |100 kB |2\.000 KB |
| Core cluster su richiesta HDInsight Azure all'interno di una sottoscrizione<sup>2</sup> |60 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unità di spostamento dei dati per ogni esecuzione attività di copia cloud<sup>3</sup> |32 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Numero di tentativi delle esecuzioni di attività pipeline |1\.000 |MaxInt (32 bit) |

<sup>1</sup> pipeline, set di dati e oggetti servizio collegato rappresentano un raggruppamento logico del carico di lavoro. Limiti per questi oggetti non riguardano la quantità di dati è possibile spostare ed elaborare con Azure Data Factory. Data Factory è progettato per la scalabilità per gestire petabyte di dati.

<sup>2</sup> I core HDInsight su richiesta vengono allocati all'esterno della sottoscrizione che contiene la data factory. Di conseguenza, il limite precedente è il limite di core a livello di Data Factory per i core HDInsight on demand. È diverso dal limite principale associato alla sottoscrizione di Azure.

<sup>3</sup> l'unità di spostamento dati cloud (DMU) per la versione 1 viene utilizzata in un'operazione di copia da cloud a cloud, altre informazioni, vedere [unità di spostamento dei dati (versione 1) Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Per informazioni sulla fatturazione, vedere [prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Risorsa** | **Limite inferiore predefinito** | **Limite minimo** |
| --- | --- | --- |
| Intervallo di pianificazione |15 minuti |15 minuti |
| Intervallo tra tentativi |1 secondo |1 secondo |
| Valore di timeout del tentativo |1 secondo |1 secondo |

#### <a name="web-service-call-limits"></a>Limiti di chiamata del servizio Web
Azure Resource Manager presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di [limiti API di gestione risorse di Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
