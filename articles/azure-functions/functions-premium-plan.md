---
title: Piano Premium di funzioni di Azure
description: Dettagli e opzioni di configurazione (VNet, nessun avvio a freddo, durata di esecuzione illimitata) per il piano Premium di funzioni di Azure.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom: references_regions
ms.openlocfilehash: 4f6e2008cad66ce7cd68016d3873ecbc18b1961c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145751"
---
# <a name="azure-functions-premium-plan"></a>Piano Premium di funzioni di Azure

Il piano Premium di funzioni di Azure, noto anche come piano Premium elastico, è un'opzione di hosting per le app per le funzioni. Il piano Premium offre funzionalità come la connettività VNet, l'avvio a freddo e l'hardware Premium.  È possibile distribuire più app per le funzioni nello stesso piano Premium e il piano consente di configurare le dimensioni dell'istanza di calcolo, le dimensioni del piano di base e la dimensione massima del piano.  Per un confronto tra il piano Premium e altri tipi di piano e hosting, vedere [Opzioni di scalabilità e hosting delle funzioni](functions-scale.md).

## <a name="create-a-premium-plan"></a>Creare un piano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

È anche possibile creare un piano Premium usando il comando [AZ functionapp Plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) nell'interfaccia della riga di comando di Azure. L'esempio seguente crea un piano di livello _Premium a 1 elastico_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

