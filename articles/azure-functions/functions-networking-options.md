---
title: Opzioni di rete di funzioni di Azure
description: Panoramica di tutte le opzioni di rete disponibili in funzioni di Azure.
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 4e55932d47389e09b135d571d0e000b9795e6edc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884956"
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
|[Trigger della rete virtuale (non HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Sì|✅Sì|
|[Connessioni ibride](#hybrid-connections)|❌No|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Sì|

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

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

L'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse all'interno di una rete virtuale. Questa funzionalità è disponibile sia nel piano Premium che nel piano di servizio app. Se l'app si trova in una ambiente del servizio app, è già in una rete virtuale e non richiede l'integrazione della rete virtuale per raggiungere le risorse nella stessa rete virtuale.

È possibile usare l'integrazione della rete virtuale per consentire l'accesso da app a database e servizi Web in esecuzione nella rete virtuale. Con l'integrazione della rete virtuale, non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale. È invece possibile utilizzare indirizzi instradabili privati e non Internet.

Esistono due forme di integrazione della rete virtuale:

+ **Integrazione della rete virtuale regionale (anteprima)** : consente l'integrazione con reti virtuali nella stessa area. Questo tipo di integrazione richiede una subnet in una rete virtuale nella stessa area. Questa funzionalità è ancora in anteprima, ma è supportata per le app per le funzioni in esecuzione in Windows, con le avvertenze descritte dopo la seguente tabella di problemi/soluzioni.
+ **Integrazione della rete virtuale necessaria**per il gateway: consente l'integrazione con reti virtuali in aree remote o con reti virtuali classiche. Questo tipo di integrazione richiede la distribuzione di un gateway di rete virtuale nella VNet. Si tratta di una funzionalità basata su VPN da punto a sito, supportata solo per le app per le funzioni eseguite in Windows.

Un'app può usare solo un tipo di funzionalità di integrazione della rete virtuale alla volta. Sebbene entrambi siano utili per molti scenari, nella tabella seguente viene indicato dove è necessario utilizzare ciascuno di essi:

| Problema  | Soluzione |
|----------|----------|
| Si desidera raggiungere un indirizzo RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) nella stessa area | Integrazione della rete virtuale a livello di area |
| Si vuole raggiungere le risorse in una rete virtuale classica o in una rete virtuale in un'altra area | Integrazione della rete virtuale obbligatoria del gateway |
| Si vuole raggiungere gli endpoint RFC 1918 in Azure ExpressRoute | Integrazione della rete virtuale a livello di area |
| Desidera raggiungere risorse tra gli endpoint del servizio | Integrazione della rete virtuale a livello di area |

Nessuna delle due funzionalità consente di raggiungere indirizzi non RFC 1918 tra ExpressRoute. A tale scopo, attualmente è necessario usare un ambiente del servizio app.

L'uso dell'integrazione della rete virtuale a livello di area non connette la rete virtuale agli endpoint locali o configura gli endpoint di servizio. Si tratta di una configurazione di rete separata. L'integrazione di rete virtuale a livello di area consente solo all'app di effettuare chiamate tra i tipi di connessione.

Indipendentemente dalla versione usata, l'integrazione della rete virtuale consente all'app per le funzioni di accedere alle risorse nella rete virtuale, ma non concede l'accesso al sito privato all'app per le funzioni dalla rete virtuale. L'accesso al sito privato significa rendere l'app accessibile solo da una rete privata come una rete virtuale di Azure. l'integrazione della rete virtuale è solo per la creazione di chiamate in uscita dall'app nella rete virtuale.

Funzionalità di integrazione della rete virtuale:

* Richiede un piano di servizio app standard, Premium o PremiumV2
* Supporta TCP e UDP
* Funziona con le app del servizio app e le app per le funzioni

L'integrazione della rete virtuale non supporta alcune operazioni, tra cui:

* Montaggio di un'unità
* Integrazione in Active Directory
* NetBIOS

L'integrazione della rete virtuale in funzioni di Azure usa l'infrastruttura condivisa con le app Web del servizio app. Per ulteriori informazioni sui due tipi di integrazione della rete virtuale, vedere:

* [Integrazione della rete virtuale a livello di area](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrazione della rete virtuale obbligatoria del gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Per altre informazioni sull'uso dell'integrazione della rete virtuale, vedere [integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Connessione alle risorse protette dell'endpoint di servizio

> [!NOTE]
> Per il momento potrebbero essere necessarie fino a 12 ore prima che i nuovi endpoint di servizio diventino disponibili per l'app per le funzioni dopo aver configurato le restrizioni di accesso per la risorsa downstream. Durante questo periodo, la risorsa non sarà completamente disponibile per l'app.

Per garantire un livello di sicurezza più elevato, è possibile limitare un numero di servizi di Azure a una rete virtuale usando gli endpoint di servizio. Per accedere alla risorsa, è quindi necessario integrare l'app per le funzioni con tale rete virtuale. Questa configurazione è supportata in tutti i piani che supportano l'integrazione della rete virtuale.

[Altre informazioni sugli endpoint del servizio rete virtuale.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Limitazione dell'account di archiviazione a una rete virtuale

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Attualmente non è possibile usare alcuna restrizione di rete virtuale per questo account. Se si configura un endpoint del servizio di rete virtuale nell'account di archiviazione usato per l'app per le funzioni, l'app verrà interrotta.

[Altre informazioni sui requisiti dell'account di archiviazione.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Uso di riferimenti Key Vault 

I riferimenti Key Vault consentono di usare i segreti Azure Key Vault nell'applicazione funzioni di Azure senza richiedere modifiche al codice. Azure Key Vault è un servizio che fornisce una gestione centralizzata dei segreti, con controllo completo sui criteri di accesso e sulla cronologia di controllo.

Attualmente [Key Vault riferimenti](../app-service/app-service-key-vault-references.md) non funzioneranno se il Key Vault è protetto con gli endpoint di servizio. Per connettersi a un Key Vault usando l'integrazione della rete virtuale, è necessario chiamare Key Vault nel codice dell'applicazione.

## <a name="virtual-network-triggers-non-http"></a>Trigger della rete virtuale (non HTTP)

Attualmente, per usare trigger di funzione diversi da HTTP dall'interno di una rete virtuale, è necessario eseguire l'app per le funzioni in un piano di servizio app o in un ambiente del servizio app.

Si supponga, ad esempio, di voler configurare Azure Cosmos DB per accettare il traffico solo da una rete virtuale. È necessario distribuire l'app per le funzioni in un piano di servizio app che fornisce l'integrazione della rete virtuale con tale rete virtuale per configurare Azure Cosmos DB trigger da tale risorsa. Durante la fase di anteprima, la configurazione dell'integrazione della rete virtuale non consente al piano Premium di attivare tale risorsa Azure Cosmos DB.

Vedere [questo elenco per tutti i trigger non http](./functions-triggers-bindings.md#supported-bindings) per verificare il doppio degli elementi supportati.

## <a name="hybrid-connections"></a>connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità del servizio di inoltro di Azure che è possibile usare per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile usarlo per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni in esecuzione in tutti i piani a consumo.

Come usato in funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e qualsiasi applicazione, purché si acceda a una porta di ascolto TCP. La funzionalità Connessioni ibride non conosce né interessa il protocollo dell'applicazione o ciò che si sta accedendo. Fornisce solo l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio app per connessioni ibride](../app-service/app-service-hybrid-connections.md). Questi stessi passaggi di configurazione supportano funzioni di Azure.

## <a name="outbound-ip-restrictions"></a>Restrizioni IP in uscita

Le restrizioni IP in uscita sono disponibili solo per le funzioni distribuite in un ambiente del servizio app. È possibile configurare le restrizioni in uscita per la rete virtuale in cui è distribuita la ambiente del servizio app.

Quando si integra un'app per le funzioni in un piano Premium o un piano di servizio app con una rete virtuale, l'app può comunque effettuare chiamate in uscita a Internet.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su rete e funzioni di Azure:

* [Segui l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Leggi le domande frequenti sulle funzionalità di rete](./functions-networking-faq.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio app/funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e controllare con gli ambienti del servizio app](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando Connessioni ibride](../app-service/app-service-hybrid-connections.md)
