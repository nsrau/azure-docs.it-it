---
title: Funzionalità di Azure Piano Premium
description: Dettagli e opzioni di configurazione (VNet, nessun avvio a freddo, durata di esecuzione illimitata) per il piano Premium di Funzioni di Azure.Details and configuration options (VNet, no cold start, unlimited execution duration) for the Azure Functions Premium plan.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276907"
---
# <a name="azure-functions-premium-plan"></a>Funzionalità di Azure Piano Premium

Il piano Premium di Funzioni di Azure (talvolta denominato piano Elastic Premium) è un'opzione di hosting per le app per le funzioni. Il piano Premium offre funzionalità come la connettività della rete virtuale, l'assenza di avviamento a freddo e l'hardware premium.  È possibile distribuire più app per le funzioni nello stesso piano Premium e il piano consente di configurare le dimensioni dell'istanza di calcolo, le dimensioni del piano di base e le dimensioni massime del piano.  Per un confronto tra il piano Premium e altri tipi di piano e hosting, vedere scala delle [funzioni e opzioni](functions-scale.md)di hosting .

## <a name="create-a-premium-plan"></a>Creare un piano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

È anche possibile creare un piano Premium usando il piano di funzioni az creare nell'interfaccia della riga di comando di Azure.You can also create a Premium plan using [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) in the Azure CLI. L'esempio seguente crea un piano di livello _Elastic Premium 1:The_ following example creates an Elastic Premium 1 tier plan:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

