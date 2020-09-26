---
title: Ridimensionamento e hosting di Funzioni di Azure
description: Scopri come scegliere tra il piano a consumo di funzioni di Azure e il piano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5dd703851054b058d96440a3a994b9d10eecfa3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372664"
---
# <a name="azure-functions-scale-and-hosting"></a>Ridimensionamento e hosting di Funzioni di Azure

Quando si crea un'app per le funzioni in Azure, è necessario scegliere un piano di hosting per l'app. Sono disponibili tre piani di hosting di base per funzioni di Azure: piano a [consumo](#consumption-plan), [piano Premium](#premium-plan)e [piano dedicato (servizio app)](#app-service-plan). Tutti i piani di hosting sono disponibili a livello generale nelle macchine virtuali Linux e Windows.

Il piano di hosting scelto determina i comportamenti seguenti:

* Modalità di ridimensionamento dell'app per le funzioni.
* Risorse disponibili per ogni istanza dell'app per le funzioni.
* Supporto per le funzionalità avanzate, ad esempio la connettività di rete virtuale di Azure.

Sia il consumo che i piani Premium aggiungono automaticamente la potenza di calcolo quando il codice è in esecuzione. L'app viene scalata orizzontalmente quando necessario per gestire il carico e viene ridimensionata quando l'esecuzione del codice viene arrestata. Per il piano a consumo, non è necessario pagare in anticipo le macchine virtuali inattive o la capacità di riserva.  

Il piano Premium offre funzionalità aggiuntive, ad esempio le istanze di calcolo Premium, la possibilità di limitare le istanze a tempo indefinito e la connettività VNet.

Il piano di servizio app consente di sfruttare i vantaggi dell'infrastruttura dedicata, che è possibile gestire. L'app per le funzioni non viene ridimensionata in base agli eventi, il che significa che non viene mai ridimensionato in zero. Richiede che [Always on](#always-on) sia abilitato.

Per un confronto dettagliato tra i diversi piani di hosting (incluso l'hosting basato su Kubernetes), vedere la [sezione confronto dei piani di hosting](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Piano a consumo

Quando si usa il piano a consumo, le istanze dell'host di funzioni di Azure vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso. Questo piano serverless offre la scalabilità automatica e sono previsti costi per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano A consumo, il timeout dell'esecuzione di una funzione si verifica dopo un periodo di tempo configurabile.

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. L'utilizzo viene aggregato in tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Il piano a consumo è l'opzione di hosting predefinita e offre i vantaggi seguenti:

* Addebiti solo quando le funzioni sono in esecuzione
* Aumento automatico del numero di istanze anche in periodo di carico elevato

Le app per le funzioni nella stessa area possono essere assegnate allo stesso piano a consumo. Non ci sono svantaggi o conseguenze per l'esecuzione di più app nello stesso piano a consumo. L'assegnazione di più app allo stesso piano a consumo non ha alcun effetto sulla resilienza, la scalabilità o l'affidabilità di ogni app.

Per altre informazioni su come stimare i costi durante l'esecuzione in un piano a consumo, vedere [informazioni sui costi del piano a consumo](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Piano Premium

Quando si usa il piano Premium, le istanze dell'host di funzioni di Azure vengono aggiunte e rimosse in base al numero di eventi in ingresso esattamente come il piano a consumo.  Il piano Premium supporta le funzionalità seguenti:

* Istanze perennemente calde per evitare l'avvio a freddo
* Connettività di rete virtuale
* Durata di esecuzione illimitata (garantita 60 minuti)
* Dimensioni delle istanze Premium (un core, due core e quattro istanze Core)
* Prezzo più prevedibile
* Allocazione di app ad alta densità per i piani con più app per le funzioni

Per informazioni su come creare un'app per le funzioni in un piano Premium, vedere [piano Premium di funzioni di Azure](functions-premium-plan.md).

Invece di eseguire la fatturazione per esecuzione e la memoria utilizzata, la fatturazione per il piano Premium si basa sul numero di secondi core e sulla memoria allocata tra le istanze.  Non sono previsti addebiti per l'esecuzione con il piano Premium. Almeno un'istanza deve essere allocata in qualsiasi momento per ogni piano. Questo comporta un costo mensile minimo per piano attivo, indipendentemente dal fatto che la funzione sia attiva o inattiva. Tenere presente che tutte le app per le funzioni in un piano Premium condividono istanze allocate.

Si consideri il piano Premium di funzioni di Azure nelle situazioni seguenti:

* Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.
* Si dispone di un numero elevato di esecuzioni di piccole dimensioni e si dispone di una fattura di esecuzione elevata, ma di una fattura con un minimo di GB nel piano a consumo.
* Sono necessarie più opzioni di CPU o memoria rispetto a quelle fornite dal piano a consumo.
* Il codice deve essere eseguito più a lungo del [tempo di esecuzione massimo consentito](#timeout) nel piano a consumo.
* Sono necessarie funzionalità disponibili solo in un piano Premium, ad esempio la connettività di rete virtuale. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Piano dedicato (servizio app)

Le app per le funzioni possono anche essere eseguite sulle stesse VM dedicate di altre app del servizio app (SKU Basic, standard, Premium e isolated).

Si consideri un piano di servizio app nelle situazioni seguenti:

* Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
* Si desidera fornire un'immagine personalizzata su cui eseguire le funzioni.

Si paga lo stesso per le app per le funzioni in un piano di servizio app come per le altre risorse del servizio app, ad esempio le app Web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Usando un piano di servizio app, è possibile aumentare manualmente il numero di istanze aggiungendo altre istanze di VM. È anche possibile abilitare la scalabilità automatica, anche se la scalabilità automatica sarà più lenta rispetto alla scalabilità elastica del piano Premium. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/manage-scale-up.md). 

Quando si eseguono funzioni JavaScript in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. Per altre informazioni, vedere [scegliere i piani di servizio app Single Core](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

L'esecuzione in un [ambiente del servizio app](../app-service/environment/intro.md) (ASE) consente di isolare completamente le funzioni e sfruttare un numero maggiore di istanze rispetto a un piano di servizio app.

### <a name="always-on"></a><a name="always-on"></a> Always On

Se si esegue in un piano di servizio app, è necessario abilitare l'impostazione **Always on in** modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzioni risulta inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. L'opzione Always on è disponibile solo nel piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Anche con Always On abilitato, il timeout di esecuzione per le singole funzioni è controllato dall'impostazione `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinare il piano di hosting di un'applicazione esistente

Per determinare il piano di hosting usato dall'app per le funzioni, vedere **piano di servizio app** nella scheda **Panoramica** per l'app per le funzioni nella [portale di Azure](https://portal.azure.com). Per visualizzare il piano tariffario, selezionare il nome del **piano di servizio app**e quindi selezionare **Proprietà** nel riquadro sinistro.

![Visualizzare il piano nel portale](./media/functions-scale/function-app-overview-portal.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per determinare il piano, come descritto di seguito:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando l'output di questo comando è `dynamic`, l'app per le funzioni è nel piano a consumo. Quando l'output di questo comando è `ElasticPremium` , l'app per le funzioni si trova nel piano Premium. Tutti gli altri valori indicano livelli diversi di un piano di servizio app.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

In qualsiasi piano, un'app per le funzioni richiede un account di archiviazione di Azure generale che supporta archiviazione BLOB, code, file e tabelle di Azure. Questo perché funzioni di Azure si basa su archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione delle esecuzioni di funzioni, ma alcuni account di archiviazione non supportano code e tabelle. Questi account, che includono l'archiviazione solo BLOB (tra cui Archiviazione premium) e gli account di archiviazione per utilizzo generico con replica di archiviazione con ridondanza della zona, vengono esclusi dalle selezioni di **Account di archiviazione** esistenti quando si crea un'app per le funzioni.

Lo stesso account di archiviazione usato dall'app per le funzioni può essere usato anche dai trigger e dalle associazioni per archiviare i dati dell'applicazione. Tuttavia, per le operazioni con utilizzo intensivo di archiviazione, è necessario usare un account di archiviazione separato.  

È possibile che più app per le funzioni condividano lo stesso account di archiviazione senza problemi. Un esempio valido è quando si sviluppano più app nell'ambiente locale usando l'emulatore di archiviazione di Azure, che agisce come un account di archiviazione. 

<!-- JH: Does using a Premium Storage account improve perf? -->

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure](../storage/common/storage-introduction.md#core-storage-services).

### <a name="in-region-data-residency"></a>Residenza dei dati in area

Quando è necessario che tutti i dati dei clienti rimangano all'interno di una singola area, l'account di archiviazione associato all'app per le funzioni deve essere uno con la [ridondanza di area](../storage/common/storage-redundancy.md).  È necessario usare anche un account di archiviazione con ridondanza in area con [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) per Durable Functions.

Altri dati relativi ai clienti gestiti dalla piattaforma verranno archiviati solo all'interno dell'area quando vengono ospitati in un Load Balancer interno ambiente del servizio app (o in ILB ASE).  Per informazioni dettagliate, vedere [ridondanza della zona ASE](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="how-the-consumption-and-premium-plans-work"></a>Come funzionano i piani a consumo e Premium

Nei piani di consumo e Premium, l'infrastruttura di funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi su cui vengono attivate le relative funzioni. Ogni istanza dell'host di funzioni nel piano a consumo è limitata a 1,5 GB di memoria e una CPU.  Un'istanza dell'host è l'intera app per le funzioni, che significa che tutte le funzioni all'interno di un'app per le funzioni condividono una risorsa all'interno di un'istanza e vengono ridimensionate allo stesso tempo. Le app per le funzioni che condividono lo stesso piano A consumo vengono ridimensionate in modo indipendente.  Nel piano Premium, le dimensioni del piano determineranno la memoria e la CPU disponibili per tutte le app del piano in tale istanza.  

I file di codice delle funzioni vengono archiviati nelle condivisioni File di Azure nell'account di archiviazione principale della funzione. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scala per funzioni di Azure è l'app per le funzioni. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene infine *ridimensionato in* zero quando nessuna funzione viene eseguita all'interno di un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Avvio a freddo

Dopo che l'app per le funzioni è rimasta inattiva per un certo numero di minuti, la piattaforma può ridimensionare il numero di istanze in cui l'app viene eseguita fino a zero. La richiesta successiva presenta la latenza aggiuntiva di ridimensionamento da zero a uno. Questa latenza è detta avvio a _freddo_. Il numero di dipendenze che devono essere caricate dall'app per le funzioni può avere un effetto sull'ora di inizio a freddo. L'avvio a freddo è un problema per le operazioni sincrone, ad esempio i trigger HTTP che devono restituire una risposta. Se l'avvio a freddo influisca sulle funzioni, provare a eseguire in un piano Premium o in un piano dedicato con always on abilitato.   

### <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. È necessario tenere presenti alcune complessità dei comportamenti di ridimensionamento:

* Una singola app per le funzioni è scalabile solo a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.  È possibile [specificare un valore massimo inferiore](#limit-scale-out) per limitare la scalabilità secondo le esigenze.
* Per i trigger HTTP, le nuove istanze vengono allocate al massimo una volta al secondo.
* Per i trigger non HTTP, le nuove istanze vengono allocate al massimo una volta ogni 30 secondi. La scalabilità è più veloce quando viene eseguita in un [piano Premium](#premium-plan).
* Per i trigger del bus di servizio, usare _Gestisci_ diritti sulle risorse per il ridimensionamento più efficiente. Con i diritti di _ascolto_ , il ridimensionamento non è accurato perché la lunghezza della coda non può essere usata per informare le decisioni di scalabilità. Per altre informazioni sull'impostazione dei diritti nei criteri di accesso del bus di servizio, vedere [criteri di autorizzazione dell'accesso condiviso](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Per i trigger dell'hub eventi, vedere le [linee guida per la scalabilità](functions-bindings-event-hubs-trigger.md#scaling) nell'articolo di riferimento. 

### <a name="limit-scale-out"></a>Limitare la scalabilità orizzontale

È possibile limitare il numero di istanze a cui viene scalata un'app.  Questa operazione è più comune nei casi in cui un componente downstream come un database ha una velocità effettiva limitata.  Per impostazione predefinita, le funzioni del piano a consumo aumenteranno fino a un numero di istanze pari a 200, mentre le funzioni del piano Premium aumenteranno fino a un numero di istanze pari a 100.  È possibile specificare un valore massimo inferiore per un'app specifica modificando il `functionAppScaleLimit` valore.  Il `functionAppScaleLimit` valore di può essere impostato su 0 o null per senza restrizioni oppure su un valore valido compreso tra 1 e il valore massimo dell'app.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Esistono molti aspetti di un'app per le funzioni che hanno un impatto sull'accuratezza del ridimensionamento, ad esempio la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices). È inoltre necessario comprendere il funzionamento delle connessioni quando l'app per le funzioni viene ridimensionata. Per altre informazioni, vedere [How to manage connections in Azure Functions](manage-connections.md) (Come gestire le connessioni in Funzioni di Azure).

Per altre informazioni sul ridimensionamento in Python e Node.js, vedere [Guida per sviluppatori Python di funzioni di Azure-scalabilità e concorrenza](functions-reference-python.md#scaling-and-concurrency) e [funzioni di Azure Node.js Guida per gli sviluppatori-scalabilità e concorrenza](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Modello di fatturazione

La fatturazione per i diversi piani è descritta in dettaglio nella [pagina dei prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/). L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti:

* **Utilizzo delle risorse in gigabyte al secondo (GB-s)**. Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

Le query e le informazioni utili su come comprendere la fattura per il consumo sono disponibili [nelle domande frequenti sulla fatturazione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Confronto di piani di hosting

La tabella di confronto seguente mostra tutti gli aspetti importanti che consentono di decidere la scelta del piano di hosting dell'app funzioni di Azure:

### <a name="plan-summary"></a>Riepilogo piano
| | |
| --- | --- |  
|**[Piano a consumo](#consumption-plan)**| Ridimensiona automaticamente e paga solo per le risorse di calcolo quando le funzioni sono in esecuzione. Nel piano a consumo, le istanze dell'host funzioni vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso.<br/> ✔ Piano di hosting predefinito.<br/>✔ Pagare solo quando le funzioni sono in esecuzione.<br/>✔ la scalabilità orizzontale automatica, anche durante i periodi di carico elevato.|  
|**[Piano Premium](#premium-plan)**|Con la scalabilità automatica basata su richiesta, è possibile usare i ruoli di lavoro pre-riscaldati per eseguire le applicazioni senza alcun ritardo dopo l'inattività, l'esecuzione in istanze più potenti e la connessione a reti virtuali. Si consideri il piano Premium di funzioni di Azure nelle situazioni seguenti, oltre a tutte le funzionalità del piano di servizio app: <br/>✔ Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.<br/>✔ Si dispone di un numero elevato di esecuzioni di piccole dimensioni e si dispone di una fattura di esecuzione elevata, ma di una fattura con un minimo di GB nel piano a consumo.<br/>✔ Sono necessarie più opzioni di CPU o memoria rispetto a quelle fornite dal piano a consumo.<br/>✔ Il codice deve essere eseguito più a lungo del tempo di esecuzione massimo consentito nel piano a consumo.<br/>✔ Sono necessarie funzionalità disponibili solo in un piano Premium, ad esempio la connettività di rete virtuale.|  
|**[Piano](#app-service-plan)**<sup>1</sup> dedicato|Eseguire le funzioni all'interno di un piano di servizio app alle normali tariffe del piano di servizio app. Ideale per le operazioni a esecuzione prolungata, nonché quando sono necessari costi e scalabilità più predittivi. Si consideri un piano di servizio app nelle situazioni seguenti:<br/>✔ Sono presenti VM sottoutilizzate esistenti che eseguono già altre istanze del servizio app.<br/>✔ Si desidera fornire un'immagine personalizzata su cui eseguire le funzioni.|  
|**[ASE](#app-service-plan)** Ambiente del servizio app<sup>1</sup>|Ambiente del servizio app (ASE) è una funzionalità del servizio app che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app su vasta scala. Gli ambienti sono appropriati per i carichi di lavoro delle applicazioni che richiedono: <br/>✔ Scalabilità molto elevata.<br/>✔ L'isolamento completo del calcolo e l'accesso alla rete sicuro.<br/>✔ Utilizzo elevato della memoria.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes fornisce un ambiente completamente isolato e dedicato in esecuzione nella piattaforma Kubernetes.  Kubernetes è appropriato per i carichi di lavoro delle applicazioni che richiedono: <br/>✔ Requisiti hardware personalizzati.<br/>Isolamento ✔ e accesso alla rete protetto.<br/>✔ La possibilità di eseguire in un ambiente ibrido o multicloud.<br/>✔ Eseguiti insieme a servizi e applicazioni Kubernetes esistenti.|  

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="operating-systemruntime"></a>Sistema operativo/Runtime

| | Linux<sup>1</sup><br/>Solo codice | Windows<sup>2</sup><br/>Solo codice | Linux<sup>1, 3</sup><br/>Contenitore Docker |
| --- | --- | --- | --- |
| **[Piano a consumo](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Nessun supporto  |
| **[Piano Premium](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Piano dedicato](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)** Ambiente del servizio app<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/d | n/d |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux è l'unico sistema operativo supportato per lo stack di runtime di Python.  
<sup>2</sup> Windows è l'unico sistema operativo supportato per lo stack del runtime di PowerShell.   
<sup>3</sup> Linux è l'unico sistema operativo supportato per i contenitori docker.
<sup>4</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="scale"></a>Scalabilità

| | Aumentare il numero di istanze | Numero massimo di istanze |
| --- | --- | --- |
| **[Piano a consumo](#consumption-plan)** | Gestito da eventi. Aumento automatico del numero di istanze anche in periodo di carico elevato. L'infrastruttura di funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi su cui vengono attivate le relative funzioni. | 200 |
| **[Piano Premium](#premium-plan)** | Gestito da eventi. Aumento automatico del numero di istanze anche in periodo di carico elevato. L'infrastruttura di funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi su cui vengono attivate le relative funzioni. |100|
| **[Piano](#app-service-plan)**<sup>1</sup> dedicato | Scalabilità manuale/automatica |10-20|
| **[ASE](#app-service-plan)** Ambiente del servizio app<sup>1</sup> | Scalabilità manuale/automatica |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Scalabilità automatica guidata dagli eventi per i cluster Kubernetes con [Keda](https://keda.sh). | Varia &nbsp; a seconda del &nbsp; cluster.&nbsp;&nbsp;|

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="cold-start-behavior"></a>Comportamento di avvio a freddo

|    |    | 
| -- | -- |
| **[Piano a consumo &nbsp;](#consumption-plan)** | Le app possono essere ridimensionate a zero se inattive per un determinato periodo di tempo, ovvero alcune richieste potrebbero avere latenza aggiuntiva all'avvio.  Il piano a consumo ha alcune ottimizzazioni che consentono di ridurre l'ora di avvio a freddo, incluso il pull da funzioni segnaposto pre-riscaldate che hanno già l'host funzione e i processi del linguaggio in esecuzione. |
| **[Piano Premium](#premium-plan)** | Per evitare l'avvio a freddo, le istanze sono perennemente calde. |
| **[Piano](#app-service-plan)**<sup>1</sup> dedicato | Quando è in esecuzione in un piano dedicato, l'host di funzioni può essere eseguito in modo continuo, il che significa che l'avvio a freddo non è realmente un problema. |
| **[ASE](#app-service-plan)** Ambiente del servizio app<sup>1</sup> | Quando è in esecuzione in un piano dedicato, l'host di funzioni può essere eseguito in modo continuo, il che significa che l'avvio a freddo non è realmente un problema. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Dipende dalla configurazione KEDA. Le app possono essere configurate per l'esecuzione sempre senza avvio a freddo o configurate per la scalabilità a zero, il che comporta l'avvio a freddo di nuovi eventi. 

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="service-limits"></a>Limiti del servizio

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Funzionalità di rete

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Fatturazione

| | | 
| --- | --- |
| **[Piano a consumo](#consumption-plan)** | Pagare solo per il tempo di esecuzione delle funzioni. La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. |
| **[Piano Premium](#premium-plan)** | Il piano Premium è basato sul numero di secondi e di memoria Core usati nelle istanze necessarie e pre-surriscaldate. È necessario che almeno un'istanza per piano rimanga sempre calda. Questo piano offre prezzi più prevedibili. |
| **[Piano](#app-service-plan)**<sup>1</sup> dedicato | Si paga lo stesso per le app per le funzioni in un piano di servizio app come per le altre risorse del servizio app, ad esempio le app Web.|
| **[ASE](#app-service-plan)** Ambiente del servizio app<sup>1</sup> | esiste una tariffa mensile fissa per un ambiente del servizio app che paga l'infrastruttura e non cambia con le dimensioni dell'ambiente del servizio app. Inoltre, c'è un costo per ogni piano di servizio app vCPU. Tutte le app ospitate in un ambiente del servizio app fanno parte di uno SKU di prezzi isolato. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Si pagano solo i costi del cluster Kubernetes. nessuna fatturazione aggiuntiva per le funzioni. L'app per le funzioni viene eseguita come carico di lavoro dell'applicazione nella parte superiore del cluster, proprio come per una normale app. |

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="next-steps"></a>Passaggi successivi

+ [Guida introduttiva: Creare un progetto Funzioni di Azure con Visual Studio Code](functions-create-first-function-vs-code.md)
+ [Tecnologie di distribuzione in funzioni di Azure](functions-deployment-technologies.md) 
+ [Guida per sviluppatori di Funzioni di Azure](functions-reference.md)
