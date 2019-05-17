---
title: Limitare l'accesso - servizio App di Azure | Microsoft Docs
description: Come usare le restrizioni di accesso con il servizio App di Azure
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: de898a7ebb9611f469f42bb23774b3b0a0c2410d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541677"
---
# <a name="azure-app-service-access-restrictions"></a>Restrizioni di accesso di servizio App di Azure #

Restrizioni di accesso consentono di definire un elenco ordinato Consenti/Nega priorità che controlla l'accesso alla rete per l'app. Questo elenco può includere gli indirizzi IP o subnet di rete virtuale di Azure. Quando sono presenti una o più voci, è quindi implicita "deny_all" presente alla fine dell'elenco.

La funzionalità di restrizioni di accesso funziona con tutti i servizio App di lavoro ospitato carica compresi; le app Web, App per le API, App Linux, le app contenitore Linux e funzioni.

Quando viene effettuata una richiesta all'App, l'indirizzo del mittente viene valutata in base alle regole di indirizzi IP nell'elenco di restrizioni di accesso. Se l'indirizzo del mittente è in una subnet che viene configurata con gli endpoint di servizio per Microsoft. Web, la subnet di origine viene confrontata con le regole della rete virtuale nel proprio elenco di restrizioni di accesso. Se all'indirizzo non viene consentito l'accesso in base alle regole nell'elenco, il servizio replica con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La funzionalità di restrizioni di accesso viene implementata nei ruoli front-end del servizio App, ovvero upstream degli host di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni di accesso sono effettivamente gli ACL di rete.

La possibilità di limitare l'accesso all'App web da una rete virtuale di Azure (VNet) viene chiamata [endpoint di servizio][serviceendpoints]. Gli endpoint di servizio consentono di limitare l'accesso a un servizio multi-tenant dalla subnet selezionata. Deve essere abilitata sul lato di rete oltre a servizio che viene viene abilitata con. Non funziona per limitare il traffico verso le app ospitate in un ambiente del servizio App.  Se trovano in un ambiente del servizio App, è possibile controllare l'accesso all'App con regole degli indirizzi IP.

![flusso di restrizioni di accesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Aggiunta e modifica delle regole di limitazione dell'accesso nel portale ##

Per aggiungere una regola di restrizione accesso all'App, usare il menu per aprire **Network**>**restrizioni di accesso** e fare clic su **configurare restrizioni di accesso**

![Opzioni di connettività di rete del servizio app](media/app-service-ip-restrictions/access-restrictions.png)  

Dall'interfaccia utente restrizioni di accesso, è possibile esaminare l'elenco delle regole di restrizioni di accesso definiti per l'app.

![restrizioni di accesso di elenco](media/app-service-ip-restrictions/access-restrictions-browse.png)

L'elenco mostrerà tutte le restrizioni correnti che sono nella tua app. Se si dispone di una restrizione di rete virtuale nella tua app, verrà visualizzato nella tabella se gli endpoint di servizio sono abilitati per Microsoft. Web. Quando non sono previste restrizioni definite nella tua app, l'app sarà accessibile da qualsiasi posizione.  

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizione di accesso. Dopo l'aggiunta una regola diventa effettiva immediatamente. Le regole vengono applicate in base alle priorità dal numero più basso al più alto. È presente un nega tutto implicito anche dopo l'aggiunta di una singola regola.

![aggiungere una regola di restrizione di accesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Quando si crea una regola, è necessario selezionare Consenti/Nega e anche il tipo di regola. È inoltre necessario fornire il valore di priorità e ciò che si è limitare l'accesso a.  È possibile aggiungere facoltativamente un nome e descrizione della regola.  

Per impostare un indirizzo IP basata su regole, selezionare un tipo di IPv4 o IPv6. La notazione Indirizzo IP deve essere specificata nella notazione CIDR per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo esatto, è possibile usare un indirizzo simile a 1.2.3.4/32, dove i primi quattro otteti rappresentano l'indirizzo IP e /32 è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, è possibile leggere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

![aggiungere una regola di restrizione di accesso di rete virtuale](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Per limitare l'accesso alla subnet selezionata, selezionare un tipo di rete virtuale. Di seguito sarà possibile selezionare la sottoscrizione della rete virtuale e subnet che si desidera consentire o negare l'accesso con. Se gli endpoint di servizio non sono abilitati già con Microsoft. Web per la subnet selezionata, si saranno abilitato automaticamente per l'utente a meno che non si seleziona la casella in cui viene chiesto di non eseguire questa operazione. La situazione in cui si desidera abilitare la funzionalità in app, ma non la subnet è correlata in gran parte se si dispone delle autorizzazioni per abilitare gli endpoint di servizio nella subnet o No. Se è necessario ottenere qualcun altro per abilitare gli endpoint di servizio nella subnet, è possibile selezionare la casella di controllo e delle App configurati per gli endpoint di servizio in attesa di essere abilitato in un secondo momento nella subnet. 

Gli endpoint di servizio non sono utilizzabile per limitare l'accesso alle App che vengono eseguiti in un ambiente del servizio App. Quando l'app è in un ambiente del servizio App, è possibile controllare l'accesso all'App con le regole di accesso IP. 

È possibile fare clic su qualsiasi riga per modificare una regola di restrizione di accesso esistente. Le modifiche diventano effettive immediatamente, incluse le modifiche nell'ordine di priorità.

![modificare una regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando si modifica una regola, è possibile modificare il tipo tra una regola di indirizzo IP e una regola della rete virtuale. 

![modificare una regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Per eliminare una regola, fare clic su **...** nella regola e su **rimuovi**.

![Elimina regola di restrizione di accesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>Sito SCM 

Oltre a essere in grado di controllare l'accesso all'App, è inoltre possibile limitare l'accesso al sito scm usato dall'app. Il sito scm è web distribuire endpoint e anche la console Kudu. Separatamente, è possibile assegnare le restrizioni di accesso al sito scm dall'app o usare lo stesso set per l'app e il sito scm. Quando si seleziona la casella per avere le stesse restrizioni dell'App, tutto ciò che viene visualizzato come vuoto. Se si deseleziona la casella, vengono applicate tutte le impostazioni nel sito scm era in precedenza. 

![restrizioni di accesso di elenco](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Modifica livello di codice restrizione delle regole di accesso ##

Attualmente non esiste un comando o PowerShell per la nuova funzionalità di restrizioni di accesso, ma i valori possono essere impostati manualmente con un'operazione PUT sulla configurazione di app in Resource Manager. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

Il percorso per questa informazione in Gestione risorse è:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

La sintassi JSON per l'esempio precedente è:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>Restrizioni IP dell'App (funzione)

Le restrizioni IP sono disponibili per entrambe le App per le funzioni con la stessa funzionalità di piani di servizio App. Abilitazione delle restrizioni IP disabiliterà l'editor di codice del portale per eventuali indirizzi IP non consentiti.

Fare clic [qui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions) per altre informazioni


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
