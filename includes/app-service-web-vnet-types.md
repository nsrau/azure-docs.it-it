---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671537"
---
* I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
* L'ambiente del servizio app (ASE), che viene distribuito nella rete virtuale e supporta le app del piano tariffario isolateThe Service Environment (ASE), which deploys into your VNet and supports Isolated pricing plan apps

Questo documento passa attraverso la funzionalità di integrazione della rete virtuale, che viene utilizzata nelle app multi-tenant. Se l'app è nell'[ambiente del servizio App][ASEintro], è già in una rete virtuale e non richiede l'uso della funzionalità Integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale. Per informazioni dettagliate su tutte le funzionalità di rete, leggere [App Service networking features][Networkingfeatures]

L'integrazione della rete virtuale consente all'app di accedere alle risorse nella rete virtuale, ma non di concedere l'accesso privato in ingresso all'app dalla rete virtuale. L'accesso al sito privato si riferisce a rendere l'app accessibile solo da una rete privata, ad esempio dall'interno di una rete virtuale di Azure.Private site access refers to making app only accessible from a private network such as from within an Azure virtual network. L'integrazione della rete virtuale è solo per effettuare chiamate in uscita dall'app nella rete virtuale. La funzionalità di integrazione della rete virtuale si comporta in modo diverso se utilizzata con le reti virtuali nella stessa area e con le reti virtuali in altre aree. La funzionalità di integrazione della rete virtuale ha due varianti.

* Integrazione della rete virtuale regionale: quando ci si connette alle reti virtuali di Resource Manager nella stessa area, è necessario disporre di una subnet dedicata nella rete virtuale con cui si esegue l'integrazione. 
* Integrazione della rete virtuale necessaria per il gateway: quando ci si connette alle reti virtuali in altre aree o a una rete virtuale classica nella stessa area, è necessario un gateway di rete virtuale di cui è stato eseguito il provisioning nella rete virtuale di destinazione.

Le funzionalità di integrazione della rete virtuale:

* richiedono un piano tariffario Standard, Premium, PremiumV2 o Elastic Premium 
* supporta TCP e UDP
* lavorare con le app del servizio app e le app per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios

L'integrazione della rete virtuale richiesta dal gateway fornisce solo l'accesso alle risorse nella rete virtuale di destinazione o nelle reti connesse alla rete virtuale di destinazione con peering o VPN. L'integrazione della rete virtuale richiesta dal gateway non consente l'accesso alle risorse disponibili tra le connessioni ExpressRoute o lavora con gli endpoint del servizio. 

Indipendentemente dalla versione usata, l'integrazione della rete virtuale consente all'app di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione della rete virtuale è solo per effettuare chiamate in uscita dall'app nella rete virtuale. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features