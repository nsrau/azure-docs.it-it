---
title: Piano Premium di funzioni di Azure (anteprima) | Microsoft Docs
description: Piano i dettagli e opzioni di configurazione (rete virtuale, senza avvio a freddo, la durata di esecuzione illimitato) per le funzioni di Azure Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: dab7561db8f223bff87f41ef756605359c3478e4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492706"
---
# <a name="azure-functions-premium-plan-preview"></a>Piano Premium di funzioni di Azure (anteprima)

Il piano Premium di funzioni di Azure è un'opzione di hosting per le app di funzione. Il piano Premium offre funzionalità come la connettività VNet, nessun avvio a freddo e hardware di premium.  Più App per le funzioni possono essere distribuiti per lo stesso piano Premium e il piano consente di configurare dimensioni di istanza di calcolo, dimensioni del piano di base e le dimensioni massime di piano.  Per un confronto tra il piano Premium e altro piano e i tipi di hosting, vedere [funzione di scalabilità e le opzioni di hosting](functions-scale.md).

> [!NOTE]
> L'anteprima del piano Premium supporta attualmente le funzioni in esecuzione in .NET, nodo o Java tramite infrastruttura di Windows.

## <a name="create-a-premium-plan"></a>Creare un piano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

È anche possibile creare un piano Premium della riga di comando di Azure

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Funzionalità

Le funzionalità seguenti sono disponibili per App per le funzioni distribuite in un piano Premium.

### <a name="pre-warmed-instances"></a>Pre-riscaldate istanze

Se non gli eventi e le esecuzioni si verificano attualmente nel piano a consumo, l'app potrebbe ridurre a zero istanze. Quando sono disponibili nuovi eventi in, una nuova istanza deve essere specializzato con l'app in esecuzione su di esso.  Le nuove istanze specializzate potrebbero richiedere alcuni minuti a seconda dell'app.  Questa latenza aggiuntiva dovuta alla prima chiamata viene spesso chiamata avvio a freddo dell'app.

Il piano Premium, è possibile avere app pre-riscaldato su un numero di istanze, fino alle dimensioni del piano minimo specificato.  Le istanze di pre-riscaldate consentono inoltre di pre-ridimensionare un'app in precedenza un carico elevato. L'app viene scalata orizzontalmente, innanzitutto aumenta in istanze di pre-warmed. Altre istanze continuano a disconnettersi e a caldo immediatamente in preparazione per l'operazione di ridimensionamento successivo del buffer. Grazie all'uso di un buffer di pre-riscaldate istanze, è possibile evitare in modo efficace le latenze di avvio a freddo.  Pre-riscaldate istanze è una funzionalità del piano Premium, è necessario conservare almeno un'istanza in esecuzione e disponibile in tutte le volte in cui il piano è attivo.

È possibile configurare il numero di istanze di pre-riscaldate nel portale di Azure portale mediante la selezione di **App per le funzioni**, uscita per il **funzionalità della piattaforma** scheda e selezionando il **Scale Out**le opzioni. Nella finestra di modifica di app di funzione, le istanze di pre-riscaldate viene specifiche dell'app, ma si applicano le istanze minime e massime per l'intero piano.

![Impostazioni di scalabilità elastica](./media/functions-premium-plan/scale-out.png)

È anche possibile configurare le istanze di pre-riscaldate per un'app con la CLI di Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Connettività di rete privata

Funzioni di Azure distribuite in un piano Premium consente di sfruttare [nuova integrazione rete virtuale per le app web](../app-service/web-sites-integrate-with-vnet.md).  Quando è configurato, l'app può comunicare con le risorse all'interno della rete virtuale o protetto tramite gli endpoint di servizio.  Le restrizioni IP sono inoltre disponibili nell'app per limitare il traffico in ingresso.

Quando si assegna una subnet per app per le funzioni in un piano Premium, necessaria una subnet con indirizzi IP sufficienti per ogni istanza potenziali. Anche se il numero massimo di istanze può variare durante l'anteprima, è necessario un blocco IP con indirizzi disponibili almeno 100.

Per ulteriori informazioni, vedere [integrare un'app di funzione in una rete virtuale](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Rapida scalabilità elastica

Le istanze di calcolo aggiuntive vengono aggiunti automaticamente per l'app usando la stessa logica di ridimensionamento rapido come il piano a consumo.  Per altre informazioni sul funzionamento del modo in cui ridimensionamento, vedere [funzione di ridimensionamento e hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Unbounded Durata esecuzione

Funzioni di Azure in un piano a consumo sono limitate a 10 minuti per una singola esecuzione.  Il piano Premium, la durata dell'esecuzione per impostazione predefinita a 30 minuti per evitare che le esecuzioni sfuggiti al controllo. Tuttavia, è possibile [modificare la configurazione host. JSON](./functions-host-json.md#functiontimeout) rendere questo illimitata per le app piano Premium.

In fase di anteprima, la durata non è garantita oltre 12 minuti e avrà le maggiori probabilità di esecuzione oltre 30 minuti se l'app non viene ampliata oltre il conteggio minimo di lavoro.

## <a name="plan-and-sku-settings"></a>Impostazioni piano e SKU

Quando si crea il piano, si configurare due impostazioni: il numero minimo di istanze (o dimensioni del piano) e il limite massimo di picco.  Il numero minimo di istanze per un piano Premium è 1, e l'aumento massimo durante l'anteprima è 20.  Numero minimo di istanze è riservate e sempre in esecuzione.

> [!IMPORTANT]
> Viene addebitata per ogni istanza allocata nel conteggio minimo di istanze indipendentemente dal fatto che se le funzioni sono in esecuzione o non.

Se l'app richiede istanze oltre le dimensioni del piano, è possibile continuare a scalabilità orizzontale fino a quando il numero di istanze raggiunge il limite massimo di picco.  Viene addebitato per le istanze oltre le dimensioni del piano solo mentre sono in esecuzione e in affitto all'utente.  Microsoft farà miglior a ridimensionare la tua app out per il limite massimo definito, mentre le istanze del piano minimo vengono garantite per le app.

È possibile configurare le dimensioni del piano e valori massimi nel portale di Azure, selezionare il **Scale Out** le opzioni in un'app per le funzioni distribuite in tale piano o il piano (sotto **funzionalità della piattaforma**).

È anche possibile aumentare il limite massimo di picco della riga di comando di Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKU delle istanze disponibili

Durante la creazione o il ridimensionamento del piano, è possibile scegliere tra tre dimensioni di istanza.  Ti verranno fatturate per il numero totale di core e memoria al secondo utilizzate.  Automaticamente l'app è possibile scalare orizzontalmente a più istanze in base alle esigenze.  

|SKU|Core|Memoria|Archiviazione|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regioni

Di seguito sono le aree attualmente supportate per l'anteprima pubblica.

|Region|
|--|
|Australia orientale|
|Australia sud-orientale|
|Canada centrale|
|Stati Uniti centrali|
|Asia orientale|
|Stati Uniti orientali 2|
|Francia centrale|
|Giappone occidentale|
|Corea del Sud centrale|
|Stati Uniti centro-settentrionali|
|Europa settentrionale|
|Stati Uniti centro-meridionali|
|India meridionale|
|Asia sud-orientale|
|Regno Unito occidentale|
|Europa occidentale|
|India occidentale|
|Stati Uniti occidentali|

## <a name="known-issues"></a>Problemi noti

È possibile tenere traccia dello stato dei problemi noti del [versione di anteprima pubblica su GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Comprendere la scalabilità di funzioni di Azure e le opzioni di hosting](functions-scale.md)
