---
title: Opzioni di rete di Funzioni di Azure
description: Panoramica di tutte le opzioni di rete disponibili in Funzioni di Azure.An overview of all networking options available in Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419582"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di Funzioni di Azure

Questo articolo descrive le funzionalità di rete disponibili nelle opzioni di hosting per Funzioni di Azure.This article describes the networking features available across the hosting options for Azure Functions. Tutte le seguenti opzioni di rete ti danno la possibilità di accedere alle risorse senza utilizzare indirizzi instradabili su Internet o di limitare l'accesso a Internet a un'app per le funzioni.

I modelli di hosting hanno diversi livelli di isolamento della rete disponibili. La scelta di quella corretta consente di soddisfare i requisiti di isolamento rete.

È possibile ospitare le app per le funzioni in due modi:You can host function apps in a couple ways:

* È possibile scegliere tra le opzioni del piano eseguite in un'infrastruttura multi-tenant, con vari livelli di connettività di rete virtuale e opzioni di scalabilità:You can choose from plan options that run on a multitenant infrastructure, with various levels of virtual network connectivity and scaling options:
    * Il [piano Consumo](functions-scale.md#consumption-plan) viene ridimensionato dinamicamente in risposta al carico e offre opzioni di isolamento rete minime.
    * Il [piano Premium](functions-scale.md#premium-plan) viene inoltre scalato in modo dinamico e offre un isolamento della rete più completo.
    * Il [piano di servizio app](functions-scale.md#app-service-plan) di Azure opera su scala fissa e offre un isolamento di rete simile al piano Premium.The Azure App Service plan operates at a fixed scale and offers network isolation similar to the Premium plan.
* È possibile eseguire funzioni in un ambiente del [servizio app.](../app-service/environment/intro.md) Questo metodo distribuisce la funzione nella rete virtuale e offre il controllo completo della rete e l'isolamento.

## <a name="matrix-of-networking-features"></a>Matrice di funzionalità di rete

|                |[Piano di consumo](functions-scale.md#consumption-plan)|[Piano Premium](functions-scale.md#premium-plan)|[Piano di servizio app](functions-scale.md#app-service-plan)|[Ambiente del servizio app](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrizioni IP in ingresso e accesso al sito privato](#inbound-ip-restrictions)|✅Sì|✅Sì|✅Sì|✅Sì|
|[Integrazione della rete virtuale](#virtual-network-integration)|❌No|✅Sì (regionale)|✅Sì (Regionale e Gateway)|✅Sì|
|[Trigger di rete virtuale (non HTTP)Virtual network triggers (non-HTTP)](#virtual-network-triggers-non-http)|❌No| ✅Sì |✅Sì|✅Sì|
|[Connessioni ibride](#hybrid-connections) (solo Windows)|❌No|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](#outbound-ip-restrictions)|❌No| ✅Sì|✅Sì|✅Sì|

## <a name="inbound-ip-restrictions"></a>Restrizioni IP in ingresso

Puoi usare le restrizioni IP per definire un elenco ordinato con priorità di indirizzi IP a cui è consentito o negato l'accesso all'app. L'elenco può includere indirizzi IPv4 e IPv6. Quando sono presenti una o più voci, alla fine dell'elenco esiste un implicito "Nega tutto". Le restrizioni IP funzionano con tutte le opzioni di hosting delle funzioni.

> [!NOTE]
> Con le restrizioni di rete, è possibile usare l'editor del portale solo dall'interno della rete virtuale o dopo aver inserito l'indirizzo IP del computer in uso per accedere al portale di Azure nell'elenco Destinatari attendibili. Tuttavia, è comunque possibile accedere a tutte le funzionalità nella scheda **Funzionalità piattaforma** da qualsiasi computer.

Per altre informazioni, vedere Restrizioni di accesso statico del servizio app di Azure .To learn more, see [Azure App Service static access restrictions](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accesso al sito privato

L'accesso al sito privato si riferisce a rendere l'app accessibile solo da una rete privata, ad esempio una rete virtuale di Azure.Private site access refers to making your app accessible only from a private network, such as an Azure virtual network.

* L'accesso al sito privato è disponibile nei piani [Premium,](./functions-premium-plan.md) [Consumption](functions-scale.md#consumption-plan)e [App Service](functions-scale.md#app-service-plan) quando vengono configurati gli endpoint del servizio.
    * Gli endpoint di servizio possono essere configurati in base all'app in **Funzionalità** > di rete**Regola** > aggiunta**configurazione restrizioni** > **di**accesso . Le reti virtuali possono ora essere selezionate come tipo di regola.
    * Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Tenere presente che con gli endpoint del servizio, la funzione dispone ancora di accesso completo in uscita a Internet, anche con l'integrazione di rete virtuale configurata.
* L'accesso al sito privato è disponibile anche in un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno. Per altre informazioni, vedere Creare e usare un servizio di [bilanciamento del carico interno con un](../app-service/environment/create-ilb-ase.md)ambiente del servizio app.

Per informazioni su come configurare l'accesso al sito privato, vedere Stabilire l'accesso al sito privato di Funzioni di [Azure.](functions-create-private-site-access.md)

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

L'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse all'interno di una rete virtuale.
Funzioni di Azure supporta due tipi di integrazione della rete virtuale:Azure Functions supports two kinds of virtual network integration:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

L'integrazione della rete virtuale in Funzioni di Azure usa l'infrastruttura condivisa con le app Web del servizio app. Per altre informazioni sui due tipi di integrazione della rete virtuale, vedere:To learn more about the two types of virtual network integration, see:

* [Integrazione della rete virtuale regionale](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrazione della rete virtuale richiesta dal gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Per informazioni su come configurare l'integrazione della rete virtuale, vedere [Integrare un'app](functions-create-vnet.md)per le funzioni con una rete virtuale di Azure.To learn how to set up virtual network integration, see Integrate a function app with an Azure virtual network .

## <a name="regional-virtual-network-integration"></a>Integrazione della rete virtuale regionale

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Connettersi alle risorse protette dell'endpoint del servizioConnect to service endpoint secured resources

Per fornire un livello di sicurezza più elevato, è possibile limitare un numero di servizi di Azure a una rete virtuale usando gli endpoint del servizio. È quindi necessario integrare l'app per le funzioni con la rete virtuale per accedere alla risorsa. Questa configurazione è supportata in tutti i piani che supportano l'integrazione della rete virtuale.

Per altre informazioni, vedere Endpoint del [servizio di rete virtuale.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Limitare l'account di archiviazione a una rete virtualeRestrict your storage account to a virtual network

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure generico che supporti l'archiviazione BLOB, coda e tabelle. Al momento non è possibile utilizzare restrizioni di rete virtuale per questo account. Se si configura un endpoint del servizio di rete virtuale nell'account di archiviazione usato per l'app per le funzioni, tale configurazione interromperà l'app.

Per altre informazioni, vedere [Requisiti dell'account di archiviazione](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Usare i riferimenti dell'insieme di credenziali delle chiaviUse Key Vault references

È possibile usare i riferimenti dell'insieme di chiavi di Azure per usare i segreti dall'insieme di credenziali delle chiavi di Azure nell'applicazione Funzioni di Azure senza richiedere modifiche al codice. Azure Key Vault è un servizio che supporta la gestione centralizzata dei segreti con controllo completo sui criteri di accesso e sulla cronologia di controllo.

Attualmente, [i riferimenti dell'insieme](../app-service/app-service-key-vault-references.md) di credenziali delle chiavi non funzioneranno se l'insieme di credenziali delle chiavi è protetto con endpoint di servizio. Per connettersi a un insieme di credenziali delle chiavi tramite l'integrazione di rete virtuale, è necessario chiamare Key Vault nel codice dell'applicazione.

## <a name="virtual-network-triggers-non-http"></a>Trigger di rete virtuale (non HTTP)Virtual network triggers (non-HTTP)

Attualmente, è possibile utilizzare funzioni trigger non HTTP dall'interno di una rete virtuale in uno dei due modi seguenti:Currently, you can use non-HTTP trigger functions from within a virtual network in one of two ways:

+ Eseguire l'app per le funzioni in un piano Premium e abilitare il supporto dei trigger di rete virtuale.
+ Eseguire l'app per le funzioni in un piano di servizio app o in un ambiente del servizio app.

### <a name="premium-plan-with-virtual-network-triggers"></a>Piano Premium con trigger di rete virtuale

Quando si esegue un piano Premium, è possibile connettere funzioni trigger non HTTP ai servizi eseguiti all'interno di una rete virtuale. A tale scopo, è necessario abilitare il supporto dei trigger di rete virtuale per l'app per le funzioni. L'impostazione **Supporto trigger rete virtuale** è disponibile nel portale di [Azure](https://portal.azure.com) in Impostazioni **app per**le funzioni .

![Attiva/disattiva rete virtuale](media/functions-networking-options/virtual-network-trigger-toggle.png)

È anche possibile abilitare i trigger di rete virtuale usando il comando dell'interfaccia della riga di comando di Azure seguente:You can also enable virtual network triggers by using the following Azure CLI command:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

I trigger di rete virtuale sono supportati nella versione 2.x e successive del runtime di Funzioni.Virtual network triggers are supported in version 2.x and above of the Functions runtime. Sono supportati i seguenti tipi di trigger non HTTP.

| Estensione | Versione minima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 o superiore |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 o superiore|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 o superiore|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 o superiore|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 o superiore|

> [!IMPORTANT]
> Quando si abilita il supporto dei trigger di rete virtuale, solo i tipi di trigger illustrati nella tabella precedente vengono ridimensionati in modo dinamico con l'applicazione. È comunque possibile usare trigger non nella tabella, ma non vengono ridimensionati oltre il numero di istanze preriscaldate. Per l'elenco completo dei trigger, vedere [Trigger e associazioni](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Piano di servizio app e ambiente del servizio app con trigger di rete virtuale

Quando l'app per le funzioni viene eseguita in un piano di servizio app o in un ambiente del servizio app, è possibile usare funzioni trigger non HTTP. Affinché le funzioni vengano attivate correttamente, è necessario essere connessi a una rete virtuale con accesso alla risorsa definita nella connessione trigger.

Si supponga, ad esempio, di voler configurare il database Cosmos di Azure per accettare il traffico solo da una rete virtuale. In questo caso, è necessario distribuire l'app per le funzioni in un piano di servizio app che fornisce l'integrazione della rete virtuale con tale rete virtuale. L'integrazione consente a una funzione di essere attivata da tale risorsa del database Cosmos di Azure.Integration enables a function to be triggered by that Azure Cosmos DB resource.

## <a name="hybrid-connections"></a>connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità di inoltro di Azure che è possibile usare per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile utilizzarlo per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni eseguite in Windows in tutti i piani di consumo tranne.

Come usato in Funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di porte e host TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e applicazione, purché si acceda a una porta di attesa TCP. La funzionalità Connessioni ibride non conosce né si preoccupa del protocollo dell'applicazione o dell'accesso. Fornisce solo l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio app per](../app-service/app-service-hybrid-connections.md)le connessioni ibride . Questi stessi passaggi di configurazione supportano Funzioni di Azure.These same configuration steps support Azure Functions.

>[!IMPORTANT]
> Connessioni ibride è supportato solo nei piani di Windows.Hybrid Connections is only supported on Windows plans. Linux non è supportato.

## <a name="outbound-ip-restrictions"></a>Restrizioni IP in uscita

Le restrizioni IP in uscita sono disponibili in un piano Premium, in un piano di servizio app o in un ambiente del servizio app. È possibile configurare le restrizioni in uscita per la rete virtuale in cui viene distribuito l'ambiente del servizio app.

Quando si integra un'app per le funzioni in un piano Premium o in un piano di servizio app con una rete virtuale, l'app può comunque effettuare chiamate in uscita verso Internet per impostazione predefinita. Aggiungendo l'impostazione `WEBSITE_VNET_ROUTE_ALL=1`dell'applicazione , si forza l'invio di tutto il traffico in uscita nella rete virtuale, in cui è possibile utilizzare le regole dei gruppi di sicurezza di rete per limitare il traffico.

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete e sulle funzioni di Azure:To learn more about networking and Azure Functions:

* [Seguire l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Leggi le Domande frequenti sulla rete di Funzioni](./functions-networking-faq.md)
* [Altre informazioni sull'integrazione della rete virtuale con Servizio app/FunzioniLearn more about virtual network integration with App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in AzureLearn more about virtual networks in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e il controllo con gli ambienti del servizio appEnable more networking features and control with App Service Environments](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche del firewall tramite connessioni ibrideConnect to individual on-premises resources without firewall changes by using Hybrid Connections](../app-service/app-service-hybrid-connections.md)