In questo esempio `<RESOURCE_GROUP>` sostituire con `<PLAN_NAME>` il gruppo di risorse e con un nome univoco per il piano nel gruppo di risorse. Specificare un [file `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Per creare un piano Premium che `--is-linux` supporti Linux, includere l'opzione.

Dopo aver creato il piano, puoi usare [az functionapp create](/cli/azure/functionapp#az-functionapp-create) per creare la tua app per le funzioni. Nel portale, sia il piano che l'app vengono creati contemporaneamente. Per un esempio di uno script completo dell'interfaccia della riga di comando di Azure, vedere [Creare un'app per](scripts/functions-cli-create-premium-plan.md)le funzioni in un piano Premium.

## <a name="features"></a>Funzionalità

Le funzionalità seguenti sono disponibili per le app per le funzioni distribuite in un piano Premium.The following features are available to function apps deployed to a Premium plan.

### <a name="pre-warmed-instances"></a>Istanze preriscaldate

Se oggi non si verificano eventi ed esecuzioni nel piano Consumo, l'app può essere ridimensionata in zero. Quando arrivano nuovi eventi, una nuova istanza deve essere specializzata con l'app in esecuzione su di essa.  La specializzazione di nuove istanze può richiedere del tempo a seconda dell'app.  Questa latenza aggiuntiva alla prima chiamata viene spesso chiamata avvio a freddo dell'app.

Nel piano Premium puoi fare in modo che l'app venga preriscaldata in un numero specificato di istanze, fino alle dimensioni minime del piano.  Le istanze preriscaldate consentono inoltre di prescalare un'app prima del caricamento elevato. Man mano che l'app viene ridimensionata, viene prima ridimensionata nelle istanze preriscaldate. Le istanze aggiuntive continuano a essere memorizzate nel buffer immediatamente in preparazione per la successiva operazione di scalabilità. Avendo un buffer di istanze preriscaldate, è possibile evitare efficacemente le latenze di avvio a freddo.  Le istanze preriscaldate sono una funzionalità del piano Premium ed è necessario mantenere almeno un'istanza in esecuzione e sempre disponibile.

È possibile configurare il numero di istanze preriscaldate nel portale di Azure selezionando **l'app per**le funzioni , passando alla scheda **Funzionalità della piattaforma** e selezionando le opzioni di **scalabilità orizzontale.** Nella finestra di modifica dell'app per le funzioni, le istanze preriscaldate sono specifiche dell'app, ma le istanze minima e massima si applicano all'intero piano.

![Impostazioni scala elastica](./media/functions-premium-plan/scale-out.png)

È anche possibile configurare istanze preriscaldate per un'app con l'interfaccia della riga di comando di Azure.You can also configure pre-warmed instances for an app with the Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Connettività di rete privata

Funzioni di Azure distribuite in un piano Premium sfrutta la nuova integrazione della [rete virtuale per le app Web.](../app-service/web-sites-integrate-with-vnet.md)  Se configurata, l'app può comunicare con le risorse all'interno della rete virtuale o protetta tramite endpoint di servizio.  Le restrizioni IP sono disponibili anche sull'app per limitare il traffico in ingresso.

Quando si assegna una subnet all'app per le funzioni in un piano Premium, è necessaria una subnet con indirizzi IP sufficienti per ogni potenziale istanza. Abbiamo bisogno di un blocco IP con almeno 100 indirizzi disponibili.

Per altre informazioni, vedere [Integrare l'app per](functions-create-vnet.md)le funzioni con una rete virtuale .

### <a name="rapid-elastic-scale"></a>Rapida scala elastica

Le istanze di calcolo aggiuntive vengono aggiunte automaticamente per l'app usando la stessa logica di ridimensionamento rapido del piano di utilizzo.  Per ulteriori informazioni sul funzionamento del ridimensionamento, vedere [Scala delle funzioni e hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Durata di esecuzione più lunga

Funzioni di Azure in un piano di consumo sono limitate a 10 minuti per una singola esecuzione.  Nel piano Premium, la durata di esecuzione per impostazione predefinita è 30 minuti per impedire esecuzioni in esecuzione. Tuttavia, è possibile [modificare la configurazione host.json](./functions-host-json.md#functiontimeout) per rendere questo unbounded per le applicazioni piano Premium (garantito 60 minuti).

## <a name="plan-and-sku-settings"></a>Pianificare e sKU impostazioni

Quando si crea il piano, si configurano due impostazioni: il numero minimo di istanze (o dimensioni del piano) e il limite massimo di burst.  Le istanze minime sono riservate e sempre in esecuzione.

> [!IMPORTANT]
> Viene addebitato alcun costo per ogni istanza allocata nel conteggio minimo delle istanze, indipendentemente dal fatto che le funzioni siano in esecuzione o meno.

Se l'app richiede istanze oltre le dimensioni del piano, può continuare a scalare fino a quando il numero di istanze non raggiunge il limite massimo di burst.  Le istanze non solo dopo le dimensioni del piano vengono fatturate e noleggiate all'utente.  Faremmo il massimo sforzo per scalare l'app al limite massimo definito, mentre le istanze del piano minimo sono garantite per la tua app.

È possibile configurare le dimensioni e i valori massimi del piano nel portale di Azure selezionando le opzioni **scalabili** nel piano o un'app per le funzioni distribuita in tale piano (in **Funzionalità della piattaforma).**

È anche possibile aumentare il limite massimo di burst dall'interfaccia della riga di comando di Azure:You can also increase the maximum burst limit from the Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKU di istanza disponibili

Quando si crea o si ridimensiona il piano, è possibile scegliere tra tre dimensioni di istanza.  Verrà fatturato il numero totale di core e memoria consumati al secondo.  L'app può essere ridimensionata automaticamente in più istanze in base alle esigenze.  

|SKU|Core|Memoria|Archiviazione|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Considerazioni sull'utilizzo della memoria
In esecuzione su un computer con più memoria non significa sempre che l'app per le funzioni utilizzerà tutta la memoria disponibile.

Ad esempio, un'app per le funzioni JavaScript è vincolata dal limite di memoria predefinito in Node.js.For example, a JavaScript function app is constrained by the default memory limit in Node.js. Per aumentare questo limite di `languageWorkers:node:arguments` memoria fisso, aggiungere l'impostazione dell'app con un valore di `--max-old-space-size=<max memory in MB>`.

## <a name="region-max-scale-out"></a>Scalabilità orizzontale massima

Di seguito sono riportati i valori di scalabilità orizzontale massima attualmente supportati per un singolo piano in ogni area e configurazione del sistema operativo. Per richiedere un aumento si prega di aprire un ticket di supporto.

Vedere la disponibilità regionale completa di Funzioni qui: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| WINDOWS | Linux |
|--| -- | -- |
|Australia centrale| 20 | Non disponibile |
|Australia centrale 2| 20 | Non disponibile |
|Australia orientale| 100 | 20 |
|Australia sud-orientale | 100 | 20 |
|Brasile meridionale| 60 | 20 |
|Canada centrale| 100 | 20 |
|Stati Uniti centrali| 100 | 20 |
|Asia orientale| 100 | 20 |
|Stati Uniti orientali | 100 | 20 |
|Stati Uniti orientali 2| 100 | 20 |
|Francia centrale| 100 | 20 |
|Germania Centro Ovest| 100 | Non disponibile |
|Giappone orientale| 100 | 20 |
|Giappone occidentale| 100 | 20 |
|Corea centrale| 100 | 20 |
|Stati Uniti centro-settentrionali| 100 | 20 |
|Europa settentrionale| 100 | 20 |
|Norvegia orientale| 20 | 20 |
|Stati Uniti centro-meridionali| 100 | 20 |
|India meridionale | 100 | Non disponibile |
|Asia sud-orientale| 100 | 20 |
|Regno Unito meridionale| 100 | 20 |
|Regno Unito occidentale| 100 | 20 |
|Europa occidentale| 100 | 20 |
|India occidentale| 100 | 20 |
|Stati Uniti centro-occidentali| 20 | 20 |
|Stati Uniti occidentali| 100 | 20 |
|Stati Uniti occidentali 2| 100 | 20 |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle opzioni di scalabilità e hosting di Funzioni di AzureUnderstand Azure Functions scale and hosting options](functions-scale.md)
