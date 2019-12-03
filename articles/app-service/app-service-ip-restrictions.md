---
title: Limitazione dell'accesso per gli indirizzi IP
description: Informazioni su come proteggere l'app nel servizio app Azure inserendo in modo esplicito gli indirizzi IP o gli intervalli di indirizzi del client.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 64ce74c84f8f69e72510be76a1309e1a5ea42f2f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672176"
---
# <a name="azure-app-service-access-restrictions"></a>Restrizioni di accesso al servizio app Azure #

Le restrizioni di accesso consentono di definire un elenco di indirizzi consentiti/negati con priorità che controlla l'accesso alla rete all'app. L'elenco può includere indirizzi IP o subnet di rete virtuale di Azure. Quando sono presenti una o più voci, esiste una "Deny All" implicita presente alla fine dell'elenco.

La funzionalità restrizioni di accesso funziona con tutti i carichi di lavoro ospitati del servizio app, tra cui; app Web, app per le API, app Linux, app contenitore Linux e funzioni.

Quando viene effettuata una richiesta all'app, l'indirizzo FROM viene valutato in base alle regole degli indirizzi IP nell'elenco delle restrizioni di accesso. Se l'indirizzo FROM si trova in una subnet configurata con gli endpoint di servizio di Microsoft. Web, la subnet di origine viene confrontata con le regole della rete virtuale nell'elenco delle restrizioni di accesso. Se all'indirizzo non viene consentito l'accesso in base alle regole nell'elenco, il servizio replica con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La funzionalità restrizioni di accesso è implementata nei ruoli front-end del servizio app, che sono upstream degli host di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni di accesso sono gli ACL di rete.

La possibilità di limitare l'accesso all'app Web da una rete virtuale di Azure (VNet) viene definita [endpoint di servizio][serviceendpoints]. Gli endpoint di servizio consentono di limitare l'accesso a un servizio multi-tenant dalle subnet selezionate. È necessario abilitarla sia sul lato rete che sul servizio con cui è abilitata. Non funziona per limitare il traffico alle app ospitate in un ambiente del servizio app.  Se ci si trova in una ambiente del servizio app, è possibile controllare l'accesso all'app con le regole degli indirizzi IP.

