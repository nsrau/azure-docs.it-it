---
title: File di inclusione
description: File di inclusione
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238583"
---
I limiti seguenti si applicano alle risorse di Azure Log Analitica per ogni sottoscrizione.

| Resource | Limite predefinito | Commenti
| --- | --- | --- |
| Numero di aree di lavoro gratuite per sottoscrizione | 10 | Non è possibile aumentare questo limite. |
| Numero di aree di lavoro a pagamento per sottoscrizione | N/D | Si è limitata dal numero di risorse all'interno di un gruppo di risorse e il numero di gruppi di risorse per ogni sottoscrizione. | 

>[!NOTE]
>A partire dal 2 aprile 2018, nuove aree di lavoro in una nuova sottoscrizione usare automaticamente il *Per GB* piano tariffario. Per le sottoscrizioni esistenti create prima del 2 aprile o per una sottoscrizione collegata a un'iscrizione con contratto Enterprise esistente, è possibile continuare a scegliere tra tre piani tariffari per nuove aree di lavoro. 
>

I limiti seguenti si applicano a ogni area di lavoro di Log Analitica.

|  | Gratuito | Standard | Premium | Autonoma | OMS | Per GB |
| --- | --- | --- | --- | --- | --- |--- |
| Volume di dati raccolti ogni giorno |500 MB<sup>1</sup> |Nessuna |Nessuna | Nessuna | Nessuna | Nessuna
| Periodo di conservazione dei dati |7 giorni |1 mese |12 mesi | 1 mese<sup>2</sup> | 1 mese<sup>2</sup>| 1 mese<sup>2</sup>|

<sup>1</sup>quando i clienti raggiungono il limite di trasferimento dei dati giornaliera 500 MB, analisi dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC.

<sup>2</sup>il periodo di conservazione per i piani dei prezzi Per GB, autonomo e OMS può essere aumentato a 730 giorni.

| Category | Limiti | Commenti
| --- | --- | --- |
| API dell'Agente di raccolta dati | Dimensioni massime per un singolo post sono di 30 MB.<br>Dimensione massima per i valori dei campi è 32 KB. | Dividere i volumi più grandi in più post.<br>I campi con una lunghezza superiore a 32 KB vengono troncati. |
| API di ricerca | 5000 record restituiti per i dati non aggregate.<br>500.000 record per i dati aggregati. | I dati aggregati sono una ricerca che include il `summarize` comando.
 
