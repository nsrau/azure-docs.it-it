---
title: Ridimensionamento e hosting di Funzioni di Azure
description: Learn how to choose between Azure Functions Consumption plan and Premium plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9644e89591d7d8b7642b5f381434357191d1711
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226591"
---
# <a name="azure-functions-scale-and-hosting"></a>Ridimensionamento e hosting di Funzioni di Azure

When you create a function app in Azure, you must choose a hosting plan for your app. There are three hosting plans available for Azure Functions: [Consumption plan](#consumption-plan), [Premium plan](#premium-plan), and [App Service plan](#app-service-plan).

The hosting plan you choose dictates the following behaviors:

* How your function app is scaled.
* The resources available to each function app instance.
* Support for advanced features, such as VNET connectivity.

Both Consumption and Premium plans automatically add compute power when your code is running. Your app is scaled out when needed to handle load, and scaled down when code stops running. For the Consumption plan, you also don't have to pay for idle VMs or reserve capacity in advance.  

Premium plan provides additional features, such as premium compute instances, the ability to keep instances warm indefinitely, and VNet connectivity.

App Service plan allows you to take advantage of dedicated infrastructure, which you manage. Your function app doesn't scale based on events, which means is never scales down to zero. (Requires that [Always on](#always-on) is enabled.)

> [!NOTE]
> You can switch between Consumption and Premium plans by changing the plan property of the function app resource.

## <a name="hosting-plan-support"></a>Hosting plan support

Feature support falls into the following two categories:

* _Generally available (GA)_ : fully supported and approved for production use.
* _Preview_: not yet fully supported and approved for production use.

The following table indicates the current level of support for the three hosting plans, when running on either Windows or Linux:

| | Piano a consumo | Piano Premium | Dedicated plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Disponibilità a livello generale | Disponibilità a livello generale | Disponibilità a livello generale |
| Linux | Disponibilità a livello generale | Disponibilità a livello generale | Disponibilità a livello generale |

## <a name="consumption-plan"></a>Piano a consumo

When you're using the Consumption plan, instances of the Azure Functions host are dynamically added and removed based on the number of incoming events. Questo piano senza server offre la scalabilità automatica e sono previsti costi per le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano A consumo, il timeout dell'esecuzione di una funzione si verifica dopo un periodo di tempo configurabile.

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. La fatturazione viene aggregata tra tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Il piano a consumo è l'opzione di hosting predefinita e offre i vantaggi seguenti:

* Addebiti solo quando le funzioni sono in esecuzione
* Aumento automatico del numero di istanze anche in periodo di carico elevato

Function apps in the same region can be assigned to the same Consumption plan. There's no downside or impact to having multiple apps running in the same Consumption plan. Assigning multiple apps to the same consumption plan has no impact on resilience, scalability, or reliability of each app.

To learn more about how to estimate costs when running in a Consumption plan, see [Understanding Consumption plan costs](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium plan

When you're using the Premium plan, instances of the Azure Functions host are added and removed based on the number of incoming events just like the Consumption plan.  Premium plan supports the following features:

* Perpetually warm instances to avoid any cold start
* Connettività di rete virtuale
* Unlimited execution duration
* Premium instance sizes (one core, two core, and four core instances)
* More predictable pricing
* High-density app allocation for plans with multiple function apps

Information on how you can configure these options can be found in the [Azure Functions premium plan document](functions-premium-plan.md).

Instead of billing per execution and memory consumed, billing for the Premium plan is based on the number of core seconds and memory used across needed and pre-warmed instances. At least one instance must be warm at all times per plan. This means that there is a minimum monthly cost per active plan, regardless of the number of executions. Keep in mind that all function apps in a Premium plan share pre-warmed and active instances.

Consider the Azure Functions premium plan in the following situations:

* Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.
* You have a high number of small executions and have a high execution bill but low GB second bill in the consumption plan.
* You need more CPU or memory options than what is provided by the Consumption plan.
* Your code needs to run longer than the [maximum execution time allowed](#timeout) on the Consumption plan.
* You require features that are only available on a Premium plan, such as VNET/VPN connectivity.

When running JavaScript functions on a Premium plan, you should choose an instance that has fewer vCPUs. For more information, see the [Choose single-core Premium plans](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedicated (App Service) plan

Your function apps can also run on the same dedicated VMs as other App Service apps (Basic, Standard, Premium, and Isolated SKUs).

Consider an App Service plan in the following situations:

* Sono presenti macchine virtuali sottoutilizzate, che eseguono già altre istanze del servizio app.
* You want to provide a custom image on which to run your functions.

You pay the same for function apps in an App Service Plan as you would for other App Service resources, like web apps. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

With an App Service plan, you can manually scale out by adding more VM instances. You can also enable autoscale. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Per aumentare le prestazioni è anche possibile scegliere un piano di servizio App diverso. Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](../app-service/manage-scale-up.md). 

Quando si eseguono funzioni JavaScript in un piano di servizio app, è necessario scegliere un piano con un minor numero di vCPU. For more information, see [Choose single-core App Service plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Se per l'esecuzione si usa un piano di servizio app, è necessario abilitare l'impostazione **Always on** in modo che l'app per le funzioni venga eseguita correttamente. In un piano di servizio app il runtime delle funzioni risulta inattivo dopo pochi minuti di inattività. Solo i trigger HTTP "attiveranno" quindi le funzioni. L'opzione Always on è disponibile solo nel piano di servizio app. In un piano a consumo, la piattaforma attiva automaticamente le app per le funzioni.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Anche con Always On abilitato, il timeout di esecuzione per le singole funzioni è controllato dall'impostazione `functionTimeout` nel file di progetto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determine the hosting plan of an existing application

Per determinare il piano di hosting usato dall'app per le funzioni, vedere **Piano di servizio app/piano tariffario** nella scheda **Panoramica** dell'app per le funzioni nel [portale di Azure](https://portal.azure.com). Per i piani di servizio app è indicato anche il piano tariffario.

![Visualizzare il piano nel portale](./media/functions-scale/function-app-overview-portal.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per determinare il piano, come descritto di seguito:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando l'output di questo comando è `dynamic`, l'app per le funzioni è nel piano a consumo. When the output from this command is `ElasticPremium`, your function app is in the Premium plan. All other values indicate different tiers of an App Service plan.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage. Il motivo è che Funzioni si basa su Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione di funzioni, ma alcuni account di archiviazione non supportano code e tabelle. Questi account, che includono l'archiviazione solo BLOB (tra cui Archiviazione premium) e gli account di archiviazione per utilizzo generico con replica di archiviazione con ridondanza della zona, vengono esclusi dalle selezioni di **Account di archiviazione** esistenti quando si crea un'app per le funzioni.

The same storage account used by your function app can also be used by your triggers and bindings to store your application data. However, for storage-intensive operations, you should use a separate storage account.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>How the consumption and premium plans work

In the consumption and premium plans, the Azure Functions infrastructure scales CPU and memory resources by adding additional instances of the Functions host, based on the number of events that its functions are triggered on. Each instance of the Functions host in the consumption plan is limited to 1.5 GB of memory and one CPU.  An instance of the host is the entire function app, meaning all functions within a function app share resource within an instance and scale at the same time. Function apps that share the same consumption plan are scaled independently.  In the premium plan, your plan size will determine the available memory and CPU for all apps in that plan on that instance.  

Function code files are stored on Azure Files shares on the function's main storage account. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

### <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

The unit of scale for Azure Functions is the function app. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene ridotto a zero quando non è in esecuzione alcuna funzione in un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. There are a few intricacies of scaling behaviors to be aware of:

* Un'app per le funzioni viene ridimensionata solo fino a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.
* For HTTP triggers, new instances will only be allocated at most once every 1 second.
* For non-HTTP triggers, new instances will only be allocated at most once every 30 seconds.

Trigger distinti possono avere limiti di ridimensionamento diversi come illustrato di seguito:

* [Hub eventi](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Esistono molti aspetti di un'app per le funzioni che hanno un impatto sull'accuratezza del ridimensionamento, ad esempio la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices). È inoltre necessario comprendere il funzionamento delle connessioni quando l'app per le funzioni viene ridimensionata. Per altre informazioni, vedere [How to manage connections in Azure Functions](manage-connections.md) (Come gestire le connessioni in Funzioni di Azure).

### <a name="billing-model"></a>Modello di fatturazione

Billing for the different plans is described in detail on the [Azure Functions pricing page](https://azure.microsoft.com/pricing/details/functions/). L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti:

* **Utilizzo delle risorse in gigabyte al secondo (GB-s)** . Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

Useful queries and information on how to understand your consumption bill can be found [on the billing FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limiti del servizio

The following table indicates the limits that apply to function apps when running in the various hosting plans:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
