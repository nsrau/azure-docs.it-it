---
title: Scegliere un piano di hosting per Funzioni di Azure | Microsoft Docs
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
ms.date: 03/14/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 9b5dabe5e27e68a4a9f140d4f07131caf7306e32
ms.lasthandoff: 03/15/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>Scegliere il piano di servizio corretto per Funzioni di Azure

## <a name="introduction"></a>Introduzione

Funzioni di Azure offre due piani di servizio diversi: piano a consumo e piano di servizio app. Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce il numero di istanze quando il codice non è in esecuzione. Ciò significa che non è necessario pagare per macchine virtuali inattive o riservare capacità prima che sia necessaria. In questo articolo è incentrato sul piano di servizio a consumo. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Se non si ha ancora familiarità con Funzioni di Azure, vedere l'articolo [Panoramica di Funzioni di Azure](functions-overview.md).

## <a name="service-plan-options"></a>Opzioni del piano di servizio

Quando si crea un'app per le funzioni, è necessario configurare un piano di hosting per le funzioni contenute nell'app. I piani di hosting disponibili sono il **piano a consumo** e il [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). È attualmente necessario scegliere il piano durante la creazione dell'app per le funzioni. Non è possibile scegliere un'opzione diversa dopo la creazione. È possibile aumentare o ridurre il numero di istanze nel [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Non sono attualmente supportate modifiche per il piano a consumo, perché il ridimensionamento è dinamico.

### <a name="consumption-plan"></a>Piano a consumo

Nel **piano a consumo** le app per le funzioni vengono assegnate a un'istanza di elaborazione di calcolo. Se necessario, altre istanze vengono aggiunte o rimosse in modo dinamico. Le funzioni vengono eseguite in parallelo riducendo al minimo il tempo totale necessario per elaborare le richieste. Il tempo di esecuzione per ogni funzione viene aggregato in base all'app per le funzioni che le contiene. Il costo è basato sulle dimensioni della memoria e sul tempo totale di esecuzione in tutte le funzioni in un'app per le funzioni misurato in gigabyte al secondo. Si tratta di un'ottima scelta se le esigenze di calcolo sono discontinue o le tempistiche del processo tendono a essere molto brevi, poiché consente di pagare solo per le risorse di calcolo quando vengono effettivamente usate. La sezione successiva illustra informazioni dettagliate sul funzionamento del piano a consumo.

### <a name="app-service-plan"></a>Piano di servizio app

Nel **piano di servizio app** le app per le funzioni vengono eseguite in macchine virtuali dedicate, analogamente alle app Web per SKU Basic, Standard o Premium. Le macchine virtuali dedicate vengono allocate alle app del servizio app e alle app per le funzioni e sono sempre disponibili, indipendentemente dal fatto che il codice sia in esecuzione. È una buona opzione in caso di VM esistenti sottoutilizzate che eseguono già altro codice o se si prevede di eseguire funzioni in modo continuativo o quasi. Una macchina virtuale separa il costo per tempo di esecuzione e dimensioni della memoria. È quindi possibile limitare il costo di molte funzioni a esecuzione prolungata al costo delle macchine virtuali sulle quali vengono eseguite. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

## <a name="how-the-consumption-plan-works"></a>Funzionamento del piano a consumo

Il piano a consumo ridimensiona automaticamente le risorse di CPU e memoria aggiungendo altre istanze di elaborazione in base ai requisiti di runtime delle funzioni in un'app per le funzioni. A ogni istanza di elaborazione delle app per le funzioni vengono allocate risorse di memoria fino a un massimo di 1,5 GB.

Quando si esegue un piano a consumo, se un'app per le funzioni è inattiva possono essere necessari fino a 10 minuti al giorno per l'elaborazione di nuovi BLOB. Se l'app per le funzioni è in esecuzione, i BLOB vengono elaborati più rapidamente. Per evitare questo ritardo iniziale, usare un normale piano di servizio app con l'opzione Always On abilitata o usare un altro meccanismo per attivare l'elaborazione dei BLOB, ad esempio un messaggio in coda che contiene il nome del BLOB. 

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, code e tabelle. Funzioni di Azure usa internamente Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione, come gli account di archiviazione solo BLOB (tra cui Archiviazione Premium) e gli account di archiviazione di uso generico con replica ZRS, non supportano code e tabelle. Questi account vengono filtrati dal pannello Account di archiviazione quando si crea una nuova app per le funzioni.

Quando si usa il piano di hosting a consumo, il contenuto delle app per le funzioni (ad esempio i file del codice di funzione e la configurazione di binding) viene archiviato nelle condivisioni di File di Azure nell'account di archiviazione principale. Se si elimina l'account di archiviazione principale, il contenuto verrà eliminato e non potrà essere ripristinato.

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure] (../storage/storage-introduction.md#introducing-the-azure-storage-services).

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni usa un controller di scalabilità per valutare le esigenze di calcolo in base ai trigger configurati e per decidere quando aumentare o ridurre il numero di istanze. Il controller di scalabilità elabora continuamente i suggerimenti per i requisiti di memoria e i punti dati specifici per i trigger. Ad esempio, nel caso di un trigger dell'archiviazione code di Azure, i punti dati includono la lunghezza della coda e tempo di attesa della voce meno recente.

![](./media/functions-scale/central-listener.png)

L'unità di ridimensionamento è l'app per le funzioni. In questo caso per ridimensionamento si intende l'aggiunta di istanze di un'app per le funzioni. In caso di riduzione delle richieste di calcolo, invece, le istanze delle app per le funzioni vengono rimosse. Il numero di istanze viene ridotto a zero quando nessuna app per le funzioni è in esecuzione. 

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per il piano a consumo viene illustrata in modo dettagliato nella [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions). L'utilizzo viene segnalato per ogni app per le funzioni, solo durante l'esecuzione del codice. Per la fatturazione vengono usate le unità seguenti: 
* Il **consumo delle risorse in GB/s (gigabyte al secondo)**, calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in esecuzione in un'app per le funzioni. 
* Le **esecuzioni**, conteggiate ogni volta che una funzione viene eseguita in risposta a un evento attivato da un binding.

