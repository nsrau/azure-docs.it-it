---
title: File di inclusione
description: File di inclusione
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293461"
---
I limiti seguenti si applicano a ogni area di lavoro di Log Analitica nello corrente in base al consumo al piano tariffario introdotti in aprile 2018:

|     | Per GB 2018 |
| --- | --- | 
| Volume di dati raccolti ogni giorno | Nessuna |
| Periodo di conservazione dei dati | 30 a 730 giorni<sup>1</sup> |

I limiti seguenti si applicano a ogni area di lavoro di Log Analitica legacy più recenti sui piani tariffari:

|  | Gratuito | Autonomo (Per GB) | Per ogni nodo (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Volume di dati raccolti ogni giorno |500 MB<sup>2</sup> |Nessuna |Nessuna |
| Periodo di conservazione dei dati |7 giorni | 30 a 730 giorni<sup>1</sup> | 30 a 730 giorni<sup>1</sup> |

I limiti seguenti si applicano a ogni area di lavoro di Log Analitica legacy meno recente piani tariffari:

|  | Standard | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| Volume di dati raccolti ogni giorno | Nessuna | Nessuna | 
| Periodo di conservazione dei dati |30 giorni | 365 giorni |

<sup>1</sup>conservazione dei dati oltre i 31 giorni è disponibile per gli addebiti aggiuntivi. Altre informazioni sui [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>quando l'area di lavoro raggiunge il limite di trasferimento dei dati giornaliera 500 MB, l'analisi dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC.

>[!NOTE]
>A seconda di quanto tempo è stato usato Log Analitica, è necessario l'accesso per i livelli di prezzi precedente. Altre informazioni sulle [legacy di Log Analitica piani tariffari](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

I limiti seguenti si applicano alle risorse di Azure Log Analitica (aree di lavoro) per ogni sottoscrizione.

| Piano tariffario    | Numero di aree di lavoro per ogni sottoscrizione | Commenti
| --- | --- | --- |
| Livello gratuito  | 10 | Non è possibile aumentare questo limite. |
| Tutti i livelli diversi da quello gratuito | N/D | Si è limitata dal numero di risorse all'interno di un gruppo di risorse e il numero di gruppi di risorse per ogni sottoscrizione. | 

I limiti seguenti si applicano alle API di Analitica di Log:

| Category | Limiti | Commenti
| --- | --- | --- |
| API dell'Agente di raccolta dati | Dimensioni massime per un singolo post sono di 30 MB.<br>Dimensione massima per i valori dei campi è 32 KB. | Dividere i volumi più grandi in più post.<br>I campi con una lunghezza superiore a 32 KB vengono troncati. |
| API di ricerca | 5000 record restituiti per i dati non aggregate.<br>500.000 record per i dati aggregati. | I dati aggregati sono una ricerca che include il `summarize` comando.
 