![flusso restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Aggiunta e modifica di regole di restrizione di accesso nel portale ##

Per aggiungere una regola di restrizione di accesso all'app, usare il menu per aprire le **restrizioni di accesso** alla **rete**>e fare clic su **configura restrizioni di accesso**

![Opzioni di connettività di rete del servizio app](media/app-service-ip-restrictions/access-restrictions.png)  

Dall'interfaccia utente delle restrizioni di accesso, è possibile esaminare l'elenco delle regole di restrizione di accesso definite per l'app.

![elencare le restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-browse.png)

L'elenco mostrerà tutte le restrizioni correnti nell'app. Se si dispone di una restrizione VNet per l'app, la tabella indicherà se gli endpoint di servizio sono abilitati per Microsoft. Web. Quando non sono presenti restrizioni definite per l'app, l'app sarà accessibile da qualsiasi luogo.  

## <a name="adding-ip-address-rules"></a>Aggiunta di regole di indirizzi IP

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizione dell'accesso. Dopo l'aggiunta una regola diventa effettiva immediatamente. Le regole vengono applicate in base alle priorità dal numero più basso al più alto. È presente un nega tutto implicito anche dopo l'aggiunta di una singola regola.

Quando si crea una regola, è necessario selezionare Consenti/Nega e anche il tipo di regola. È inoltre necessario specificare il valore di priorità e gli elementi a cui si sta limitando l'accesso.  Facoltativamente, è possibile aggiungere un nome e una descrizione alla regola.  

![aggiungere una regola di restrizione dell'accesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Per impostare una regola basata sull'indirizzo IP, selezionare un tipo di IPv4 o IPv6. La notazione Indirizzo IP deve essere specificata nella notazione CIDR per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo esatto, è possibile usare un indirizzo simile a 1.2.3.4/32, dove i primi quattro otteti rappresentano l'indirizzo IP e /32 è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, è possibile leggere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio consentono di limitare l'accesso alle subnet della rete virtuale di Azure selezionate. Per limitare l'accesso a una subnet specifica, creare una regola di restrizione con un tipo di rete virtuale. È possibile selezionare la sottoscrizione, la VNet e la subnet a cui si vuole consentire o negare l'accesso. Se gli endpoint di servizio non sono già abilitati con Microsoft. Web per la subnet selezionata, verranno automaticamente abilitati, a meno che non si selezioni la casella che richiede di non eseguire tale operazione. La situazione in cui si vuole abilitarla nell'app, ma non la subnet è sostanzialmente correlata a se si dispone delle autorizzazioni per abilitare gli endpoint di servizio nella subnet. Se è necessario ottenere un altro utente per abilitare gli endpoint di servizio nella subnet, è possibile selezionare la casella e configurare l'app per gli endpoint di servizio in modo che venga abilitata in un secondo momento nella subnet. 

![aggiungere una regola di restrizione dell'accesso VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Gli endpoint di servizio non possono essere usati per limitare l'accesso alle app eseguite in un ambiente del servizio app. Quando l'app si trova in una ambiente del servizio app, è possibile controllare l'accesso all'app con le regole di accesso IP. 

Con gli endpoint di servizio è possibile configurare l'app con gateway applicazione o altri dispositivi WAF. È anche possibile configurare applicazioni multilivello con backend protetti. Per altri dettagli su alcune delle possibilità, vedere [funzionalità di rete e servizio app](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Gestione delle regole di restrizione dell'accesso

È possibile fare clic su una riga qualsiasi per modificare una regola di restrizione dell'accesso esistente. Le modifiche diventano effettive immediatamente, incluse le modifiche nell'ordine di priorità.

![modificare una regola di restrizione dell'accesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando si modifica una regola, non è possibile modificare il tipo tra una regola indirizzi IP e una regola della rete virtuale. 

![modificare una regola di restrizione dell'accesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Per eliminare una regola, fare clic su **...** nella regola e su **rimuovi**.

![Elimina regola di restrizione accesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blocco di un singolo indirizzo IP ##

Quando si aggiunge la prima regola di restrizione IP, il servizio aggiunge una regola esplicita **Deny All** con una priorità pari a 2147483647. In pratica, la regola esplicita **Deny All** verrà eseguita l'ultima regola e bloccherà l'accesso a qualsiasi indirizzo IP non consentito in modo esplicito tramite una regola di **autorizzazione** .

Per lo scenario in cui gli utenti desiderano bloccare in modo esplicito un singolo indirizzo IP o un blocco di indirizzi IP, ma consentire l'accesso a tutti gli altri elementi, è necessario aggiungere una regola esplicita **Consenti tutto** .

![Blocca indirizzo IP singolo](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Sito SCM 

Oltre a essere in grado di controllare l'accesso all'app, è anche possibile limitare l'accesso al sito SCM usato dall'app. Il sito SCM è l'endpoint di distribuzione Web e la console Kudu. È possibile assegnare separatamente restrizioni di accesso al sito SCM dall'app o usare lo stesso set sia per l'app che per il sito SCM. Quando si seleziona la casella per avere le stesse restrizioni dell'app, tutto viene svuotato. Se si deseleziona la casella, verranno applicate tutte le impostazioni presenti in precedenza nel sito SCM. 

![elencare le restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipolazione a livello di codice delle regole di restrizione di accesso ##

Attualmente non è disponibile un'interfaccia della riga di comando o PowerShell per le nuove funzionalità di restrizione dell'accesso, ma i valori possono essere impostati manualmente con un'operazione di inserimento dell' [API REST di Azure](https://docs.microsoft.com/rest/api/azure/) nella configurazione dell'app in Gestione risorse. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

Il percorso per questa informazione in Gestione risorse è:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

La sintassi JSON per l'esempio precedente è:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Restrizioni IP app per le funzioni

Le restrizioni IP sono disponibili per entrambe le app per le funzioni con le stesse funzionalità dei piani di servizio app. Con l'abilitazione delle restrizioni IP, l'editor del codice del portale viene disabilitato per tutti gli indirizzi IP non

Fare clic [qui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions) per altre informazioni


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
