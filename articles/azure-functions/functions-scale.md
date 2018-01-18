---
title: Ridimensionamento e hosting di Funzioni di Azure | Microsoft Docs
description: Informazioni su come scegliere tra il piano di utilizzo di Funzioni di Azure e piano di servizio app.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: funzioni di azure, funzioni, piano di utilizzo, piano di servizio app, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5be2fe57287f816434b6d6fdf40dbbcb0dd435f4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="azure-functions-scale-and-hosting"></a>Ridimensionamento e hosting di Funzioni di Azure

È possibile eseguire Funzioni di Azure in due modalità diverse, ovvero con piano a consumo e piano di servizio app di Azure. Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce le prestazioni quando il codice non è in esecuzione. Non è necessario pagare per le macchine virtuali inattive e non è necessario riservare in anticipo la capacità. Questo articolo è incentrato sul piano a consumo, un modello App [senza server](https://azure.microsoft.com/overview/serverless-computing/). Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

>[!NOTE]  
> L'[hosting in Linux](functions-create-first-azure-function-azure-cli-linux.md) è attualmente disponibile solo per i piani di servizio app.

Se non si ha ancora familiarità con Funzioni di Azure, vedere la [panoramica di Funzioni di Azure](functions-overview.md).

Quando si crea un'app per le funzioni, è necessario configurare un piano di hosting per le funzioni contenute nell'app. In entrambe le modalità, le funzioni vengono eseguite da un'istanza dell'*host di Funzioni di Azure*. Il tipo di piano determina:

* Il modo in cui vengono aumentate le istanze dell'host.
* Le risorse disponibili per ogni host.

È attualmente necessario scegliere il tipo di piano di hosting durante la creazione dell'app per le funzioni. Non è possibile modificare il piano in un secondo momento. 

In un piano di servizio app è possibile applicare la scalabilità tra i livelli per allocare quantità diverse di risorse. Nel piano a consumo Funzioni di Azure gestisce automaticamente l'allocazione di tutte le risorse.

## <a name="consumption-plan"></a>Piano a consumo

Quando si usa un piano a consumo, le istanze dell'host di Funzioni di Azure vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso. Questo piano offre la scalabilità automatica e sono previsti costi per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano a consumo, una funzione può essere eseguita al massimo per 10 minuti. 

> [!NOTE]
> Il timeout predefinito per le funzioni in un piano a consumo è di 5 minuti. Il valore può essere aumentato a 10 minuti per l'app per le funzioni modificando la proprietà `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. La fatturazione viene aggregata tra tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure].

Il piano a consumo è l'opzione di hosting predefinita e offre i vantaggi seguenti:
- Addebiti solo quando le funzioni sono in esecuzione.
- Aumento automatico del numero di istanze anche in periodo di carico elevato.

## <a name="app-service-plan"></a>Piano di servizio app

Nel piano di servizio app le app per le funzioni vengono eseguite in macchine virtuali dedicate in SKU Basic, Standard, Premium e Isolati, analogamente alle app Web, alle app per le API e alle app per dispositivi mobili. Le macchine virtuali dedicate vengono allocate alle app del servizio app, ovvero l'host di funzioni è sempre in esecuzione. I piani di servizio app supportano Linux.

Prendere in considerazione un piano di servizio app nei casi seguenti:
- Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
- Si prevede che le app per le funzioni vengano eseguite in modo continuo o quasi continuo. In questo caso, un piano di servizio app può essere più conveniente.
- Sono necessarie altre opzioni per CPU o memoria, rispetto alle opzioni disponibili nel piano a consumo.
- È necessario un tempo di esecuzione superiore al tempo di esecuzione massimo consentito nel piano a consumo (di 10 minuti).
- Sono necessarie funzionalità disponibili solo in un piano di servizio app, ad esempio il supporto per Ambiente del servizio app, la connettività di rete virtuale/VPN e dimensioni maggiori delle macchine virtuali. 
- Si vuole eseguire l'app per le funzioni in Linux o fornire un'immagine personalizzata in cui eseguire le funzioni.

Una macchina virtuale separa i costi associati al numero di esecuzioni, al tempo di esecuzione e alla memoria usata. Non si pagherà quindi più del costo dell'istanza di macchina virtuale allocata. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Con un piano di servizio app, è possibile aumentare manualmente il numero di istanze aggiungendo altre istanze di macchine virtuali oppure abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/web-sites-scale.md). 

Se si prevede di eseguire funzioni JavaScript in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. Per altre informazioni, vedere [Scegliere i piani di servizio app single core](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### AlwaysOn

Se per l'esecuzione si usa un piano di servizio app, è necessario abilitare l'impostazione **Sempre online** in modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzione risulterà inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. Questo approccio è simile alla necessità di abilitazione di AlwaysOn per i processi Web. 

L'opzione Sempre online è disponibile solo nel piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

In un piano a consumo o un piano di servizio app è necessario che un'app per le funzioni abbia un account di Archiviazione di Microsoft Azure generale che supporti l'archiviazione BLOB, code, file e tabelle. Funzioni di Azure usa internamente Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione non supportano code e tabelle, ad esempio gli account di archiviazione solo BLOB (tra cui Archiviazione Premium) e gli account di archiviazione di uso generico con replica archiviazione con ridondanza della zona. Tali account vengono filtrati dal pannello **Account di archiviazione** quando si crea un'app per le funzioni.

<!-- JH: Does using a PRemium Storage account improve perf? -->

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure](../storage/common/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Funzionamento del piano a consumo

Nel piano a consumo il controller di scalabilità offre la scalabilità automatica di CPU e delle risorse di memoria aggiungendo altre istanze dell'host di Funzioni di Azure, in base al numero di eventi che attivano le rispettive funzioni. Ogni istanza dell'host di Funzioni è limitata a 1,5 GB di memoria.  Un'istanza dell'host è l'app per le funzioni, vale a dire che tutte le funzioni all'interno di un'app per le funzioni condividono le risorse all'interno di un'istanza e vengono ridimensionate contemporaneamente.

Quando si usa un piano di hosting a consumo, i file di codice delle funzioni vengono archiviati nelle condivisioni di File di Azure nell'account di archiviazione principale della funzione. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

> [!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, può verificarsi un ritardo massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per evitare questo ritardo iniziale, prendere in considerazione una delle opzioni seguenti:
> - Eseguire l'hosting dell'app per le funzioni in un piano di servizio app con l'opzione Always On abilitata.
> - Usare un altro meccanismo per attivare l'elaborazione dei BLOB, ad esempio una sottoscrizione di Griglia di eventi o un messaggio della coda che contiene il nome del BLOB. Per un esempio, vedere gli [esempi di associazione di input BLOB](functions-bindings-storage-blob.md#input---example).

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scalabilità è l'app per le funzioni. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene ridotto a zero quando non è in esecuzione alcuna funzione in un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. Esistono tuttavia alcuni aspetti del ridimensionamento comuni a livello di sistema:
* Un'app per le funzioni verrà ridimensionata solo fino a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.
* Le nuove istanze verranno allocate al massimo una volta ogni 10 secondi.

Trigger distinti possono avere limiti di ridimensionamento diversi come illustrato di seguito:

* [Hub eventi](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Esistono molti aspetti di un'app per le funzioni che hanno un impatto sull'accuratezza del ridimensionamento, ad esempio la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices).

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per il piano a consumo viene illustrata in modo dettagliato nella [pagina relativa ai prezzi per Funzioni di Azure]. L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti: 
* **Utilizzo delle risorse in gigabyte al secondo (GB-s)**. Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

[pagina relativa ai prezzi per Funzioni di Azure]: https://azure.microsoft.com/pricing/details/functions
