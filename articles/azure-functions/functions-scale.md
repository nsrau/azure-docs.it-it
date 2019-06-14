---
title: Ridimensionamento e hosting di Funzioni di Azure | Microsoft Docs
description: Informazioni su come scegliere tra piano a consumo di funzioni di Azure e piano Premium.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funzioni, funzioni, piano a consumo, il piano premium, l'elaborazione di eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3253cc7e379ae63880d533f14bc76e7af5a4425a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050563"
---
# <a name="azure-functions-scale-and-hosting"></a>Ridimensionamento e hosting di Funzioni di Azure

Quando si crea un'app per le funzioni in Azure, è necessario scegliere un piano di hosting per l'app. Sono disponibili tre piani di hosting per funzioni di Azure: [Piano a consumo](#consumption-plan), [Premium-piano](#premium-plan), e [piano di servizio App](#app-service-plan).

Il piano di hosting che scelto determina i comportamenti seguenti:

* Modalità di ridimensionamento di app per le funzioni.
* Le risorse disponibili per ogni istanza dell'app funzioni.
* Supporto per funzionalità avanzate, ad esempio la connettività di rete virtuale.

I piani di consumo e Premium aggiungono automaticamente potenza di calcolo quando il codice è in esecuzione. L'app è aumentato quando è necessario per gestire il carico e scalabilità verso il basso al termine dell'esecuzione di codice. Per il piano a consumo, anche non devi pagare per le macchine virtuali inattive o riservare in anticipo la capacità.  

Piano Premium offre funzionalità aggiuntive, ad esempio premium istanze di calcolo, la possibilità di mantenere le istanze a caldo a tempo indeterminato e la connettività di rete virtuale.

Piano di servizio App consente di sfruttare i vantaggi dell'infrastruttura dedicata, che è gestire. App per le funzioni non supporta la scalabilità in base agli eventi, ovvero non è mai scale fino a zero. (Richiede che [Always on](#always-on) è abilitato.)

> [!NOTE]
> È possibile passare tra i piani di consumo e Premium, modificare la proprietà del piano della risorsa di app per la funzione.

## <a name="hosting-plan-support"></a>Supporto dei piani di hosting

Supporto della funzionalità rientra nelle due categorie seguenti:

* _Disponibile a livello generale (GA)_ : completamente supportato e approvato per l'uso in produzione.
* _Anteprima_: non è ancora completamente supportato e approvato per l'uso in produzione.

Nella tabella seguente indica il livello corrente di supporto per i piani di hosting tre, durante l'esecuzione in Windows o Linux:

| | Piano a consumo | Piano Premium | Piano dedicato |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | preview | GA |
| Linux | preview | N/D | GA |

## <a name="consumption-plan"></a>Piano a consumo

Quando si usa il piano a consumo, in modo dinamico istanze dell'host di funzioni di Azure vengono aggiunti e rimossi in base al numero di eventi in ingresso. Questo piano senza server offre la scalabilità automatica e sono previsti costi per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano A consumo, il timeout dell'esecuzione di una funzione si verifica dopo un periodo di tempo configurabile.

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. La fatturazione viene aggregata tra tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Il piano a consumo è l'opzione di hosting predefinita e offre i vantaggi seguenti:

* Addebiti solo quando le funzioni sono in esecuzione
* Aumento automatico del numero di istanze anche in periodo di carico elevato

App per le funzioni nella stessa area possono essere assegnati per lo stesso piano di consumo. Non sono svantaggio o impatto alla presenza di più App in esecuzione nello stesso piano di consumo. Assegnazione di più App per lo stesso piano di consumo non ha alcun impatto su resilienza, scalabilità o affidabilità di ogni app.

## <a name="premium-plan"></a>Piano Premium (anteprima)

Quando si usa il piano Premium, le istanze dell'host di funzioni di Azure vengono aggiunti e rimossi in base al numero di eventi in ingresso esattamente come il piano a consumo.  Piano Premium supporta le funzionalità seguenti:

* Istanze perennemente warm per evitare qualsiasi avvio a freddo
* Connettività di rete virtuale
* Durata di esecuzione illimitato
* Dimensioni delle istanze di Premium (un core, due core e quattro le istanze di core)
* Più prevedibili di prezzi
* Assegnazione di app ad alta densità per i piani con più App per le funzioni

Informazioni sul modo in cui è possibile configurare queste opzioni sono disponibili nel [documento di funzioni di Azure premium piano](functions-premium-plan.md).

Invece di fatturazione per ogni esecuzione e di memoria utilizzata, la fatturazione per il piano Premium si basa sul numero di secondi core, il tempo di esecuzione e sulla memoria usata tra le istanze riservate e necessari.  Almeno un'istanza deve essere at a caldo sempre. Ciò significa che vi sia un costo fisso mensile per ogni piano attivo, indipendentemente dal numero di esecuzioni.

Prendere in considerazione il piano premium di funzioni di Azure nelle situazioni seguenti:

* Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.
* Sono necessarie altre opzioni di utilizzo della CPU o memoria rispetto a quello fornito dal piano a consumo.
* Il codice deve eseguire più di [tempo di esecuzione massimo consentito](#timeout) nel piano a consumo.
* Sono necessarie funzionalità disponibili solo in un piano Premium, ad esempio la connettività di rete virtuale/VPN.

> [!NOTE]
> L'anteprima del piano premium supporta attualmente solo funzioni di Azure in Windows.

Quando si esegue funzioni JavaScript in un piano Premium, è consigliabile scegliere un'istanza con un minor numero di Vcpu. Per altre informazioni, vedere la [scegliere i piani Premium single core](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Piano dedicato (servizio App)

App per le funzioni eseguibili anche sulla stesse macchine virtuali dedicate di altre app di servizio App (Basic, Standard, Premium e agli SKU isolati).

Prendere in considerazione un piano di servizio App nelle situazioni seguenti:

* Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
* Si desidera fornire un'immagine personalizzata in cui eseguire le funzioni.

Si paga la stessa App per le funzioni in un piano di servizio App come si farebbe per altre risorse del servizio App, ad esempio le app web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Con un piano di servizio App, è possibile scalare orizzontalmente manualmente aggiungendo altre istanze di macchina virtuale. È anche possibile abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/web-sites-scale.md). 

Quando si eseguono funzioni JavaScript in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. Per altre informazioni, vedere [scegliere i piani di servizio App single core](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Se per l'esecuzione si usa un piano di servizio app, è necessario abilitare l'impostazione **Always on** in modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzioni risulta inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. L'opzione Always on è disponibile solo nel piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Anche con Always On abilitato, il timeout di esecuzione per le singole funzioni è controllato dall'impostazione `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinare il piano di hosting di un'applicazione esistente

Per determinare il piano di hosting usato dall'app per le funzioni, vedere **Piano di servizio app/piano tariffario** nella scheda **Panoramica** dell'app per le funzioni nel [portale di Azure](https://portal.azure.com). Per i piani di servizio app è indicato anche il piano tariffario.

![Visualizzare il piano nel portale](./media/functions-scale/function-app-overview-portal.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per determinare il piano, come descritto di seguito:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando l'output di questo comando è `dynamic`, l'app per le funzioni è nel piano a consumo. Quando l'output da questo comando è `ElasticPremium`, app per le funzioni già inclusi nel piano Premium. Tutti gli altri valori indicano diversi livelli di un piano di servizio App.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Nel piano a qualsiasi app per le funzioni richiede un account di archiviazione di Azure generale che supporta l'archiviazione Blob di Azure, file, tabelle e di Accodamento. Questo avviene perché le funzioni si basano sull'archiviazione di Azure per operazioni quali la gestione dei trigger e la registrazione dell'esecuzione di funzione, ma alcuni account di archiviazione non supportano code e tabelle. Questi account, che includono l'archiviazione solo BLOB (tra cui Archiviazione premium) e gli account di archiviazione per utilizzo generico con replica di archiviazione con ridondanza della zona, vengono esclusi dalle selezioni di **Account di archiviazione** esistenti quando si crea un'app per le funzioni.

<!-- JH: Does using a Premium Storage account improve perf? -->

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Come funzionano i piani di consumo e premium

Nell'uso e i piani premium, l'infrastruttura di funzioni di Azure ridimensiona le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi che attivano le relative funzioni. Ogni istanza dell'host di funzioni nel piano a consumo è limitata a 1,5 GB di memoria e una CPU.  Un'istanza dell'host è l'app intera funzione, vale a dire tutte le funzioni all'interno di una risorsa di condivisione di app di funzione all'interno di un'istanza e nello stesso momento. App per le funzioni che condividono lo stesso piano di consumo vengono ridimensionati in modo indipendente.  Il piano premium, le dimensioni del piano determinerà la memoria disponibile e la CPU per tutte le app nel piano in quell'istanza.  

I file di codice di funzione vengono archiviati nelle condivisioni di file di Azure nell'account di archiviazione principale della funzione. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

> [!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, può verificarsi un ritardo massimo di 10 minuti per l'elaborazione di nuovi BLOB. Questo ritardo si verifica in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per evitare questo ritardo di avvio a freddo, usare il piano Premium oppure usare il [trigger griglia di eventi](functions-bindings-event-grid.md). Per altre informazioni, vedere l'articolo di riferimento sull'[associazione del trigger BLOB](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scala per le funzioni di Azure è l'app per le funzioni. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene ridotto a zero quando non è in esecuzione alcuna funzione in un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. Esistono alcuni aspetti complessi della scalabilità comportamenti da tenere presenti:

* Un'app per le funzioni viene ridimensionata solo fino a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.
* Per i trigger HTTP, le nuove istanze verranno allocate al massimo una volta ogni secondo.
* Per i trigger non HTTP, le nuove istanze verranno allocate al massimo una volta ogni 30 secondi.

Trigger distinti possono avere limiti di ridimensionamento diversi come illustrato di seguito:

* [Hub eventi](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Esistono molti aspetti di un'app per le funzioni che hanno un impatto sull'accuratezza del ridimensionamento, ad esempio la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices). È inoltre necessario comprendere il funzionamento delle connessioni quando l'app per le funzioni viene ridimensionata. Per altre informazioni, vedere [How to manage connections in Azure Functions](manage-connections.md) (Come gestire le connessioni in Funzioni di Azure).

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per i diversi piani è descritto in dettaglio nel [pagina dei prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/). L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti:

* **Utilizzo delle risorse in gigabyte al secondo (GB-s)** . Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

Query utili e informazioni su come comprendere la fattura di consumo reperibili [sulle domande frequenti su fatturazione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limiti del servizio

Nella tabella seguente indica i limiti applicabili alle App per le funzioni durante l'esecuzione in vari piani di hosting:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
