---
title: Domande frequenti sulle reti in funzioni di Azure
description: Risposte ad alcune delle domande più comuni e scenari di rete con funzioni di Azure.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548640"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Domande frequenti sulle reti in funzioni di Azure

Seguito è riportato un elenco delle domande frequenti di rete. Per una panoramica più completa, leggere il [funzioni documento opzioni rete](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Come impostare un indirizzo IP statico nelle funzioni?

La distribuzione di una funzione in un ambiente del servizio App (ASE) è attualmente l'unico modo per avere un IP statico in ingresso e in uscita per la funzione. Per informazioni dettagliate sull'uso di un ambiente del servizio App, iniziare dall'articolo di seguito: [Creazione e utilizzo di un ambiente del servizio App ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Come si limita l'accesso a internet per la funzione?

È possibile limitare l'accesso a internet in diversi modi, elencati di seguito.

* [Le restrizioni IP](../app-service/app-service-ip-restrictions.md): limitare il traffico in ingresso per app per le funzioni dall'intervallo di indirizzi IP.
* Rimuovere tutti i trigger HTTP. Per alcune applicazioni, è sufficiente semplicemente evitare i trigger HTTP e utilizzare qualsiasi altra origine di eventi per attivare la funzione.

L'aspetto più importante in questo caso consiste nel tenere presente che l'editor del portale di Azure richiede l'accesso diretto alla funzione in esecuzione da usare. Modifiche al codice tramite il portale di Azure richiederà il dispositivo in uso per esplorare il portale per avere relativo inserito nella whitelist IP. È possibile comunque, tuttavia, usare solo sotto la scheda di funzionalità della piattaforma con restrizioni di rete.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Come si limita l'app per le funzioni a una rete virtuale?

L'unico modo per limitare completamente una funzione in modo che tutto il traffico passa attraverso una rete virtuale deve usare un internamente con bilanciamento del carico ambiente del servizio App (ASE) (ILB). Questa opzione distribuisce il sito in un'infrastruttura dedicata in una rete virtuale e invia tutti i trigger e il traffico attraverso la rete virtuale. Per informazioni dettagliate sull'uso di un ambiente del servizio App, iniziare dall'articolo di seguito: [Creazione e utilizzo di un ambiente del servizio App ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Come è possibile accedere alle risorse in una rete virtuale da un'app per le funzioni?

È possibile accedere alle risorse in una rete virtuale da una funzione in esecuzione usando l'integrazione rete virtuale. Per altre informazioni, vedere [integrazione rete virtuale](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Come posso accedere alle risorse protette tramite endpoint di servizio?

Usa la nuova integrazione rete virtuale (attualmente in anteprima), è possibile accedere a Endpoint di servizio protetto alle risorse da una funzione in esecuzione. Per altre informazioni, vedere [anteprima l'integrazione rete virtuale](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Come è possibile attivare una funzione da una risorsa in una rete virtuale?

È possibile attivare una funzione da una risorsa in una rete virtuale solo con la distribuzione di app per le funzioni in un ambiente del servizio App. Per informazioni dettagliate sull'uso di un ambiente del servizio App, vedere [creazione e uso di un ambiente del servizio App ILB](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Come è possibile distribuire l'app per le funzioni in una rete virtuale?

Distribuzione in un ambiente del servizio App è l'unico modo per creare un'app per le funzioni che è completamente all'interno di una rete virtuale per informazioni dettagliate sull'uso di un ambiente del servizio App ILB, iniziare dall'articolo di seguito: [Creazione e utilizzo di un ambiente del servizio App ILB](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Per scenari in cui è necessario solo unidirezionale accesso alle risorse di rete virtuale o meno l'isolamento rete completa, vedere la [Panoramica di rete funzioni](functions-networking-options.md).

## <a name="next-steps"></a>Fasi successive

Per altre informazioni sulla rete e le funzioni: 

* [Seguire la Guida introduttiva di integrazione rete virtuale](./functions-create-vnet.md)
* [Altre informazioni sulle opzioni di rete in questo caso le funzioni](./functions-networking-options.md)
* [Altre informazioni sull'integrazione della rete virtuale con il servizio/le funzioni app qui](../app-service/web-sites-integrate-with-vnet.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilita altre funzionalità di rete e controllo con ambienti del servizio App](../app-service/environment/intro.md)
