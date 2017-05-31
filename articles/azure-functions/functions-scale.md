---
title: Piani a consumo e piani di servizio app di Funzioni di Azure | Microsoft Docs
description: "Comprendere le modalità di scalabilità di Funzioni di Azure per soddisfare le esigenze dei carichi di lavoro basati su eventi."
services: functions
documentationcenter: na
author: lindydonna
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
ms.date: 05/15/2017
ms.author: donnam, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25df99203ccb194d75985fca42d3d05609dc7d7c
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Piani a consumo e piani di servizio app di Funzioni di Azure 

## <a name="introduction"></a>Introduzione

È possibile eseguire Funzioni di Azure in due modalità diverse, ovvero con piano a consumo e piano di servizio app. Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce le prestazioni quando il codice non è in esecuzione. Non è quindi necessario pagare per le macchine virtuali inattive e non è necessario riservare in anticipo la capacità. Questo articolo è incentrato sul piano a consumo. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Se non si ha ancora familiarità con Funzioni di Azure, vedere l'articolo [Panoramica di Funzioni di Azure](functions-overview.md).

Quando si crea un'app per le funzioni, è necessario configurare un piano di hosting per le funzioni contenute nell'app. I piani di hosting disponibili sono il **piano a consumo** e il [**piano di servizio app**](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). In entrambe le modalità le funzioni vengono eseguite da un'istanza dell'*host di Funzioni di Azure*. Il tipo di piano controlla: 1) la modalità di aumento del numero di istanze e 2) le risorse disponibili per ogni host.

La scelta del tipo di piano deve essere attualmente effettuata durante la creazione dell'app per le funzioni e non può essere modificata in seguito. È possibile aumentare o ridurre il numero di istanze nel [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Nel piano a consumo Funzioni di Azure gestisce automaticamente l'allocazione di tutte le risorse.

## <a name="consumption-plan"></a>Piano a consumo

Quando si usa un **piano a consumo**, le istanze dell'host di Funzioni di Azure vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso. Questo piano offre la scalabilità automatica e sono previsti addebiti per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano a consumo una funzione può essere eseguita al massimo per cinque minuti. 

La fatturazione è basata sul tempo di esecuzione e sulla memoria usata ed è aggregata per tutte le funzioni in un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure].

Il piano a consumo è l'impostazione predefinita e offre i vantaggi seguenti:
- Addebiti solo quando le funzioni sono in esecuzione
- Aumento automatico del numero di istanze anche in periodo di carico elevato

## <a name="app-service-plan"></a>Piano di servizio app

Nel **piano di servizio app** le app per le funzioni vengono eseguite in macchine virtuali dedicate in SKU Basic, Standard, Premium, analogamente alle app Web. Le macchine virtuali dedicate vengono allocate alle app del servizio app, ovvero l'host di funzioni è sempre in esecuzione.

Prendere in considerazione un piano di servizio app nei casi seguenti:
- Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
- Si prevede che le app per le funzioni vengano eseguite in modo continuo o quasi continuo.
- Sono necessarie altre opzioni per CPU o memoria, rispetto alle opzioni disponibili nel piano a consumo.
- È necessario un tempo di esecuzione superiore al tempo di esecuzione massimo consentito nel piano a consumo.

Una macchina virtuale separa il costo per tempo di esecuzione e dimensioni della memoria. Non si pagherà quindi più del costo dell'istanza di macchina virtuale allocata. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Con un piano di servizio app, è possibile aumentare manualmente il numero di istanze aggiungendo altre istanze di macchine virtuali oppure abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service-web/web-sites-scale.md). Se si prevede di eseguire funzioni JavaScript in un piano di servizio App, è necessario scegliere un piano con un minor numero di core. Per altre informazioni, vedere le [informazioni di riferimento su JavaScript per le funzioni](functions-reference-node.md#choose-single-core-app-service-plans).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### AlwaysOn

Se per l'esecuzione si usa un piano di servizio app, è necessario abilitare l'impostazione **AlwaysOn**, in modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzione risulterà inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi effettivamente le funzioni. Questo approccio è simile alla necessità di abilitazione di AlwaysOn per i processi Web. 

AlwaysOn è disponibile solo nel piano di servizio app. In un piano a consumo la piattaforma attiva automaticamente le app per le funzioni.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

In un piano a consumo o un piano di servizio app è necessario che un'app per le funzioni abbia un account di archiviazione di Azure che supporta l'archiviazione BLOB, code e tabelle. Funzioni di Azure usa internamente Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione, ad esempio gli account di archiviazione solo BLOB (tra cui Archiviazione Premium) e gli account di archiviazione di uso generico con replica ZRS, non supportano code e tabelle. Tali account vengono filtrati dal pannello Account di archiviazione quando si crea un'app per le funzioni.

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure] (../storage/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Funzionamento del piano a consumo

Il piano a consumo offre la scalabilità automatica di CPU e di risorse di memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi che attivano le rispettive funzioni. Ogni istanza dell'host di Funzioni è limitata al massimo a 1,5 GB di memoria.

Quando si usa un piano di hosting a consumo, i file di codice delle funzioni vengono archiviati nelle condivisioni di File di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto viene eliminato e non può essere ripristinato.

> [!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per evitare questo ritardo iniziale, prendere in considerazione una delle opzioni seguenti:
> - Usare un piano di servizio app con l'opzione AlwaysOn abilitata.
> - Usare un altro meccanismo per attivare l'elaborazione dei BLOB, ad esempio un messaggio della coda che contiene il nome del BLOB. Per un esempio, vedere il [trigger della coda con binding di input del BLOB](functions-bindings-storage-blob.md#input-sample).

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scalabilità è l'app per le funzioni. In caso di aumento del numero di istanze, vengono allocate più risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, invece, le istanze delle app per le funzioni vengono rimosse. Il numero di istanze viene ridotto a zero quando non è in esecuzione alcuna funzione in un'app per le funzioni.

![](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per il piano a consumo viene illustrata in modo dettagliato nella [pagina relativa ai prezzi per Funzioni di Azure]. L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti: 
* **Utilizzo delle risorse in GB-s (gigabyte al secondo)**. Calcolato come una combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un evento attivato da un binding.

[pagina relativa ai prezzi per Funzioni di Azure]: https://azure.microsoft.com/pricing/details/functions
