---
title: Come ridimensionare Funzioni di Azure | Documentazione Microsoft
description: "Comprendere le modalità di scalabilità di Funzioni di Azure per soddisfare le esigenze dei carichi di lavoro basati su eventi."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>Ridimensionamento di Funzioni di Azure

## <a name="introduction"></a>Introduzione

La piattaforma di Funzioni di Azure alloca funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce il numero di istanze quando il codice non è in esecuzione. Ciò significa che non è necessario pagare per macchine virtuali inattive o riservare capacità prima che sia necessaria. Il meccanismo per questa funzionalità è basato sul piano di servizio a consumo. Questo articolo fornisce una panoramica del funzionamento del piano di servizio a consumo. 

Se non si ha ancora familiarità con Funzioni di Azure, vedere l'articolo [Panoramica di Funzioni di Azure](functions-overview.md).

## <a name="choose-a-service-plan"></a>Scegliere un piano di servizio

Quando si crea un'app per le funzioni, è necessario configurare un piano di hosting per le funzioni contenute nell'app. I piani di hosting disponibili sono il **piano a consumo** e il [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). È attualmente necessario scegliere il piano durante la creazione dell'app per le funzioni. Non è possibile scegliere un'opzione diversa dopo la creazione. È possibile aumentare o ridurre il numero di istanze nel [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Non sono attualmente supportate modifiche per il piano a consumo, perché il ridimensionamento è dinamico.

### <a name="consumption-plan"></a>Piano a consumo

Nel **piano a consumo** le app per le funzioni vengono assegnate a un'istanza di elaborazione di calcolo. Se necessario, altre istanze vengono aggiunte o rimosse in modo dinamico. Le funzioni vengono eseguite in parallelo riducendo al minimo il tempo totale necessario per elaborare le richieste. Il tempo di esecuzione per ogni funzione viene aggregato in base all'app per le funzioni che le contiene. Il costo è basato sulle dimensioni della memoria e sul tempo totale di esecuzione in tutte le funzioni in un'app per le funzioni misurato in gigabyte al secondo. Si tratta di un'ottima scelta se le esigenze di calcolo sono discontinue o le tempistiche del processo tendono a essere molto brevi, poiché consente di pagare solo per le risorse di calcolo quando vengono effettivamente usate. 

### <a name="app-service-plan"></a>Piano di servizio app

Nel **piano di servizio app** le app per le funzioni vengono eseguite in macchine virtuali dedicate, analogamente alle app Web per SKU Basic, Standard, o Premium. Le macchine virtuali dedicate vengono allocate alle app del servizio app e alle app per le funzioni e sono sempre disponibili, indipendentemente dal fatto che il codice sia in esecuzione. È una buona opzione in caso di VM esistenti sottoutilizzate che eseguono già altro codice o se si prevede di eseguire funzioni in modo continuativo o quasi. Una macchina virtuale separa il costo per tempo di esecuzione e dimensioni della memoria. È quindi possibile limitare il costo di molte funzioni a esecuzione prolungata al costo di una o più macchine virtuali sulle quali vengono eseguite.

## <a name="consumption-service-plan"></a>Piano di servizio a consumo

Il piano di servizio a consumo ridimensiona automaticamente le risorse di CPU e memoria aggiungendo altre istanze di elaborazione in base alle esigenze di runtime delle funzioni nell'app per le funzioni. A ogni istanza di elaborazione delle app per le funzioni vengono allocate risorse di memoria fino a un massimo di 1,5 GB.

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

La piattaforma di Funzioni di Azure usa un listener centrale per valutare le esigenze di calcolo in base ai trigger configurati e per decidere quando aumentare o ridurre il numero di istanze. Il listener centrale elabora costantemente i suggerimenti per i requisiti di memoria e punti dati specifici per i trigger. Ad esempio, nel caso di un trigger dell'archiviazione code di Azure, i punti dati includono la lunghezza della coda e tempo di attesa per la voce meno recente.

![](./media/functions-scale/central-listener.png)

L'unità di ridimensionamento è l'app per le funzioni. In questo caso per ridimensionamento si intende l'aggiunta di istanze di un'app per le funzioni. In caso di riduzione delle richieste di calcolo, invece, le istanze delle app per le funzioni vengono rimosse, riducendo il numero a zero quando non sono in esecuzione istanze. 

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per il piano di servizio a consumo viene illustrata in modo dettagliato nella [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions). L'utilizzo viene segnalato per ogni app per le funzioni, solo durante l'esecuzione del codice. Per la fatturazione vengono usate le unità seguenti: 
* Il **consumo delle risorse in GB/s (gigabyte al secondo)**, calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in esecuzione in un'app per le funzioni. 
* Le **esecuzioni**, conteggiate ogni volta che una funzione viene eseguita in risposta a un evento attivato da un binding.



<!--HONumber=Nov16_HO4-->


