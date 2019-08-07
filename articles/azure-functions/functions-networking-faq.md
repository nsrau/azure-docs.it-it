---
title: Domande frequenti sulla rete in funzioni di Azure
description: Risposte ad alcune domande e scenari più comuni per la rete con funzioni di Azure.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 6f363003dc24509bd0b80922d9e34560250cc7ed
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779296"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Domande frequenti sulla rete in funzioni di Azure

Questo articolo elenca le domande frequenti sulla rete in funzioni di Azure. Per una panoramica più completa, vedere [Opzioni di rete](functions-networking-options.md)per le funzioni.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Ricerca per categorie impostare un indirizzo IP statico in funzioni?

La distribuzione di una funzione in un ambiente del servizio app è attualmente l'unico modo per avere un IP in ingresso e in uscita statico per la funzione. Per informazioni dettagliate sull'uso di un ambiente del servizio app, iniziare con l'articolo [creare e usare un servizio di bilanciamento del carico interno con una ambiente del servizio app](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Ricerca per categorie limitare l'accesso a Internet per la funzione?

È possibile limitare l'accesso a Internet in due modi:

* [Restrizioni IP](../app-service/app-service-ip-restrictions.md): Limitare il traffico in ingresso all'app per le funzioni in base all'intervallo IP.
    * In restrizioni IP, è anche possibile configurare gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md), che limitano la funzione a accettare solo il traffico in ingresso da una determinata rete virtuale.
* Rimozione di tutti i trigger HTTP. Per alcune applicazioni, è sufficiente evitare semplicemente i trigger HTTP e usare qualsiasi altra origine evento per attivare la funzione.

Tenere presente che l'editor di portale di Azure richiede l'accesso diretto alla funzione in esecuzione. Tutte le modifiche al codice tramite il portale di Azure richiedono che il dispositivo usato per esplorare il portale abbia la relativa lista di indirizzi IP consentita. Tuttavia, è comunque possibile usare qualsiasi elemento nella scheda funzionalità della piattaforma con restrizioni di rete.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Ricerca per categorie limitare l'app per le funzioni a una rete virtuale?

È possibile limitare il traffico in **ingresso** per un'app per le funzioni a una rete virtuale usando gli [endpoint di servizio](./functions-networking-options.md#private-site-access). Questa configurazione consente ancora all'app per le funzioni di effettuare chiamate in uscita a Internet.

L'unico modo per limitare completamente una funzione in modo che tutto il traffico scorra attraverso una rete virtuale consiste nell'usare un ambiente del servizio app con carico bilanciato internamente. Questa opzione consente di distribuire il sito in un'infrastruttura dedicata all'interno di una rete virtuale e di inviare tutti i trigger e il traffico attraverso la rete virtuale. 

Per informazioni dettagliate sull'uso di un ambiente del servizio app, iniziare con l'articolo [creare e usare un servizio di bilanciamento del carico interno con una ambiente del servizio app](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Come è possibile accedere alle risorse in una rete virtuale da un'app per le funzioni?

È possibile accedere alle risorse in una rete virtuale da una funzione in esecuzione usando l'integrazione della rete virtuale. Per ulteriori informazioni, vedere [integrazione della rete virtuale](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Ricerca per categorie accedere alle risorse protette dagli endpoint di servizio?

Con l'integrazione della rete virtuale è possibile accedere alle risorse protette da endpoint servizio da una funzione in esecuzione. Per ulteriori informazioni, vedere [integrazione della rete virtuale](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Come è possibile attivare una funzione da una risorsa in una rete virtuale?

È possibile consentire la chiamata dei trigger HTTP da una rete virtuale usando gli [endpoint di servizio](./functions-networking-options.md#private-site-access). 

È anche possibile attivare una funzione da una risorsa in una rete virtuale distribuendo l'app per le funzioni in un ambiente del servizio app. Per informazioni dettagliate sull'uso di un ambiente del servizio app, vedere [creare e usare un servizio di bilanciamento del carico interno con una ambiente del servizio app](../app-service/environment/create-ilb-ase.md).

Il piano di servizio app e Premium supportano i trigger HTTP da una rete virtuale, ma solo un ambiente del servizio app supporta tutti gli altri tipi di trigger di funzione tramite una rete virtuale.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Come si distribuisce l'app per le funzioni in una rete virtuale?

La distribuzione in un ambiente del servizio app è l'unico modo per creare un'app per le funzioni interamente all'interno di una rete virtuale. Per informazioni dettagliate sull'uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app, iniziare con l'articolo [creare e usare un servizio di bilanciamento del carico interno con una ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Per gli scenari in cui è necessario solo l'accesso unidirezionale alle risorse della rete virtuale o un isolamento di rete meno completo, vedere la [Panoramica sulle funzioni](functions-networking-options.md)di rete.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su rete e funzioni: 

* [Segui l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Altre informazioni sulle opzioni di rete in funzioni di Azure](./functions-networking-options.md)
* [Scopri di più sull'integrazione della rete virtuale con il servizio app e le funzioni](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e controllare con gli ambienti del servizio app](../app-service/environment/intro.md)
