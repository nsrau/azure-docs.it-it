---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419525"
---
* I sistemi multi-tenant che supportano l'intera gamma di piani tariffari ad eccezione di Isolato.
* Ambiente del servizio app, che viene distribuito nella rete virtuale e supporta le app del piano tariffario isolato.

La funzionalità di integrazione della rete virtuale viene usata nelle app multi-tenant. Se l'app si trova [nell'ambiente][ASEintro]del servizio app, è già in una rete virtuale e non richiede l'uso della funzionalità di integrazione della rete virtuale per raggiungere le risorse nella stessa rete virtuale. Per ulteriori informazioni su tutte le funzionalità di rete, vedere Funzionalità di rete del [servizio app][Networkingfeatures].

L'integrazione della rete virtuale consente all'app di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app dalla rete virtuale. L'accesso al sito privato si riferisce al rendere un'app accessibile solo da una rete privata, ad esempio dall'interno di una rete virtuale di Azure.Private site access refers to making an app accessible only from a private network, such as from within an Azure virtual network. L'integrazione della rete virtuale viene usata solo per effettuare chiamate in uscita dall'app nella rete virtuale. La funzionalità di integrazione della rete virtuale si comporta in modo diverso quando viene usata con reti virtuali nella stessa area e con reti virtuali in altre aree. La funzionalità di integrazione della rete virtuale presenta due varianti:The VNet Integration feature has two variations:

* **Integrazione della rete virtuale regionale:** quando ci si connette alle reti virtuali di Azure Resource Manager nella stessa area, è necessario disporre di una subnet dedicata nella rete virtuale con cui si esegue l'integrazione.
* **Integrazione della rete virtuale richiesta**dal gateway: quando ci si connette a reti virtuali in altre aree o a una rete virtuale classica nella stessa area, è necessario un gateway di rete virtuale di Azure di cui è stato eseguito il provisioning nella rete virtuale di destinazione.

Le funzionalità di integrazione della rete virtuale:

* Richiedere un piano tariffario Standard, Premium, PremiumV2 o Elastic Premium.
* Supporta TCP e UDP.
* Usare le app del servizio app di Azure e le app per le funzioni.

Ci sono alcune cose che l'integrazione di VNet non supporta, come:

* Montaggio di un'unità.
* Integrazione con Active Directory.
* Netbios.

L'integrazione della rete virtuale richiesta dal gateway consente di accedere alle risorse solo nella rete virtuale di destinazione o nelle reti connesse alla rete virtuale di destinazione con peering o VPN. L'integrazione della rete virtuale richiesta dal gateway non consente l'accesso alle risorse disponibili nelle connessioni ExpressRoute di Azure o funziona con gli endpoint del servizio.

Indipendentemente dalla versione usata, l'integrazione della rete virtuale consente all'app di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app dalla rete virtuale. L'accesso al sito privato si riferisce a rendere l'app accessibile solo da una rete privata, ad esempio da una rete virtuale di Azure.Private site access refers to making your app accessible only from a private network, such as from within an Azure virtual network. L'integrazione della rete virtuale è solo per effettuare chiamate in uscita dall'app nella rete virtuale.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
