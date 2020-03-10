---
title: Opzioni di rete di funzioni di Azure
description: Panoramica di tutte le opzioni di rete disponibili in funzioni di Azure.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945578"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di funzioni di Azure

Questo articolo descrive le funzionalità di rete disponibili nelle opzioni di hosting per funzioni di Azure. Tutte le seguenti opzioni di rete consentono di accedere alle risorse senza utilizzare indirizzi instradabili tramite Internet o di limitare l'accesso a Internet a un'app per le funzioni.

I modelli di hosting hanno diversi livelli di isolamento della rete disponibili. La scelta dell'opzione corretta consente di soddisfare i requisiti di isolamento rete.

È possibile ospitare le app per le funzioni in due modi:

* È disponibile un set di opzioni di piano che vengono eseguite in un'infrastruttura multi-tenant, con diversi livelli di opzioni di connettività e scalabilità della rete virtuale:
    * Il [piano a consumo](functions-scale.md#consumption-plan), che viene scalato dinamicamente in risposta al carico e offre opzioni di isolamento rete minime.
    * Il [piano Premium](functions-scale.md#premium-plan), anch ' esso scalabile dinamicamente, offrendo un isolamento di rete più completo.
    * Il [piano di servizio app](functions-scale.md#app-service-plan)di Azure, che opera a una scala fissa e offre un isolamento di rete simile a quello del piano Premium.
* È possibile eseguire funzioni in un [ambiente del servizio app](../app-service/environment/intro.md). Questo metodo distribuisce la funzione nella rete virtuale e offre un controllo di rete completo e un isolamento.

## <a name="matrix-of-networking-features"></a>Matrice delle funzionalità di rete

|                |[Piano a consumo](functions-scale.md#consumption-plan)|[Piano Premium](functions-scale.md#premium-plan)|[Piano di servizio app](functions-scale.md#app-service-plan)|[Ambiente del servizio app](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrizioni IP in ingresso & accesso al sito privato](#inbound-ip-restrictions)|✅Sì|✅Sì|✅Sì|✅Sì|
|[Integrazione della rete virtuale](#virtual-network-integration)|❌No|✅Sì (regione)|✅Sì (Regional e gateway)|✅Sì|
|[Trigger della rete virtuale (non HTTP)](#virtual-network-triggers-non-http)|❌No| ✅Sì |✅Sì|✅Sì|
|[Connessioni ibride](#hybrid-connections) (solo Windows)|❌No|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](#outbound-ip-restrictions)|❌No| ✅Sì|✅Sì|✅Sì|

## <a name="inbound-ip-restrictions"></a>Restrizioni degli indirizzi IP in ingresso

È possibile usare le restrizioni IP per definire un elenco di indirizzi IP ordinato in ordine di priorità a cui è consentito o negato l'accesso all'app. L'elenco può includere indirizzi IPv4 e IPv6. Quando sono presenti una o più voci, alla fine dell'elenco esiste una "Deny All" implicita. Le restrizioni IP funzionano con tutte le opzioni di hosting di funzioni.

> [!NOTE]
> Con le restrizioni di rete, è possibile usare l'editor del portale solo dall'interno della rete virtuale o quando è stato inserito l'indirizzo IP del computer usato per accedere al portale di Azure nell'elenco dei destinatari sicuri. Tuttavia, è comunque possibile accedere alle funzionalità della scheda **funzionalità della piattaforma** da qualsiasi computer.

Per altre informazioni, vedere [restrizioni di accesso statico del servizio app Azure](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accesso al sito privato

L'accesso al sito privato si riferisce al fatto che l'app sia accessibile solo da una rete privata, ad esempio una rete virtuale di Azure.

* L'accesso al sito privato è disponibile nei piani di [servizio app](functions-scale.md#app-service-plan) , a [consumo](functions-scale.md#consumption-plan)e [Premium](./functions-premium-plan.md)quando sono configurati gli endpoint di servizio.
    * Gli endpoint di servizio possono essere configurati in base alle singole app in **funzionalità della piattaforma** > **rete** > configurare le **restrizioni di accesso** > **Aggiungi regola**. È ora possibile selezionare le reti virtuali come tipo di regola.
    * Per ulteriori informazioni, vedere [endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Tenere presente che con gli endpoint di servizio, la funzione ha ancora l'accesso in uscita completo a Internet, anche con l'integrazione della rete virtuale configurata.
* L'accesso al sito privato è disponibile anche all'interno di un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno (ILB). Per altre informazioni, vedere [creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app](../app-service/environment/create-ilb-ase.md).

Per informazioni su come configurare l'accesso al sito privato, vedere [stabilire l'accesso al sito privato di funzioni di Azure](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

L'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse all'interno di una rete virtuale. Funzioni di Azure supporta due tipi di integrazione della rete virtuale:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

L'integrazione della rete virtuale in funzioni di Azure usa l'infrastruttura condivisa con le app Web del servizio app. Per ulteriori informazioni sui due tipi di integrazione della rete virtuale, vedere:

* [Integrazione della rete virtuale a livello di area](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrazione della rete virtuale obbligatoria del gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Per informazioni su come configurare l'integrazione della rete virtuale, vedere [integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integrazione della rete virtuale a livello di area

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>Connessione alle risorse protette dell'endpoint di servizio

Per garantire un livello di sicurezza più elevato, è possibile limitare un numero di servizi di Azure a una rete virtuale usando gli endpoint di servizio. Per accedere alla risorsa, è quindi necessario integrare l'app per le funzioni con tale rete virtuale. Questa configurazione è supportata in tutti i piani che supportano l'integrazione della rete virtuale.

[Altre informazioni sugli endpoint del servizio rete virtuale.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>Limitazione dell'account di archiviazione a una rete virtuale

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Attualmente non è possibile usare alcuna restrizione di rete virtuale per questo account. Se si configura un endpoint del servizio di rete virtuale nell'account di archiviazione usato per l'app per le funzioni, l'app verrà interrotta.

[Altre informazioni sui requisiti dell'account di archiviazione.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>Uso di riferimenti Key Vault 

I riferimenti Key Vault consentono di usare i segreti Azure Key Vault nell'applicazione funzioni di Azure senza richiedere modifiche al codice. Azure Key Vault è un servizio che fornisce una gestione centralizzata dei segreti, con controllo completo sui criteri di accesso e sulla cronologia di controllo.

Attualmente [Key Vault riferimenti](../app-service/app-service-key-vault-references.md) non funzioneranno se il Key Vault è protetto con gli endpoint di servizio. Per connettersi a un Key Vault usando l'integrazione della rete virtuale, è necessario chiamare Key Vault nel codice dell'applicazione.

## <a name="virtual-network-triggers-non-http"></a>Trigger della rete virtuale (non HTTP)

Attualmente, è possibile usare funzioni trigger non HTTP da una rete virtuale in uno dei due modi seguenti: 
+ Eseguire l'app per le funzioni in un piano Premium e abilitare il supporto dei trigger della rete virtuale.
+ Eseguire l'app per le funzioni in un piano di servizio app o in ambiente del servizio app.

### <a name="premium-plan-with-virtual-network-triggers"></a>Piano Premium con trigger di rete virtuale

Quando è in esecuzione in un piano Premium, è possibile connettere funzioni trigger non HTTP ai servizi in esecuzione all'interno di una rete virtuale. A tale scopo, è necessario abilitare il supporto dei trigger della rete virtuale per l'app per le funzioni. L'impostazione di supporto per il **trigger della rete virtuale** si trova nel [portale di Azure](https://portal.azure.com) in **impostazioni app**per le funzioni.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

È anche possibile abilitare i trigger della rete virtuale usando il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

I trigger della rete virtuale sono supportati nella versione 2. x e successive del runtime di funzioni. Sono supportati i tipi di trigger non HTTP seguenti.

| Estensione | Versione minima |
|-----------|---------| 
|[Microsoft. Azure. webjobs. Extensions. storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 o versione successiva |
|[Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 o versione successiva|
|[Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 o versione successiva|
|[Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 o versione successiva|
|[Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 o versione successiva|

> [!IMPORTANT]
> Quando si Abilita il supporto dei trigger della rete virtuale, solo i tipi di trigger sopra riportano in modo dinamico con l'applicazione. È comunque possibile usare i trigger non elencati sopra, ma non vengono ridimensionati oltre il numero di istanze pre-riscaldate. Per l'elenco completo dei trigger [, vedere Trigger e associazioni](./functions-triggers-bindings.md#supported-bindings) .

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Piano di servizio app e ambiente del servizio app con i trigger della rete virtuale

Quando l'app per le funzioni viene eseguita in un piano di servizio app o in una ambiente del servizio app, è possibile usare funzioni trigger non HTTP. Affinché le funzioni vengano attivate correttamente, è necessario essere connessi a una rete virtuale con accesso alla risorsa definita nella connessione trigger. 

Si supponga, ad esempio, di voler configurare Azure Cosmos DB per accettare il traffico solo da una rete virtuale. In questo caso, è necessario distribuire l'app per le funzioni in un piano di servizio app che fornisce l'integrazione della rete virtuale con tale rete virtuale. Ciò consente a una funzione di essere attivata da tale risorsa Azure Cosmos DB. 

## <a name="hybrid-connections"></a>connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità del servizio di inoltro di Azure che è possibile usare per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile usarlo per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni in esecuzione in Windows in tutto tranne il piano a consumo.

Come usato in funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e qualsiasi applicazione, purché si acceda a una porta di ascolto TCP. La funzionalità Connessioni ibride non conosce né interessa il protocollo dell'applicazione o ciò che si sta accedendo. Fornisce solo l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio app per connessioni ibride](../app-service/app-service-hybrid-connections.md). Questi stessi passaggi di configurazione supportano funzioni di Azure.

>[!IMPORTANT]
> Connessioni ibride è supportato solo nei piani di Windows. Linux non è supportato

## <a name="outbound-ip-restrictions"></a>Restrizioni IP in uscita

Le restrizioni IP in uscita sono disponibili in un piano Premium, in un piano di servizio app o in ambiente del servizio app. È possibile configurare le restrizioni in uscita per la rete virtuale in cui è distribuita la ambiente del servizio app.

Quando si integra un'app per le funzioni in un piano Premium o un piano di servizio app con una rete virtuale, per impostazione predefinita l'app può comunque effettuare chiamate in uscita a Internet. Aggiungendo un'impostazione dell'applicazione `WEBSITE_VNET_ROUTE_ALL=1`, si forza l'invio di tutto il traffico in uscita nella rete virtuale, in cui è possibile usare le regole del gruppo di sicurezza di rete per limitare il traffico.

## <a name="troubleshooting"></a>risoluzione dei problemi 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su rete e funzioni di Azure:

* [Segui l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Leggi le domande frequenti sulle funzionalità di rete](./functions-networking-faq.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio app/funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e controllare con gli ambienti del servizio app](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando Connessioni ibride](../app-service/app-service-hybrid-connections.md)
