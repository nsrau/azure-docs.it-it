---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671537"
---
* I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
* Il ambiente del servizio app (ambiente del servizio app), che distribuisce nella VNet e supporta le app di piano tariffario isolate

Questo documento descrive la funzionalità di integrazione VNet, che può essere usata nelle app multi-tenant. Se l'app è in [ambiente del servizio app][ASEintro], si trova già in una VNet e non richiede l'uso della funzionalità di integrazione VNet per raggiungere le risorse nello stesso VNet. Per informazioni dettagliate su tutte le funzionalità di rete, vedere [funzionalità di rete del servizio app][Networkingfeatures]

L'integrazione di VNet consente all'app di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app dalla VNet. L'accesso al sito privato si riferisce all'app che rende accessibile solo da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. La funzionalità di integrazione VNet si comporta in modo diverso quando viene usata con reti virtuali nella stessa area e con reti virtuali in altre aree. La funzionalità di integrazione VNet presenta due varianti.

* Integrazione VNet a livello di area: quando ci si connette a Gestione risorse reti virtuali nella stessa area, è necessario disporre di una subnet dedicata nel VNet con cui si esegue l'integrazione. 
* Integrazione VNet necessaria per il gateway: quando ci si connette a reti virtuali in altre aree o a una VNet classica nella stessa area è necessario un gateway di rete virtuale di cui è stato effettuato il provisioning nel VNet di destinazione.

Funzionalità di integrazione di VNet:

* Richiedi un piano tariffario standard, Premium, PremiumV2 o Elastic Premium 
* supportare TCP e UDP
* usare le app del servizio app e le app per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios

L'integrazione VNet necessaria per il gateway consente solo l'accesso alle risorse nel VNet di destinazione o nelle reti connesse al VNet di destinazione con peering o VPN. L'integrazione VNet necessaria per il gateway non consente l'accesso alle risorse disponibili in connessioni ExpressRoute o funziona con gli endpoint di servizio. 

Indipendentemente dalla versione usata, l'integrazione di VNet consente all'app di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features