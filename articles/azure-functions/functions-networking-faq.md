---
title: Domande frequenti sulle reti in funzioni di Azure
description: Risposte ad alcune delle domande più comuni e scenari di rete con funzioni di Azure.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0d352d61a971e289a6286c2d948c3f0869ddd8d2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706393"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Domande frequenti sulle reti in funzioni di Azure

Questo articolo elenca le domande frequenti sulle reti in funzioni di Azure. Per una panoramica più completa, vedere [opzioni di rete funzioni](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Come impostare un indirizzo IP statico nelle funzioni?

La distribuzione di una funzione in un ambiente del servizio App è attualmente l'unico modo per avere un IP statico in ingresso e in uscita per la funzione. Per informazioni dettagliate sull'uso di un ambiente del servizio App, iniziare dall'articolo [creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Come si limita l'accesso a internet per la funzione?

È possibile limitare l'accesso a internet in due modi:

* [Le restrizioni IP](../app-service/app-service-ip-restrictions.md): Limitare il traffico in ingresso per app per le funzioni dall'intervallo di indirizzi IP.
    * Sotto le restrizioni IP, si è anche possibile configurare [gli endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md), che consente di limitare la funzione che accetta solo il traffico in ingresso da una determinata rete virtuale.
* Rimozione di tutti i trigger HTTP. Per alcune applicazioni, è sufficiente semplicemente evitare i trigger HTTP e utilizzare qualsiasi altra origine di eventi per attivare la funzione.

Tenere presente che l'editor del portale di Azure richiede l'accesso diretto alla funzione in esecuzione. Modifiche al codice tramite il portale di Azure richiederà il dispositivo in uso per esplorare il portale per avere relativo inserito nella whitelist IP. Ma è comunque possibile usare qualsiasi elemento della scheda di funzionalità della piattaforma con restrizioni di rete.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Come si limita l'app per le funzioni a una rete virtuale?

Si è in grado di limitare **in ingresso** traffic per un'app per le funzioni a una rete virtuale usando [endpoint di servizio](./functions-networking-options.md#private-site-access). Questa configurazione consente ancora l'app per le funzioni effettuare chiamate in uscita a internet.

L'unico modo per limitare completamente una funzione in modo che tutto il traffico passa attraverso una rete virtuale consiste nell'utilizzare un ambiente del servizio App internamente con bilanciamento del carico. Questa opzione distribuisce il sito in un'infrastruttura dedicata all'interno di una rete virtuale e invia tutti i trigger e il traffico attraverso la rete virtuale. 

Per informazioni dettagliate sull'uso di un ambiente del servizio App, iniziare dall'articolo [creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Come è possibile accedere alle risorse in una rete virtuale da un'app per le funzioni?

È possibile accedere alle risorse in una rete virtuale da una funzione in esecuzione tramite l'integrazione rete virtuale. Per altre informazioni, vedere [integrazione rete virtuale](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Come posso accedere alle risorse protette tramite endpoint di servizio?

Tramite l'integrazione rete virtuale (attualmente in anteprima), è possibile accedere alle risorse di endpoint di servizio protetto da una funzione in esecuzione. Per altre informazioni, vedere [anteprima l'integrazione rete virtuale](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Come è possibile attivare una funzione da una risorsa in una rete virtuale?

Si è in grado di consentire i trigger HTTP per essere chiamato da una rete virtuale usando [gli endpoint di servizio](./functions-networking-options.md#private-site-access). 

È anche possibile attivare una funzione da una risorsa in una rete virtuale con la distribuzione di app per le funzioni in un ambiente del servizio App. Per informazioni dettagliate sull'uso di un ambiente del servizio App, vedere [creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App](../app-service/environment/create-ilb-ase.md).

I servizio App e Premium piano supporto i trigger HTTP da una rete virtuale, ma solo un ambiente del servizio App supportano tutti gli altri tipi di trigger di funzione tramite una rete virtuale.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Come è possibile distribuire l'app per le funzioni in una rete virtuale?

Distribuzione in un ambiente del servizio App è l'unico modo per creare un'app per le funzioni che è completamente all'interno di una rete virtuale. Per informazioni dettagliate sull'uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App, iniziare dall'articolo [creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio App](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Per scenari in cui è necessario solo unidirezionale accedere a risorse di rete virtuale oppure minore isolamento rete completa, vedere la [Panoramica di rete funzioni](functions-networking-options.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di rete e le funzioni: 

* [Seguire l'esercitazione sulle operazioni iniziali con l'integrazione rete virtuale](./functions-create-vnet.md)
* [Altre informazioni sulle opzioni di rete in funzioni di Azure](./functions-networking-options.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio App e funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilita altre funzionalità di rete e controllo con ambienti del servizio App](../app-service/environment/intro.md)
