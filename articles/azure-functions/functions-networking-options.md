---
title: Opzioni di rete di Funzioni di Azure
description: Panoramica di tutte le opzioni di rete disponibili in Funzioni di Azure.
ms.topic: conceptual
ms.date: 4/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 271730e57a2d7ef8324420744b4bcd088b9809cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530090"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di Funzioni di Azure

Questo articolo descrive le funzionalità di rete disponibili nelle opzioni di hosting per Funzioni di Azure. Tutte le opzioni di rete seguenti consentono di accedere in vari modi alle risorse senza usare indirizzi instradabili tramite Internet o di limitare l'accesso a Internet a un'app per le funzioni.

Nei modelli di hosting sono disponibili diversi livelli di isolamento della rete. La scelta dell'opzione corretta consente di soddisfare i requisiti di isolamento della rete.

È possibile ospitare le app per le funzioni in due modi:

* È possibile scegliere tra le opzioni di piano eseguite in un'infrastruttura multi-tenant, con diversi livelli di opzioni di connettività e scalabilità della rete virtuale:
    * Il [Piano a consumo](functions-scale.md#consumption-plan) si ridimensiona dinamicamente in risposta al carico e offre opzioni minime di isolamento rete.
    * Anche il [piano Premium](functions-scale.md#premium-plan) si ridimensiona dinamicamente e offre un isolamento rete più completo.
    * Il [piano di servizio app](functions-scale.md#app-service-plan) di Azure opera su scala fissa e offre un isolamento rete simile a quello del piano Premium.
* È possibile eseguire funzioni in un [ambiente del servizio app](../app-service/environment/intro.md). Questo metodo distribuisce la funzione nella rete virtuale e offre controllo di rete e isolamento completi.

## <a name="matrix-of-networking-features"></a>Matrice delle funzionalità di rete

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>Restrizioni IP in ingresso

È possibile usare le restrizioni IP per definire un elenco in ordine di priorità di indirizzi IP ai quali è consentito o negato l'accesso all'app. L'elenco può includere indirizzi IPv4 e IPv6. In presenza di una o più voci, alla fine dell'elenco è presente un'istruzione di tipo "rifiuta tutto" implicita. Le restrizioni IP funzionano con tutte le opzioni di hosting di funzioni.

> [!NOTE]
> Con le restrizioni di rete implementate, è possibile usare l'editor del portale solo dall'interno della rete virtuale o quando l'indirizzo IP del computer usato per accedere al portale di Azure è stato inserito nell'elenco dei destinatari sicuri. È tuttavia possibile accedere a qualsiasi funzionalità della scheda **Funzionalità della piattaforma** da qualsiasi computer.

Per altre informazioni, vedere [Restrizioni di accesso al servizio app di Azure](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accesso al sito privato

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

L'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse all'interno di una rete virtuale.
Funzioni di Azure supporta due tipi di integrazione della rete virtuale:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

L'integrazione della rete virtuale in Funzioni di Azure usa l'infrastruttura condivisa con le app Web del servizio app. Per altre informazioni sui due tipi di integrazione della rete virtuale, vedere:

* [Uso dell'integrazione della rete virtuale](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrazione della rete virtuale richiesta dal gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Per informazioni su come configurare l'integrazione della rete virtuale, vedere [Integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integrazione della rete virtuale regionale

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Connettersi a risorse protette dell'endpoint di servizio

Per garantire un livello di sicurezza più elevato, è possibile limitare vari servizi di Azure a una rete virtuale usando gli endpoint di servizio. Sarà quindi necessario integrare l'app per le funzioni con la rete virtuale per accedere alla risorsa. Questa configurazione è supportata in tutti i piani che supportano l'integrazione della rete virtuale.

Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Limitare l'account di archiviazione a una rete virtuale

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporta l'archiviazione BLOB, Coda e Tabella. Attualmente non è possibile usare nessuna restrizione di rete virtuale per questo account. Se si configura un endpoint del servizio di rete virtuale nell'account di archiviazione usato per l'app per le funzioni, questa configurazione interrompe l'esecuzione dell'app.

Per altre informazioni, vedere [Requisiti dell'account di archiviazione](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Usare i riferimenti di Key Vault

È possibile usare i riferimenti di Azure Key Vault per usare segreti di Azure Key Vault nell'applicazione Funzioni di Azure senza modificare il codice. Azure Key Vault è un servizio che supporta la gestione centralizzata dei segreti, con controllo completo sui criteri di accesso e sulla cronologia di controllo.

Attualmente i [riferimenti di Key Vault](../app-service/app-service-key-vault-references.md) non funzionano se l'insieme di credenziali delle chiavi è protetto con gli endpoint di servizio. Per connettersi a un insieme di credenziali delle chiavi tramite l'integrazione della rete virtuale, è necessario chiamare Key Vault nel codice dell'applicazione.

## <a name="virtual-network-triggers-non-http"></a>Trigger della rete virtuale (non HTTP)

Attualmente è possibile usare funzioni trigger non HTTP da una rete virtuale in uno dei due modi seguenti:

+ Eseguire l'app per le funzioni in un piano Premium e abilitare il supporto dei trigger della rete virtuale.
+ Eseguire l'app per le funzioni in un piano di servizio app o in un ambiente del servizio app.

### <a name="premium-plan-with-virtual-network-triggers"></a>Piano Premium con trigger di rete virtuale

Quando si esegue un piano Premium, è possibile connettere funzioni trigger non HTTP a servizi in esecuzione all'interno di una rete virtuale. A tale scopo è necessario abilitare il supporto dei trigger della rete virtuale per l'app per le funzioni. L'impostazione di **monitoraggio della scalabilità di runtime** si trova nel [portale di Azure](https://portal.azure.com) in **Configuration**  >  **impostazioni runtime funzione**di configurazione.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

È anche possibile abilitare i trigger della rete virtuale usando il seguente comando dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

I trigger della rete virtuale sono supportati nella versione 2.x e versioni successive del runtime di Funzioni. Sono supportati i tipi di trigger non HTTP seguenti.

| Estensione | Versione minima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 o versioni successive |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 o versioni successive|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 o versioni successive|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 o versioni successive|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 o versioni successive|

> [!IMPORTANT]
> Quando si abilita il supporto dei trigger della rete virtuale, solo i tipi di trigger indicati nella tabella precedente vengono ridimensionati dinamicamente con l'applicazione. È comunque possibile usare trigger non presenti nella tabella, ma questi non vengono ridimensionati oltre il numero di istanze preriscaldate. Per l'elenco completo dei trigger, vedere [Trigger e associazioni](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Piano di servizio app e ambiente del servizio app con trigger della rete virtuale

Quando l'app per le funzioni viene eseguita in un piano di servizio app o in un ambiente del servizio app, è possibile usare funzioni trigger non HTTP. Per garantire che le funzioni vengano attivate correttamente, è necessario essere connessi a una rete virtuale con accesso alla risorsa definita nella connessione trigger.

Si supponga ad esempio di voler configurare Azure Cosmos DB in modo che accetti traffico solo da una rete virtuale. In questo caso è necessario distribuire l'app per le funzioni in un piano di servizio app che offre l'integrazione della rete virtuale con quella rete virtuale specifica. L'integrazione consente a una funzione di essere attivata da quella risorsa Azure Cosmos DB.

## <a name="hybrid-connections"></a>connessioni ibride

Le [connessioni ibride](../azure-relay/relay-hybrid-connections-protocol.md) sono una funzionalità di Inoltro di Azure che consente di accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile usarla per accedere all'applicazione. Le connessioni ibride sono disponibili per le funzioni che vengono eseguite in Windows in tutte le versioni meno il Piano a consumo.

Come in Funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Questo significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e in qualsiasi applicazione, a condizione che si acceda a una porta TCP in ascolto. La funzionalità Connessioni ibride non conosce né deve conoscere qual è il protocollo dell'applicazione o a quale risorsa sta accedendo l'utente. Offre solo l'accesso alla rete.

Per altre informazioni, vedere la [Documentazione del servizio app per le connessioni ibride](../app-service/app-service-hybrid-connections.md). La stessa procedura di configurazione supporta Funzioni di Azure.

>[!IMPORTANT]
> Le connessioni ibride sono supportate solo nei piani di Windows. Linux non è supportato.

## <a name="outbound-ip-restrictions"></a>Restrizioni IP in uscita

Le restrizioni IP in uscita sono disponibili in un piano Premium, un piano di servizio app o un ambiente del servizio app. È possibile configurare le restrizioni in uscita per la rete virtuale in cui è distribuito l'ambiente del servizio app.

Quando si integra un'app per le funzioni in un piano Premium o in un piano di servizio app con una rete virtuale, per impostazione predefinita l'app può comunque effettuare chiamate in uscita a Internet. Se si aggiunge l'impostazione dell'applicazione `WEBSITE_VNET_ROUTE_ALL=1` si forza l'invio di tutto il traffico in uscita alla rete virtuale, in cui è possibile usare regole del gruppo di sicurezza di rete per limitare il traffico.

## <a name="automation"></a>Automazione
Le seguenti API consentono di gestire a livello di codice le integrazioni delle reti virtuali internazionali:

+ **Interfaccia**della riga di comando di Azure: usare i [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) comandi per aggiungere, elencare o rimuovere le integrazioni di una rete virtuale a livello di area.  
+ **Modelli ARM**: l'integrazione della rete virtuale a livello di area può essere abilitata usando un modello di Azure Resource Manager. Per un esempio completo, vedere [il modello di avvio rapido di funzioni](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete e su Funzioni di Azure:

* [Eseguire l'esercitazione Integrare Funzioni con una rete virtuale di Azure](./functions-create-vnet.md)
* [Leggere le Domande frequenti sulla rete di Funzioni](./functions-networking-faq.md)
* [Informazioni sull'integrazione della rete virtuale con il servizio app/Funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità e il controllo di rete con gli ambienti di servizio app](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando le connessioni ibride](../app-service/app-service-hybrid-connections.md)
