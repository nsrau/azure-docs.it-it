---
title: Domande frequenti sulla rete in Funzioni di AzureFrequently asked questions about networking in Azure Functions
description: Risposte ad alcune delle domande e scenari più comuni per la rete con Funzioni di Azure.Answers to some of the most common questions and scenarios for networking with Azure Functions.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409540"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Domande frequenti sulla rete in Funzioni di AzureFrequently asked questions about networking in Azure Functions

Questo articolo elenca le domande frequenti sulla rete in Funzioni di Azure.This article lists frequently asked questions about networking in Azure Functions. Per una panoramica più completa, vedere Opzioni di rete di [Funzioni](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Come si imposta un IP statico in Funzioni?

La distribuzione di una funzione in un ambiente del servizio app è attualmente l'unico modo per avere un indirizzo IP statico in ingresso e in uscita per la funzione. Per informazioni dettagliate sull'uso di un ambiente del servizio app, iniziare con l'articolo Creare e usare un servizio di [bilanciamento del carico interno con un](../app-service/environment/create-ilb-ase.md)ambiente del servizio app.

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Come posso limitare l'accesso a Internet alla mia funzione?

È possibile limitare l'accesso a Internet in due modi:You can restrict internet access in a couple of ways:

* [Restrizioni IP](../app-service/app-service-ip-restrictions.md): Limita il traffico in ingresso all'app per le funzioni in base all'intervallo IP.
    * In Restrizioni IP è inoltre possibile configurare [gli endpoint](../virtual-network/virtual-network-service-endpoints-overview.md)del servizio , che limitano la funzione per accettare solo il traffico in ingresso da una determinata rete virtuale.
* Rimozione di tutti i trigger HTTP. Per alcune applicazioni, è sufficiente evitare semplicemente i trigger HTTP e usare qualsiasi altra origine eventi per attivare la funzione.

Tenere presente che l'editor del portale di Azure richiede l'accesso diretto alla funzione in esecuzione. Qualsiasi modifica del codice tramite il portale di Azure richiederà il dispositivo in uso per esplorare il portale per la whitelist dell'indirizzo IP. Ma è ancora possibile utilizzare qualsiasi cosa sotto la scheda delle funzionalità della piattaforma con restrizioni di rete in atto.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Come è possibile limitare l'app per le funzioni a una rete virtuale?

È possibile limitare il traffico **in ingresso** per un'app per le funzioni a una rete virtuale utilizzando gli endpoint [del servizio.](./functions-networking-options.md#private-site-access) Questa configurazione consente ancora all'app per le funzioni di effettuare chiamate in uscita verso Internet.

L'unico modo per limitare completamente una funzione in modo che tutto il traffico scorra attraverso una rete virtuale consiste nell'utilizzare un ambiente del servizio app con bilanciamento del carico interno. Questa opzione distribuisce il sito in un'infrastruttura dedicata all'interno di una rete virtuale e invia tutti i trigger e il traffico attraverso la rete virtuale. 

Per informazioni dettagliate sull'uso di un ambiente del servizio app, iniziare con l'articolo Creare e usare un servizio di [bilanciamento del carico interno con un](../app-service/environment/create-ilb-ase.md)ambiente del servizio app.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Come è possibile accedere alle risorse in una rete virtuale da un'app per le funzioni?

È possibile accedere alle risorse in una rete virtuale da una funzione in esecuzione usando l'integrazione della rete virtuale. Per ulteriori informazioni, vedere [Integrazione](functions-networking-options.md#virtual-network-integration)della rete virtuale .

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Come si accede alle risorse protette dagli endpoint del servizio?

Utilizzando l'integrazione di rete virtuale è possibile accedere alle risorse protette da endpoint del servizio da una funzione in esecuzione. Per altre informazioni, vedere [Integrazione](functions-networking-options.md#virtual-network-integration)della rete virtuale .

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Come è possibile attivare una funzione da una risorsa in una rete virtuale?

È possibile consentire la chiamata di trigger HTTP da una rete virtuale tramite [gli endpoint del servizio.](./functions-networking-options.md#private-site-access) 

È anche possibile attivare una funzione da tutte le altre risorse in una rete virtuale distribuendo l'app per le funzioni in un piano Premium, un piano di servizio app o un ambiente del servizio app. Per altre informazioni, vedere Trigger di [rete virtuale non HTTP](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Come è possibile distribuire l'app per le funzioni in una rete virtuale?

La distribuzione in un ambiente del servizio app è l'unico modo per creare un'app per le funzioni che si trova in tutta una rete virtuale. Per informazioni dettagliate sull'uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app, iniziare con l'articolo Creare e usare un servizio di [bilanciamento del carico interno con un ambiente](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)del servizio app.

Per gli scenari in cui è necessario solo l'accesso unidirezionale alle risorse di rete virtuale o l'isolamento della rete meno completo, vedere la [panoramica](functions-networking-options.md)della rete di funzioni .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete e sulle funzioni: 

* [Seguire l'esercitazione su come iniziare a usare l'integrazione della rete virtuale](./functions-create-vnet.md)
* [Altre informazioni sulle opzioni di rete in Funzioni di AzureLearn more about the networking options in Azure Functions](./functions-networking-options.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio app e le funzioniLearn more about virtual network integration with App Service and Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in AzureLearn more about virtual networks in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilitare altre funzionalità di rete e il controllo con gli ambienti del servizio appEnable more networking features and control with App Service Environments](../app-service/environment/intro.md)