In questo esempio sostituire `<RESOURCE_GROUP>` con il gruppo di risorse e `<PLAN_NAME>` con un nome per il piano univoco nel gruppo di risorse. Specificare un [valore `<REGION>` supportato ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Per creare un piano Premium che supporta Linux, includere l' `--is-linux` opzione.

Dopo aver creato il piano, è possibile usare il comando [AZ functionapp create](/cli/azure/functionapp#az-functionapp-create) per creare l'app per le funzioni. Nel portale vengono creati contemporaneamente il piano e l'app. Per un esempio di script dell'interfaccia della riga di comando di Azure completo, vedere [creare un'app per le funzioni in un piano Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funzionalità

Per le app per le funzioni distribuite in un piano Premium sono disponibili le funzionalità seguenti.

### <a name="always-ready-instances"></a>Istanze sempre pronte

Se attualmente non si verificano eventi ed esecuzioni nel piano a consumo, l'app può essere ridimensionata a zero istanze. Quando vengono rilasciati nuovi eventi, è necessario specializzare una nuova istanza con l'app in esecuzione su di essa.  La specializzazione di nuove istanze può richiedere del tempo a seconda dell'app.  Questa latenza aggiuntiva alla prima chiamata viene spesso chiamata avvio a freddo dell'app.

Nel piano Premium è possibile fare in maniera che l'app sia sempre pronta per un numero specificato di istanze.  Il numero massimo di istanze sempre pronte è 20.  Quando gli eventi iniziano ad attivare l'app, vengono indirizzati alle istanze sempre pronte.  Quando la funzione diventa attiva, le istanze aggiuntive verranno scaldate come buffer.  Questo buffer impedisce l'avvio a freddo per le nuove istanze richieste durante la scalabilità.  Queste istanze memorizzate nel buffer sono denominate [istanze pre-surriscaldate](#pre-warmed-instances).  Con la combinazione delle istanze sempre pronte e di un buffer già riscaldato, l'app può eliminare efficacemente l'avvio a freddo.

> [!NOTE]
> Ogni piano Premium avrà sempre almeno un'istanza attiva e fatturata.

È possibile configurare il numero di istanze sempre pronte nel portale di Azure selezionando il **app per le funzioni**, passando alla scheda **funzionalità della piattaforma** e selezionando le opzioni di **scale out** . Nella finestra di modifica dell'app per le funzioni, le istanze sempre pronte sono specifiche dell'app.

![Impostazioni di scalabilità elastica](./media/functions-premium-plan/scale-out.png)

È anche possibile configurare le istanze sempre pronte per un'app con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>Istanze pre-riscaldate

Le istanze pre-riscaldate sono il numero di istanze scaldate come buffer durante gli eventi di scalabilità e attivazione.  Le istanze pre-surriscaldate continuano a essere memorizzate nel buffer fino a quando non viene raggiunto il limite massimo di scalabilità orizzontale.  Il numero predefinito di istanze pre-riscaldate è 1 e per la maggior parte degli scenari deve rimanere come 1.  Se un'app ha un tempo di riscaldamento prolungato (ad esempio un'immagine del contenitore personalizzata), è possibile aumentare questo buffer.  Un'istanza pre-riscaldata diventerà attiva solo dopo che tutte le istanze attive saranno state sufficientemente utilizzate.

Si consideri questo esempio del modo in cui le istanze sempre pronte e quelle pre-surriscaldate interagiscono.  Per un'app per le funzioni Premium sono state configurate cinque istanze sempre pronte e il valore predefinito di un'istanza con riscaldamento.  Quando l'app è inattiva e non viene attivato alcun evento, l'app viene sottoposta a provisioning e in esecuzione in cinque istanze.  

Non appena viene introdotto il primo trigger, le cinque istanze sempre pronte diventano attive e viene allocata un'istanza aggiuntiva pre-riscaldata.  L'app viene ora eseguita con sei istanze di cui è stato effettuato il provisioning: le cinque istanze always ready attive e il sesto buffer preriscaldato e inattivo.  Se la frequenza delle esecuzioni continua ad aumentare, verranno utilizzate le cinque istanze attive.  Quando la piattaforma decide di scalare oltre cinque istanze, verrà ridimensionata nell'istanza pre-riscaldata.  In tal caso, saranno presenti sei istanze attive e verrà eseguito immediatamente il provisioning di una settima istanza e verrà riempito il buffer pre-riscaldato.  Questa sequenza di ridimensionamento e pre-riscaldamento continuerà fino a raggiungere il numero massimo di istanze per l'app.  Nessuna istanza verrà pre-riscaldata o attivata oltre il valore massimo.

È possibile modificare il numero di istanze pre-surriscaldate per un'app usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>Numero massimo di istanze per un'app

Oltre al [numero massimo di istanze del piano](#plan-and-sku-settings), è possibile configurare un valore massimo per app.  Il numero massimo di app può essere configurato usando il [limite di scalabilità dell'app](./functions-scale.md#limit-scale-out).

### <a name="private-network-connectivity"></a>Connettività di rete privata

Funzioni di Azure distribuite in un piano Premium sfrutta la [nuova integrazione VNet per le app Web](../app-service/web-sites-integrate-with-vnet.md).  Quando viene configurata, l'app può comunicare con le risorse all'interno della VNet o essere protetta tramite endpoint di servizio.  Le restrizioni IP sono disponibili anche nell'app per limitare il traffico in ingresso.

Quando si assegna una subnet all'app per le funzioni in un piano Premium, è necessaria una subnet con un numero sufficiente di indirizzi IP per ogni potenziale istanza. È necessario un blocco IP con almeno 100 indirizzi disponibili.

Per altre informazioni, vedere [integrare l'app per le funzioni con un VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Scalabilità elastica rapida

Altre istanze di calcolo vengono aggiunte automaticamente per l'app usando la stessa logica di scalabilità rapida del piano a consumo. Le app nello stesso piano di servizio app vengono ridimensionate indipendentemente l'una dall'altra in base alle esigenze di una singola app. Tuttavia, le app per le funzioni nello stesso piano di servizio app condividono le risorse delle macchine virtuali per ridurre i costi, quando possibile. Il numero di app associate a una macchina virtuale dipende dal footprint di ogni app e dalle dimensioni della macchina virtuale.

Per altre informazioni sul funzionamento della scalabilità, vedere [scalabilità e hosting di funzioni](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Durata dell'esecuzione più lunga

Per una singola esecuzione, le funzioni di Azure in un piano a consumo sono limitate a 10 minuti.  Nel piano Premium, per la durata dell'esecuzione viene impostato un valore predefinito di 30 minuti per impedire l'esecuzione di Runaway. Tuttavia, è possibile [modificare il host.jsnella configurazione](./functions-host-json.md#functiontimeout) per rendere la durata non vincolata per le app del piano Premium (garantita 60 minuti).

## <a name="plan-and-sku-settings"></a>Impostazioni del piano e dello SKU

Quando si crea il piano, sono disponibili due impostazioni relative alle dimensioni del piano, ovvero il numero minimo di istanze (o le dimensioni del piano) e il limite massimo di picchi.

Se l'app richiede istanze che superano le istanze sempre pronte, può continuare a eseguire la scalabilità orizzontale finché il numero di istanze raggiunge il limite massimo di picchi.  Vengono addebitati i costi per le istanze oltre le dimensioni del piano solo quando sono in esecuzione e affittate all'utente.  Per il ridimensionamento dell'app in base al limite massimo definito, si farà il possibile.

È possibile configurare le dimensioni del piano e i valori massimi nel portale di Azure selezionando le opzioni **scale out** nel piano o in un'app per le funzioni distribuita in tale piano (in **funzionalità della piattaforma**).

È anche possibile aumentare il limite massimo di picchi dall'interfaccia della riga di comando di Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

Il valore minimo per ogni piano sarà almeno un'istanza.  Il numero minimo effettivo di istanze verrà configurato automaticamente in base alle istanze sempre pronte richieste dalle app del piano.  Ad esempio, se l'app A richiede cinque istanze sempre pronte e l'app B richiede due istanze sempre pronte nello stesso piano, le dimensioni minime del piano verranno calcolate come cinque.  L'app A verrà eseguita in tutti i 5 e l'app B verrà eseguita solo su 2.

> [!IMPORTANT]
> Viene addebitato il costo di ogni istanza allocata nel numero minimo di istanze indipendentemente dall'esecuzione delle funzioni.

Nella maggior parte dei casi questo valore minimo calcolato automaticamente dovrebbe essere sufficiente.  Tuttavia, il ridimensionamento oltre il minimo si verifica al massimo sforzo.  È possibile, anche se improbabile, che in un determinato momento la scalabilità orizzontale venga posticipata se non sono disponibili istanze aggiuntive.  Impostando un valore minimo superiore al minimo calcolato automaticamente, le istanze vengono riservate in anticipo rispetto alla scalabilità orizzontale.

L'aumento del valore minimo calcolato per un piano può essere eseguito usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKU di istanze disponibili

Quando si crea o si ridimensiona il piano, è possibile scegliere tra tre dimensioni delle istanze.  Ti verrà addebitato il numero totale di core e memoria utilizzati al secondo.  L'app può essere ridimensionata automaticamente a più istanze in base alle esigenze.  

|SKU|Core|Memoria|Archiviazione|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Considerazioni sull'utilizzo della memoria
L'esecuzione in un computer con una maggiore quantità di memoria non significa sempre che l'app per le funzioni utilizzerà tutta la memoria disponibile.

Ad esempio, un'app per le funzioni JavaScript è vincolata dal limite di memoria predefinito in Node.js. Per aumentare il limite di memoria fisso, aggiungere l'impostazione dell'app `languageWorkers:node:arguments` con un valore di `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Scale Out area massima

Di seguito sono riportati i valori di scalabilità orizzontale massimi attualmente supportati per un singolo piano in ogni area e configurazione del sistema operativo. Per richiedere un aumento, aprire un ticket di supporto.

Vedere la disponibilità completa a livello di area delle funzioni qui: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Area| Windows | Linux |
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
|Germania centro-occidentale| 100 | Non disponibile |
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
|West US 2| 100 | 20 |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle opzioni di scalabilità e hosting di funzioni di Azure](functions-scale.md)
