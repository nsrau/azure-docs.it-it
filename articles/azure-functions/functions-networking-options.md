---
title: Opzioni di rete di funzioni di Azure
description: Una panoramica di tutte le opzioni di rete disponibili in funzioni di Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 8bbc44e7af68f005f30fff143741bc4bfe0adcf2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896458"
---
# <a name="azure-functions-networking-options"></a>Opzioni di rete di funzioni di Azure

Questo documento descrive la suite di funzionalità di rete disponibile tra le funzioni di Azure le opzioni di hosting. Tutte le opzioni di rete seguenti forniscono capacità di accedere alle risorse senza usare indirizzi instradabili a internet, o limitare l'accesso a internet a un'App per le funzioni. Tutti i modelli di hosting hanno diversi livelli di isolamento della rete disponibile e scegliere quello corretto verrà consentono di soddisfare i requisiti di isolamento rete.

App per le funzioni possono essere ospitate in modi diversi.

* Esistono una serie di opzioni del piano di esecuzione sull'infrastruttura multi-tenant, con vari livelli di connettività tra reti VIRTUALI e le opzioni di scalabilità.
    1. Il piano a consumo, che ridimensiona dinamicamente in risposta al carico e offre opzioni di isolamento rete minime.
    1. Il piano Premium, che adatti anche in modo dinamico, garantendo l'isolamento rete più completa.
    1. Il piano di servizio App, che funziona su una scala fissa che offre isolamento di rete simili al piano Premium.
* Funzioni possono anche essere eseguite in un servizio ambiente App (ASE) che distribuisce la funzione in una rete virtuale e offre isolamento e controllo di rete completo.

## <a name="networking-feature-matrix"></a>Matrice di funzionalità di rete

