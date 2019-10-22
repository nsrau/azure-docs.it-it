---
title: Opzioni di rete di funzioni di Azure
description: Panoramica di tutte le opzioni di rete disponibili in funzioni di Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 9fe7147325b2e14a7ae6bb4b31aa941fb4059b11
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690821"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di funzioni di Azure

Questo articolo descrive le funzionalità di rete disponibili nelle opzioni di hosting per funzioni di Azure. Tutte le seguenti opzioni di rete consentono di accedere alle risorse senza utilizzare indirizzi instradabili tramite Internet o di limitare l'accesso a Internet a un'app per le funzioni. 

I modelli di hosting hanno diversi livelli di isolamento della rete disponibili. La scelta dell'opzione corretta consente di soddisfare i requisiti di isolamento rete.

È possibile ospitare le app per le funzioni in due modi:

* È disponibile un set di opzioni di piano che vengono eseguite in un'infrastruttura multi-tenant, con diversi livelli di opzioni di connettività e scalabilità della rete virtuale:
    * Il [piano a consumo](functions-scale.md#consumption-plan), che viene scalato dinamicamente in risposta al carico e offre opzioni di isolamento rete minime.
    * Il [piano Premium](functions-scale.md#premium-plan), anch ' esso scalabile dinamicamente, offrendo un isolamento di rete più completo.
    * Il [piano di servizio app](functions-scale.md#app-service-plan)di Azure, che opera a una scala fissa e offre un isolamento di rete analogo al piano Premium.
* È possibile eseguire funzioni in un [ambiente del servizio app](../app-service/environment/intro.md). Questo metodo distribuisce la funzione nella rete virtuale e offre un controllo di rete completo e un isolamento.

## <a name="matrix-of-networking-features"></a>Matrice delle funzionalità di rete

|                |[Piano a consumo](functions-scale.md#consumption-plan)|[Piano Premium (anteprima)](functions-scale.md#premium-plan)|[Piano di servizio app](functions-scale.md#app-service-plan)|[Ambiente del servizio app](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrizioni IP in ingresso & accesso al sito privato](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrazione della rete virtuale](#virtual-network-integration)|❌No|✅Yes (regione)|✅Yes (Regional e gateway)|✅Yes|
|[Trigger della rete virtuale (non HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[connessioni ibride](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[Restrizioni IP in uscita](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Restrizioni degli indirizzi IP in ingresso

È possibile usare le restrizioni IP per definire un elenco con priorità di indirizzi IP consentiti o negati per l'accesso all'app. L'elenco può includere indirizzi IPv4 e IPv6. Quando sono presenti una o più voci, alla fine dell'elenco esiste una "Deny All" implicita. Le restrizioni IP funzionano con tutte le opzioni di hosting di funzioni.

> [!NOTE]
> Con le restrizioni di rete, è possibile usare l'editor del portale solo dall'interno della rete virtuale o quando è stato inserito nell'elenco elementi consentiti l'indirizzo IP del computer usato per accedere al portale di Azure. Tuttavia, è comunque possibile accedere alle funzionalità della scheda **funzionalità della piattaforma** da qualsiasi computer.

Per altre informazioni, vedere [restrizioni di accesso statico del servizio app Azure](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Accesso al sito privato

L'accesso al sito privato si riferisce a rendere l'app accessibile solo da una rete privata, ad esempio dall'interno di una rete virtuale di Azure. 
* L'accesso al sito privato è disponibile nel piano [Premium](./functions-premium-plan.md), [consumo], (Functions-scale. MD # consumo-piano) e nel [piano di servizio app](functions-scale.md#app-service-plan) quando sono configurati gli **endpoint di servizio** . 
    * Gli endpoint di servizio possono essere configurati in base alle singole app in funzionalità della piattaforma > rete > configurare le restrizioni di accesso > Aggiungi regola. È ora possibile selezionare le reti virtuali come "tipo" di una regola.
    * Per altre informazioni, vedere [endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md)
        * Tenere presente che con gli endpoint di servizio, la funzione ha ancora l'accesso in uscita completo a Internet, anche con l'integrazione della rete virtuale configurata.
* L'accesso al sito privato è disponibile anche con un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno (ILB). Per altre informazioni, vedere [creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

L'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse all'interno di una rete virtuale. Questa funzionalità è disponibile sia nel piano Premium che nel piano di servizio app. Se l'app si trova in una ambiente del servizio app, è già in una rete virtuale e non richiede l'uso dell'integrazione della rete virtuale per raggiungere le risorse nella stessa rete virtuale.

È possibile usare l'integrazione della rete virtuale per consentire l'accesso da app a database e servizi Web in esecuzione nella rete virtuale. Con l'integrazione della rete virtuale, non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale. È invece possibile usare gli indirizzi instradabili non Internet privati.

Esistono due moduli per la funzionalità di integrazione della rete virtuale

1. L'integrazione della rete virtuale regionale consente l'integrazione con reti virtuali nella stessa area. Questo formato della funzionalità richiede una subnet in una rete virtuale nella stessa area. Questa funzionalità è ancora in anteprima, ma è supportata per i carichi di lavoro di produzione delle app Windows con alcune avvertenze indicate di seguito.
2. L'integrazione della rete virtuale necessaria per il gateway consente l'integrazione con reti virtuali in aree remote o con reti virtuali classiche. Questa versione della funzionalità richiede la distribuzione di un gateway di rete virtuale nella VNet. Si tratta della funzionalità basata su VPN da punto a sito ed è supportata solo con le app di Windows.

Un'app può usare solo un modulo della funzionalità di integrazione VNet alla volta. La domanda è la funzionalità da usare. È possibile utilizzare per molti aspetti. Tuttavia, i differenziatori chiari sono:

| Problema  | Soluzione | 
|----------|----------|
| Si desidera raggiungere un indirizzo RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) nella stessa area | Integrazione VNet a livello di area |
| Si vuole raggiungere le risorse in una VNet classica o in una VNet in un'altra area | Integrazione VNet necessaria per il gateway |
| Si vuole raggiungere gli endpoint RFC 1918 tra ExpressRoute | Integrazione VNet a livello di area |
| Desidera raggiungere risorse tra gli endpoint del servizio | Integrazione VNet a livello di area |

Nessuna delle due funzionalità consentirà di raggiungere indirizzi non RFC 1918 tra ExpressRoute. A tale scopo, è necessario usare un ambiente del servizio app per il momento.

L'uso dell'integrazione VNet a livello di area non connette il VNet a locale o configura gli endpoint di servizio. Si tratta di una configurazione di rete separata. L'integrazione VNet regionale consente semplicemente all'app di effettuare chiamate tra i tipi di connessione.

Indipendentemente dalla versione usata, l'integrazione di VNet consente all'app per le funzioni di accedere alle risorse nella rete virtuale, ma non concede l'accesso al sito privato all'app per le funzioni dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. 

La funzionalità Integrazione rete virtuale:

* Richiede un piano di servizio app standard, Premium o PremiumV2
* supporta TCP e UDP
* funziona con le app del servizio app e con le app per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* Montaggio di un'unità
* Integrazione di AD 
* NetBios

L'integrazione della rete virtuale in funzioni usa l'infrastruttura condivisa con le app Web del servizio app. Per ulteriori informazioni sui due tipi di integrazione della rete virtuale, vedere:
* [integrazione rete virtuale regionali](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrazione VNet necessaria per il gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Per altre informazioni sull'uso dell'integrazione della rete virtuale, vedere [integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Connessione alle risorse protette dell'endpoint di servizio

> [!note] 
> Temporaneamente, potrebbero essere necessarie fino a 12 ore affinché i nuovi endpoint di servizio diventino disponibili per l'app per le funzioni una volta configurate le restrizioni di accesso per la risorsa downstream. Durante questo periodo, la risorsa non sarà completamente disponibile per l'app.

Per garantire un livello di sicurezza più elevato, è possibile limitare un numero di servizi di Azure a una rete virtuale usando gli endpoint di servizio. Per accedere alla risorsa, è quindi necessario integrare l'app per le funzioni con tale rete virtuale. Questa configurazione è supportata in tutti i piani che supportano l'integrazione della rete virtuale.

[Per altre informazioni sugli endpoint del servizio rete virtuale, vedere qui.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Limitazione dell'account di archiviazione a una rete virtuale
Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Attualmente non è possibile utilizzare restrizioni della rete virtuale per questo account. Se si configura un endpoint del servizio di rete virtuale nell'account di archiviazione usato per l'app per le funzioni, l'app verrà interrotta.

[Per altre informazioni sui requisiti dell'account di archiviazione, vedere qui.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Trigger della rete virtuale (non HTTP)

Attualmente, per poter usare trigger di funzione diversi da HTTP dall'interno di una rete virtuale, è necessario eseguire l'app per le funzioni in un piano di servizio app o in un ambiente del servizio app.

Per fornire un esempio, se è necessario configurare Azure Cosmos DB per accettare solo il traffico da una rete virtuale, è necessario distribuire l'app per le funzioni in un piano di servizio app con l'integrazione della rete virtuale con tale rete virtuale per configurare i trigger Azure Cosmos DB da tale risorsa. Durante la fase di anteprima, la configurazione dell'integrazione di VNET non consentirà al piano Premium di attivare tale risorsa Azure Cosmos DB.

Controllare [questo elenco per tutti i trigger non http](./functions-triggers-bindings.md#supported-bindings) per verificare che il valore sia supportato.

## <a name="hybrid-connections"></a>Connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità del servizio di inoltro di Azure che è possibile usare per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile usarlo per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni in esecuzione in tutti i piani a consumo.

Come usato in funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e qualsiasi applicazione, a condizione che si acceda a una porta di ascolto TCP. La funzionalità Connessioni ibride non conosce né interessa il protocollo dell'applicazione o ciò che si sta accedendo. Fornisce semplicemente l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio app per connessioni ibride](../app-service/app-service-hybrid-connections.md), che supporta le funzioni tramite gli stessi passaggi di configurazione.

## <a name="outbound-ip-restrictions"></a>Restrizioni IP in uscita

Le restrizioni IP in uscita sono disponibili solo per le funzioni distribuite in un ambiente del servizio app. È possibile configurare le restrizioni in uscita per la rete virtuale in cui è distribuita la ambiente del servizio app.

Quando si integra un'app per le funzioni in un piano Premium o un piano di servizio app con una rete virtuale, l'app è ancora in grado di effettuare chiamate in uscita a Internet.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su rete e funzioni di Azure: 

* [Segui l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Leggi le domande frequenti sulle funzionalità di rete](./functions-networking-faq.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio app/funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e controllare con gli ambienti del servizio app](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando Connessioni ibride](../app-service/app-service-hybrid-connections.md)
