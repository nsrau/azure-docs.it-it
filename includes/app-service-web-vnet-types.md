---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 481bd4f50eb527bcad2ba79b5ba4b9df4b872bfc
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739910"
---
* I sistemi multi-tenant che supportano l'intera gamma di piani tariffari eccetto isolated.
* Il ambiente del servizio app, che distribuisce nella VNet e supporta le app di piano tariffario isolate.

La funzionalità di integrazione VNet viene usata nelle app multi-tenant. Se l'app è nell'[ambiente del servizio App][ASEintro], è già in una rete virtuale e non richiede l'uso della funzionalità Integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale. Per altre informazioni su tutte le funzionalità di rete, vedere [funzionalità di rete del servizio app][Networkingfeatures].

L'integrazione di VNet consente all'app di accedere alle risorse nel VNet, ma non concede l'accesso privato in ingresso all'app dal VNet. L'accesso al sito privato fa riferimento a rendere un'app accessibile solo da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet viene usata solo per eseguire chiamate in uscita dall'app in VNet. La funzionalità di integrazione VNet si comporta in modo diverso quando viene usata con VNet nella stessa area e con VNet in altre aree. La funzionalità di integrazione VNet presenta due varianti:

* **Integrazione VNet a livello**di area: quando ci si connette a Azure Resource Manager reti virtuali nella stessa area, è necessario disporre di una subnet dedicata nel VNet con cui si esegue l'integrazione.
* **Gateway: integrazione VNet necessaria**: quando ci si connette a VNet in altre aree o a una rete virtuale classica nella stessa area, è necessario un gateway di rete virtuale di Azure di cui è stato effettuato il provisioning nel VNet di destinazione.

Funzionalità di integrazione di VNet:

* Richiedi un piano tariffario standard, Premium, PremiumV2, PremiumV3 o Elastic Premium.
* Supportare TCP e UDP.
* Usare app Azure app di servizio e le app per le funzioni.

Ci sono alcuni aspetti che l'integrazione VNet non supporta, ad esempio:

* Montaggio di un'unità.
* Active Directory l'integrazione.
* NetBIOS.

Gateway: l'integrazione VNet necessaria consente di accedere alle risorse solo nel VNet di destinazione o nelle reti connesse al VNet di destinazione con peering o VPN. Gateway: l'integrazione VNet necessaria non consente l'accesso alle risorse disponibili tra le connessioni ExpressRoute di Azure o funziona con gli endpoint di servizio.

Indipendentemente dalla versione usata, l'integrazione di VNet consente all'app di accedere alle risorse nel VNet, ma non concede l'accesso privato in ingresso all'app dal VNet. L'accesso al sito privato fa riferimento a rendere l'app accessibile solo da una rete privata, ad esempio dall'interno di una VNet di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
