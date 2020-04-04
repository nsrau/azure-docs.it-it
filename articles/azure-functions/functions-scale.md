---
title: Ridimensionamento e hosting di Funzioni di Azure
description: Informazioni su come scegliere tra il piano di consumo di Funzioni di Azure e il piano Premium.Learn how to choose between Azure Functions Consumption plan and Premium plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ac0417e9d8adca168dd68e1721a1c9c890de1c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656951"
---
# <a name="azure-functions-scale-and-hosting"></a>Ridimensionamento e hosting di Funzioni di Azure

Quando si crea un'app per le funzioni in Azure, è necessario scegliere un piano di hosting per l'app. Sono disponibili tre piani di hosting per Funzioni di Azure: [Piano di consumo](#consumption-plan), [Piano Premium](#premium-plan)e Piano [dedicato (servizio app).](#app-service-plan)

Il piano di hosting scelto determina i seguenti comportamenti:

* Modalità di ridimensionamento dell'app per le funzioni.
* Risorse disponibili per ogni istanza dell'app per le funzioni.
* Supporto per funzionalità avanzate, ad esempio la connettività della rete virtuale di Azure.Support for advanced features, such as Azure Virtual Network connectivity.

Entrambi i piani Consumption e Premium aggiungono automaticamente potenza di calcolo quando il codice è in esecuzione. L'app viene ridimensionata quando necessario per gestire il carico e viene ridimensionata quando l'esecuzione del codice viene interrotta. Per il piano Consumo, inoltre, non è necessario pagare per le macchine virtuali inattive o prenotare la capacità in anticipo.  

Il piano Premium offre funzionalità aggiuntive, ad esempio istanze di elaborazione premium, la possibilità di mantenere le istanze calde a tempo indeterminato e la connettività della rete virtuale.

Il piano di servizio app consente di sfruttare l'infrastruttura dedicata gestita dall'utente. L'app per le funzioni non viene ridimensionata in base agli eventi, il che significa che non è mai scalabile a zero. (Richiede che [l'opzione Sempre attivata](#always-on) sia abilitata.

## <a name="hosting-plan-support"></a>Supporto del piano di hosting

Il supporto delle funzionalità rientra nelle due categorie seguenti:

* _Generalmente disponibile (GA):_ completamente supportato e approvato per l'uso in produzione.
* _Anteprima_: non ancora completamente supportato né approvato per l'uso in produzione.

La tabella seguente indica il livello corrente di supporto per i tre piani di hosting, quando viene eseguito su Windows o Linux:

| | Piano a consumo | Piano Premium | Piano dedicato |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Piano a consumo

Quando si usa il piano Consumo, le istanze dell'host Funzioni di Azure vengono aggiunte e rimosse dinamicamente in base al numero di eventi in ingresso. Questo piano senza server offre la scalabilità automatica e sono previsti costi per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano A consumo, il timeout dell'esecuzione di una funzione si verifica dopo un periodo di tempo configurabile.

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. La fatturazione viene aggregata tra tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Il piano a consumo è l'opzione di hosting predefinita e offre i vantaggi seguenti:

* Addebiti solo quando le funzioni sono in esecuzione
* Aumento automatico del numero di istanze anche in periodo di carico elevato

Le app per le funzioni nella stessa area possono essere assegnate allo stesso piano di consumo. Non c'è nessun inconveniente o impatto di avere più applicazioni in esecuzione nello stesso piano di consumo. L'assegnazione di più app allo stesso piano di consumo non ha alcun impatto sulla resilienza, sulla scalabilità o sull'affidabilità di ogni app.

Per ulteriori informazioni su come stimare i costi durante l'esecuzione in un piano di consumo, vedere [Understanding Consumption plan costs](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Piano Premium

Quando si usa il piano Premium, le istanze dell'host Funzioni di Azure vengono aggiunte e rimosse in base al numero di eventi in ingresso, proprio come il piano di consumo.  Il piano Premium supporta le seguenti funzionalità:

* Istanze sempre calde per evitare qualsiasi avvio a freddo
* Connettività di rete virtuale
* Durata esecuzione illimitata (60 minuti garantiti)
* Dimensioni delle istanze Premium (un core, due core e quattro istanze core)
* Prezzi più prevedibili
* Allocazione di app ad alta densità per piani con più app per funzioni

Informazioni su come configurare queste opzioni sono disponibili nel documento del piano Premium di Funzioni di [Azure.](functions-premium-plan.md)

Anziché la fatturazione per esecuzione e la memoria consumata, la fatturazione per il piano Premium si basa sul numero di secondi core e sulla memoria utilizzata nelle istanze necessarie e preriscaldate. Almeno un'istanza deve essere sempre calda per ogni piano. Ciò significa che esiste un costo mensile minimo per piano attivo, indipendentemente dal numero di esecuzioni. Tieni presente che tutte le app per le funzioni in un piano Premium condividono istanze preriscaldate e attive.

Si consideri il piano Premium di Funzioni di Azure nelle situazioni seguenti:Consider the Azure Functions Premium plan in the following situations:

* Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.
* Si dispone di un numero elevato di esecuzioni di piccole dimensioni e si dispone di una fattura di esecuzione elevata ma bassa fattura GB seconda nel piano di consumo.
* Sono necessarie più opzioni di CPU o memoria rispetto a quelle fornite dal piano Consumo.
* Il codice deve essere eseguito più a lungo del [tempo di esecuzione massimo consentito](#timeout) nel piano di consumo.
* Sono necessarie funzionalità disponibili solo in un piano Premium, ad esempio la connettività di rete virtuale.

Quando si eseguono funzioni JavaScript su un piano Premium, è consigliabile scegliere un'istanza con meno vCPU. Per ulteriori informazioni, vedere scegliere i [piani Premium single-core](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Piano dedicato (servizio app)

Le app per le funzioni possono essere eseguite anche nelle stesse macchine virtuali dedicate di altre app del servizio app (SKU Basic, Standard, Premium e Isolated).

Si consideri un piano di servizio app nelle situazioni seguenti:Consider an App Service plan in the following situations:

* Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
* Si desidera fornire un'immagine personalizzata su cui eseguire le funzioni.

Si paga lo stesso per le app per le funzioni in un piano di servizio app come per altre risorse del servizio app, ad esempio le app Web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Con un piano di servizio app, è possibile aumentare manualmente la scalabilità orizzontale aggiungendo altre istanze di macchina virtuale. È inoltre possibile abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/manage-scale-up.md). 

Quando si eseguono funzioni JavaScript in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. Per altre informazioni, vedere Scegliere piani di [servizio app single-core](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Sempre acceso

Se si esegue un piano di servizio app, è necessario abilitare l'impostazione **Sempre attivo** in modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzioni risulta inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. L'opzione Always on è disponibile solo nel piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Anche con Always On abilitato, il timeout di esecuzione per le singole funzioni è controllato dall'impostazione `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinare il piano di hosting di un'applicazione esistente

Per determinare il piano di hosting usato dall'app per le funzioni, vedere **Piano di servizio app** nella scheda **Panoramica** dell'app per le funzioni nel portale di [Azure.](https://portal.azure.com) Per visualizzare il piano tariffario, selezionare il nome del piano di **servizio app**e quindi scegliere **Proprietà** nel riquadro sinistro.

![Visualizzare il piano nel portale](./media/functions-scale/function-app-overview-portal.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per determinare il piano, come descritto di seguito:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando l'output di questo comando è `dynamic`, l'app per le funzioni è nel piano a consumo. Quando l'output di `ElasticPremium`questo comando è , l'app per le funzioni è nel piano Premium. Tutti gli altri valori indicano livelli diversi di un piano di servizio app.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

In qualsiasi piano, un'app per le funzioni richiede un account di archiviazione di Azure generale, che supporta l'archiviazione BLOB, coda, file e tabelle di Azure.On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage. Ciò è dovuto al fatto che Funzioni di Azure si basa su Archiviazione di Azure per operazioni quali la gestione di trigger e la registrazione delle esecuzioni di funzioni, ma alcuni account di archiviazione non supportano code e tabelle. Questi account, che includono l'archiviazione solo BLOB (tra cui Archiviazione premium) e gli account di archiviazione per utilizzo generico con replica di archiviazione con ridondanza della zona, vengono esclusi dalle selezioni di **Account di archiviazione** esistenti quando si crea un'app per le funzioni.

Lo stesso account di archiviazione usato dall'app per le funzioni può essere usato anche dai trigger e dalle associazioni per archiviare i dati dell'applicazione. Tuttavia, per le operazioni che richiedono un uso intensivo dell'archiviazione, è consigliabile usare un account di archiviazione separato.  

È certamente possibile per più app per le funzioni condividere lo stesso account di archiviazione senza problemi. Un buon esempio è quando si sviluppano più app nell'ambiente locale usando l'emulatore di archiviazione di Azure, che funziona come un account di archiviazione. 

<!-- JH: Does using a Premium Storage account improve perf? -->

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Come funzionano i piani Consumo e Premio

Nei piani Consumo e Premium, l'infrastruttura di Funzioni di Azure scala le risorse di CPU e memoria aggiungendo istanze aggiuntive dell'host Funzioni, in base al numero di eventi in cui vengono attivate le relative funzioni. Ogni istanza dell'host Funzioni nel piano Consumo è limitata a 1,5 GB di memoria e a una CPU.  Un'istanza dell'host è l'intera app per le funzioni, ovvero tutte le funzioni all'interno di un'app per le funzioni condividono la risorsa all'interno di un'istanza e vengono ridimensionate contemporaneamente. Le app per le funzioni che condividono lo stesso piano A consumo vengono ridimensionate in modo indipendente.  Nel piano Premium, le dimensioni del piano determineranno la memoria e la CPU disponibili per tutte le app in tale piano in tale istanza.  

I file di codice della funzione vengono archiviati nelle condivisioni File di Azure nell'account di archiviazione principale della funzione. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scala per Funzioni di Azure è l'app per le funzioni. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene infine *ridimensionato a* zero quando nessuna funzione è in esecuzione all'interno di un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. Ci sono alcune complessità dei comportamenti di ridimensionamento di cui essere a conoscenza:

* Una singola app per funzioni scala solo fino a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.
* Per i trigger HTTP, le nuove istanze vengono allocate, al massimo, una volta al secondo.
* Per i trigger non HTTP, le nuove istanze vengono allocate al massimo, una volta ogni 30 secondi. La scalabilità è più veloce quando si esegue un [piano Premium](#premium-plan).
* Per i trigger del bus di servizio, usare i diritti _di gestione_ sulle risorse per una scalabilità più efficiente. Con i diritti di _ascolto,_ il ridimensionamento non è accurato perché la lunghezza della coda non può essere utilizzata per informare le decisioni di ridimensionamento. Per ulteriori informazioni sull'impostazione dei diritti nei criteri di accesso al bus di servizio, vedere [Criteri di autorizzazione](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)di accesso condiviso .
* Per i trigger dell'Hub eventi, vedere le [indicazioni sul ridimensionamento](functions-bindings-event-hubs-trigger.md#scaling) nell'articolo di riferimento. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Esistono molti aspetti di un'app per le funzioni che hanno un impatto sull'accuratezza del ridimensionamento, ad esempio la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices). È inoltre necessario comprendere il funzionamento delle connessioni quando l'app per le funzioni viene ridimensionata. Per altre informazioni, vedere [How to manage connections in Azure Functions](manage-connections.md) (Come gestire le connessioni in Funzioni di Azure).

Per altre informazioni sul ridimensionamento in Python e Node.js, vedere Guida per gli sviluppatori di [Azure Functions Python - Scalabilità e concorrenza](functions-reference-python.md#scaling-and-concurrency) e Guida per gli sviluppatori di Azure Functions [Node.js - Scalabilità e concorrenza.](functions-reference-node.md#scaling-and-concurrency)

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per i diversi piani è descritta in dettaglio nella pagina dei prezzi di Funzioni di [Azure.](https://azure.microsoft.com/pricing/details/functions/) L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti:

* **Utilizzo delle risorse in gigabyte al secondo (GB-s)**. Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

Domande utili e informazioni su come comprendere la fattura di consumo possono essere trovate [sulle domande frequenti sulla fatturazione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limiti del servizio

La tabella seguente indica i limiti che si applicano alle app per le funzioni durante l'esecuzione nei vari piani di hosting:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
