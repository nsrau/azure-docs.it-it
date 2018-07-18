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
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34307489"
---
I limiti seguenti si applicano alle risorse di Log Analytics per ogni sottoscrizione:

| Risorsa | Limite predefinito | Commenti
| --- | --- | --- |
| Numero di aree di lavoro gratuite per sottoscrizione | 10 | Non è possibile aumentare questo limite |
| Numero di aree di lavoro a pagamento per sottoscrizione | N/D | Il limite è dato dal numero di risorse all'interno di un gruppo di risorse e dal numero di gruppi di risorse per ogni sottoscrizione | 

>[!NOTE]
>A partire dal 2 aprile 2018 le nuove aree di lavoro in una nuova sottoscrizione useranno automaticamente il piano tariffario *Per GB*.  Per le sottoscrizioni esistenti create prima del 2 aprile o per una sottoscrizione collegata a un registrazione di un Contratto Enterprise esistente, è possibile continuare a scegliere tra tre piani tariffari per le nuove aree di lavoro. 
>

I limiti seguenti si applicano a ogni area di lavoro di Log Analytics:

|  | Gratuito | Standard | Premium | Autonoma | OMS | Per GB |
| --- | --- | --- | --- | --- | --- |--- |
| Volume di dati raccolti ogni giorno |500 MB<sup>1</sup> |Nessuna |Nessuna | Nessuna | Nessuna | Nessuna
| Periodo di conservazione dei dati |7 giorni |1 mese |12 mesi | 1 mese<sup>2</sup> | 1 mese <sup>2</sup>| 1 mese <sup>2</sup>|

<sup>1</sup> Quando i clienti raggiungono il limite giornaliero di trasferimento dati di 500 MB, l'analisi dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC.

<sup>2</sup> Il periodo di conservazione dei dati per i piani tariffari Autonomo, OMS e Per GB può essere aumentato a 730 giorni.

| Categoria | Limiti | Commenti
| --- | --- | --- |
| API dell'Agente di raccolta dati | La dimensione massima per un singolo post è di 30 MB<br>La dimensione massima per i valori dei campi è di 32 KB | Dividere i volumi più grandi in più post<br>I campi con una lunghezza superiore a 32 KB vengono troncati. |
| API di ricerca | 5000 record restituiti per i dati non aggregati<br>500000 record per i dati aggregati | I dati aggregati sono una ricerca che include il comando `summarize`
 