|                |[Piano a consumo](functions-scale.md#consumption-plan)|⚠ [Piano Premium](functions-scale.md##premium-plan-public-preview)|[Piano di servizio app](functions-scale.md#app-service-plan)|[Ambiente del servizio app](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Restrizioni indirizzi IP in ingresso**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Integrazione rete virtuale**](#vnet-integration)|❌No|⚠ Sì|✅Yes|✅Yes|
|[**Integrazione rete virtuale di anteprima (Express Route e gli endpoint di servizio)**](#preview-vnet-integration)|❌No|⚠ Sì|⚠ Sì|✅Yes|
|[**connessioni ibride**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Accesso al sito privato**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Funzionalità di anteprima e non per uso in produzione

## <a name="inbound-ip-restrictions"></a>Restrizioni indirizzi IP in ingresso

Le restrizioni IP consentono di definire un elenco consenti/nega basato sulle priorità di indirizzi IP a cui è consentito accedere all'app. L'elenco consenti può includere gli indirizzi IPv4 e IPv6. In presenza di una o più voci, alla fine dell'elenco è presente un nega tutto implicito. La funzionalità restrizioni IP può essere usata con funzione di tutte le opzioni di hosting.

> ! [IMPORTANTE] Per poter usare l'editor del portale di Azure, il portale deve essere in grado di accedere direttamente alle app per le funzioni in esecuzione e il dispositivo che si usa per accedere al portale deve essere relativo inserito nella whitelist IP. Con restrizioni di rete, è comunque possibile accedere le caratteristiche di **funzionalità della piattaforma** scheda.

[Altre informazioni sono disponibili qui](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Integrazione rete virtuale

Integrazione rete virtuale consente all'app di funzione accedere alle risorse all'interno di una rete virtuale. Integrazione rete virtuale è disponibile nel piano di servizio App sia piano Premium. Se l'app è in un ambiente del servizio App, è già in una rete virtuale e non richiede l'uso della funzionalità integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale.

Integrazione rete virtuale consente all'app di funzione accedere alle risorse nella rete virtuale ma non concede [l'accesso al sito privato](#private-site-access) app per le funzioni dalla rete virtuale.

Integrazione rete virtuale viene spesso usata per consentire l'accesso dalle app ai database e ai servizi Web in esecuzione nella rete virtuale. Con Integrazione rete virtuale non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale ma è possibile usare gli indirizzi instradabili non Internet privati.

La versione disponibile a livello generale di integrazione rete virtuale si basa su un gateway VPN per App per le funzioni di connettersi a una rete virtuale. È disponibile nelle funzioni ospitate in un piano di servizio app. Per informazioni su come configurare questa funzionalità, vedere la [documento di servizio App per la stessa funzionalità](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Integrazione rete virtuale di anteprima

È disponibile in anteprima una nuova versione della funzionalità Integrazione rete virtuale. Non dipende dalla VPN da punto a sito e supporta anche l'accesso alle risorse in ExpressRoute o negli endpoint servizio. Questa funzionalità è disponibile nel piano Premium e nei piani di servizio App ridimensionato in base a PremiumV2.

La nuova versione dell'integrazione rete virtuale, che è attualmente in anteprima, offre i vantaggi seguenti:

* Per usare la nuova funzionalità Integrazione rete virtuale non è richiesto alcun gateway
* È possibile accedere alle risorse attraverso le connessioni ExpressRoute senza alcuna configurazione aggiuntiva oltre all'integrazione con la rete virtuale connessa a ExpressRoute.
* È possibile usare l'Endpoint del servizio a risorse protette dall'esecuzione di funzioni. A tale scopo, abilitare gli endpoint servizio nella subnet usata per Integrazione rete virtuale.
* Non è possibile configurare i trigger per l'uso di Endpoint del servizio utilizzando la nuova funzionalità integrazione rete virtuale alle risorse protette. 
* Sia l'app per le funzioni e la rete virtuale deve essere nella stessa area.
* La nuova funzionalità richiede una subnet inutilizzata nella rete virtuale di Resource Manager.
* I carichi di lavoro di produzione non sono supportate nella nuova versione dell'integrazione rete virtuale mentre è in anteprima.
* Le tabelle di route e il peering globale non sono ancora disponibili con la nuova funzionalità Integrazione rete virtuale.
* Un indirizzo viene usato per ogni istanza dell'app funzioni potenziali. Perché le dimensioni della subnet non possono essere modificata dopo l'assegnazione, usare una subnet in grado di supportare facilmente le dimensioni della scala massima. Ad esempio, per supportare un piano Premium che può essere ridimensionato alle 80 istanze, è consigliabile un `/25` subnet che fornisce gli indirizzi host 126.

Per altre informazioni sull'utilizzo dell'integrazione rete virtuale di anteprima, vedere [integrare un'app per le funzioni con una rete virtuale di Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>connessioni ibride

[Connessioni ibride](../service-bus-relay/relay-hybrid-connections-protocol.md) è una funzionalità di inoltro di Azure che può essere utilizzato per accedere alle risorse dell'applicazione in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non è utilizzabile per accedere all'applicazione. Connessioni ibride è disponibile per le funzioni in esecuzione in un [piano di servizio App](functions-scale.md#app-service-plan) e un [ambiente del servizio App](../app-service/environment/intro.md).

Quando utilizzata nelle funzioni, ogni connessione ibrida è correlata a una singola combinazione di host e la porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e in qualsiasi applicazione, a condizione che si acceda a una porta TCP in ascolto. La funzionalità Connessioni ibride non conosce né deve conoscere quale sia il protocollo dell'applicazione o a quale risorsa l'utente stia accedendo, in quanto si limita a fornire l'accesso alla rete.

Per altre informazioni, vedere la [documentazione del servizio App per le connessioni ibride](../app-service/app-service-hybrid-connections.md), che supporta le funzioni e App Web.

## <a name="private-site-access"></a>Accesso al sito privato

Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'accesso privato al sito è disponibile solo con un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno (ILB). Per informazioni dettagliate sull'uso di un ambiente del servizio App ILB, vedere [creazione e uso di un ambiente del servizio App ILB](../app-service/environment/create-ilb-ase.md).

Esistono molti modi per accedere alle risorse di rete virtuale di altre opzioni di hosting, ma un ambiente del servizio App è l'unico modo per consentire i trigger per una funzione essere eseguita su una rete virtuale.
