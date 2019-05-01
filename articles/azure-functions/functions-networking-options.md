---
title: Opzioni di rete di funzioni di Azure
description: Una panoramica di tutte le opzioni di rete disponibili in funzioni di Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 49f89d39b3b917ec6357b241d7c413c2790eca25
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575611"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di funzioni di Azure

Questo articolo descrive le funzionalità di rete disponibile tra le opzioni di hosting per funzioni di Azure. Tutte le opzioni di rete seguenti forniscono capacità di accedere alle risorse senza usare indirizzi instradabili a internet, o limitare l'accesso a internet a un'app per le funzioni. 

I modelli di hosting hanno diversi livelli di isolamento di rete disponibile. Scegliere quello corretto consentono di soddisfare i requisiti di isolamento rete.

È possibile ospitare l'App per le funzioni in due modi:

* È disponibile un set di opzioni del piano di esecuzione in un'infrastruttura multi-tenant, con vari livelli di connettività di rete virtuale e le opzioni di scalabilità:
    * Il [piano a consumo](functions-scale.md#consumption-plan), che ridimensiona dinamicamente in risposta al carico e offre opzioni di isolamento rete minime.
    * Il [Premium-piano](functions-scale.md#premium-plan-public-preview), che anche offre una scalabilità in modo dinamico, garantendo l'isolamento rete più completa.
    * Azure [piano di servizio App](functions-scale.md#app-service-plan), che funziona su una scala fissa che offre isolamento di rete simili al piano Premium.
* È possibile eseguire le funzioni in un' [ambiente del servizio App](../app-service/environment/intro.md). Questo metodo distribuisce la funzione nella rete virtuale e offre isolamento e controllo di rete completa.

## <a name="matrix-of-networking-features"></a>Matrice delle funzionalità di rete

|                |[Piano a consumo](functions-scale.md#consumption-plan)|[Piano Premium (anteprima)](functions-scale.md#premium-plan-public-preview)|[Piano di servizio app](functions-scale.md#app-service-plan)|[Ambiente del servizio app](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrizioni di IP in ingresso](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Restrizioni di IP in uscita](#private-site-access)|❌No| ❌No|❌No|✅Yes|
|[Integrazione della rete virtuale](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Visualizzare in anteprima l'integrazione rete virtuale (Azure ExpressRoute e gli endpoint di servizio in uscita)](#preview-version-of-virtual-network-integration)|❌No|✅Yes|✅Yes|✅Yes|
|[Connessioni ibride](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[accesso al sito privato](#private-site-access)|❌No| ✅Yes|✅Yes|✅Yes|

## <a name="inbound-ip-restrictions"></a>Restrizioni di IP in ingresso

È possibile usare le restrizioni IP per definire un elenco ordinato in base alla priorità di indirizzi IP che sono consentito/negato l'accesso all'app. Questo elenco può includere gli indirizzi IPv4 e IPv6. Quando è presente una o più voci, è implicita "deny_all" presente alla fine dell'elenco. Le restrizioni IP funzionano con tutte le opzioni di hosting di funzione.

> [!NOTE]
> Per usare l'editor del portale di Azure, il portale deve essere in grado di accedere direttamente alle app per le funzioni in esecuzione. Inoltre, il dispositivo che si sta utilizzando per accedere al portale deve avere relativo inserito nella whitelist IP. Con restrizioni di rete, è comunque possibile accedere tutte le funzionalità nel **funzionalità della piattaforma** scheda.

Per altre informazioni, vedere [restrizioni di servizio App di Azure l'accesso statico](../app-service/app-service-ip-restrictions.md).

## <a name="outbound-ip-restrictions"></a>Restrizioni di IP in uscita

Restrizioni di IP in uscita sono disponibili solo per le funzioni distribuite in un ambiente del servizio App. È possibile configurare restrizioni in uscita per la rete virtuale in cui viene distribuito l'ambiente del servizio App.

## <a name="virtual-network-integration"></a>Integrazione della rete virtuale

Integrazione della rete virtuale consente all'app di funzione accedere alle risorse all'interno di una rete virtuale. Questa funzionalità è disponibile nel piano Premium e piano di servizio App. Se l'app è in un ambiente del servizio App, è già in una rete virtuale e non richiede l'utilizzo dell'integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale.

Integrazione della rete virtuale consente all'app di funzione accedere alle risorse nella rete virtuale ma non concede [l'accesso al sito privato](#private-site-access) app per le funzioni dalla rete virtuale.

È possibile utilizzare l'integrazione della rete virtuale per abilitare l'accesso dall'App per i database e servizi web in esecuzione in una rete virtuale. Con l'integrazione rete virtuale, non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale. È possibile usare gli indirizzi instradabili privati, non internet.

La versione disponibile a livello generale di integrazione della rete virtuale si basa su un gateway VPN per App per le funzioni di connettersi a una rete virtuale. È disponibile nelle funzioni ospitate in un piano di servizio App. Per informazioni su come configurare questa funzionalità, vedere [integrare un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Versione di anteprima di integrazione della rete virtuale

Una nuova versione della funzionalità integrazione rete virtuale è in anteprima. Non dipende VPN point-to-site. Supporta l'accesso alle risorse in ExpressRoute o gli endpoint di servizio. È disponibile nel piano Premium e nei piani di servizio App ridimensionato in base a PremiumV2.

Ecco alcune caratteristiche di questa versione:

* Non è necessario un gateway per usarlo.
* Puoi accedere a risorse sulle connessioni ExpressRoute senza alcuna configurazione aggiuntiva oltre all'integrazione con la rete virtuale connessa a ExpressRoute.
* È possibile usare le risorse di endpoint di servizio protetto dall'esecuzione di funzioni. A tale scopo, abilitare gli endpoint di servizio nella subnet usata per l'integrazione rete virtuale.
* Non è possibile configurare i trigger per l'uso del servizio endpoint-risorse protette. 
* Sia l'app per le funzioni e la rete virtuale deve essere nella stessa area.
* La nuova funzionalità richiede una subnet non usata nella rete virtuale che è stato distribuito tramite Azure Resource Manager.
* I carichi di lavoro di produzione non sono supportati mentre la funzionalità è disponibile in anteprima.
* Le tabelle di route e il peering globale non sono ancora disponibili con la funzionalità.
* Un indirizzo viene usato per ogni istanza potenziale di un'app per le funzioni. Poiché non è possibile modificare le dimensioni della subnet dopo l'assegnazione, usare una subnet in grado di supportare facilmente le dimensioni della scala massima. Ad esempio, per supportare un piano Premium che può essere ridimensionato alle 80 istanze, è consigliabile un `/25` subnet che fornisce gli indirizzi host 126.

Per altre informazioni sull'uso di versione di anteprima dell'integrazione della rete virtuale, vedere [integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità di inoltro di Azure che è possibile usare per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è possibile usarlo per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni in esecuzione in un [piano di servizio App](functions-scale.md#app-service-plan) e un [ambiente del servizio App](../app-service/environment/intro.md).

Quando usata in funzioni di Azure, ogni connessione ibrida è correlata a una singola combinazione di host e la porta TCP. Ciò significa che endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e qualsiasi altra applicazione, purché si accede a una porta TCP in ascolto. La funzionalità connessioni ibride non conosce o non occupa che cos'è il protocollo dell'applicazione o sta eseguendo l'accesso. Fornisce semplicemente l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio App per le connessioni ibride](../app-service/app-service-hybrid-connections.md), che supporta le funzioni in un piano di servizio App.

## <a name="private-site-access"></a>Accesso al sito privato

Accesso privato s'intende per rendere l'app accessibile solo da una rete privata, ad esempio all'interno di una rete virtuale di Azure. 
* Accesso al sito privato è disponibile nel servizio App delle Premium pianificare quando **gli endpoint di servizio** configurati. Per altre informazioni, vedere [endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md)
    * Tenere presente che con gli endpoint di servizio, la funzione ancora ha accesso completo in uscita a internet, anche con la configurazione dell'integrazione rete virtuale.
* Accesso al sito privato è disponibile solo con un ambiente del servizio App configurato con un servizio di bilanciamento del carico interno (ILB). Per altre informazioni, vedere [creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App](../app-service/environment/create-ilb-ase.md).

Esistono molti modi per accedere alle risorse di rete virtuale di altre opzioni di hosting. Tuttavia, un ambiente del servizio App è l'unico modo per consentire i trigger per una funzione essere eseguita su una rete virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzioni di Azure e rete: 

* [Seguire l'esercitazione sulle operazioni iniziali con l'integrazione rete virtuale](./functions-create-vnet.md)
* [Leggere le funzioni di domande frequenti sulla rete](./functions-networking-faq.md)
* [Altre informazioni sull'integrazione della rete virtuale con servizio App/funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilita altre funzionalità di rete e controllo con ambienti del servizio App](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando connessioni ibride](../app-service/app-service-hybrid-connections.md)
