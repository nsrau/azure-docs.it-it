---
title: Imporre restrizioni agli indirizzi IP dei client - Servizio app di Azure | Microsoft Docs
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 805de614246028bc75268e83991fa7831b990325
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882328"
---
# <a name="azure-app-service-static-access-restrictions"></a>Restrizioni di accesso statiche di servizio App di Azure #

Restrizioni di accesso consentono di definire una priorità Consenti/Nega elenco ordinato di indirizzi IP consentiti per accedere all'app. L'elenco consenti può includere gli indirizzi IPv4 e IPv6. In presenza di una o più voci, alla fine dell'elenco è presente un nega tutto implicito.

La funzionalità di restrizioni di accesso funziona con tutte le che App ospitate del servizio carichi di lavoro, che sono rappresentati da: le app Web, App per le API, App Linux, le app contenitore Linux e funzioni.

Quando viene effettuata una richiesta all'App, l'indirizzo IP iniziale viene valutata rispetto all'elenco di restrizioni di accesso. Se all'indirizzo non viene consentito l'accesso in base alle regole nell'elenco, il servizio replica con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La funzionalità di restrizioni di accesso viene implementata nei ruoli front-end del servizio App, ovvero upstream degli host di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni di accesso sono effettivamente gli ACL di rete.  

![flusso di restrizioni di accesso](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Per un periodo di tempo, la funzionalità di restrizioni di accesso nel portale è stato un livello sopra la funzionalità ipSecurity in IIS. La funzionalità di restrizioni di accesso corrente è diversa. È comunque possibile configurare ipSecurity all'interno dell'applicazione Web. config, ma verranno applicate le regole di restrizioni di accesso basato su server front-end prima di tutto il traffico raggiunge IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Aggiunta e modifica delle regole di limitazione dell'accesso nel portale ##

Per aggiungere una regola di restrizione accesso all'App, usare il menu per aprire **Network**>**restrizioni di accesso** e fare clic su **configurare restrizioni di accesso**

![Opzioni di connettività di rete del servizio app](media/app-service-ip-restrictions/ip-restrictions.png)  

Dall'interfaccia utente restrizioni di accesso, è possibile esaminare l'elenco delle regole di restrizioni di accesso definiti per l'app.

![restrizioni di accesso di elenco](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Se le regole sono state configurate come in questa immagine, l'app accetta solo il traffico da 131.107.159.0/24 e nega quello proveniente da qualsiasi altro indirizzo IP.

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizione di accesso. Dopo l'aggiunta una regola diventa effettiva immediatamente. Le regole vengono applicate in base alle priorità dal numero più basso al più alto. È presente un nega tutto implicito anche dopo l'aggiunta di una singola regola.

![aggiungere una regola di restrizione di accesso](media/app-service-ip-restrictions/ip-restrictions-add.png)

La notazione Indirizzo IP deve essere specificata nella notazione CIDR per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo esatto, è possibile usare un indirizzo simile a 1.2.3.4/32, dove i primi quattro otteti rappresentano l'indirizzo IP e /32 è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, è possibile leggere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

È possibile fare clic su qualsiasi riga per modificare una regola di restrizione di accesso esistente. Le modifiche diventano effettive immediatamente, incluse le modifiche nell'ordine di priorità.

![modificare una regola di restrizione di accesso](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Per eliminare una regola, fare clic su **...** nella regola e su **rimuovi**.

![Elimina regola di restrizione di accesso](media/app-service-ip-restrictions/ip-restrictions-delete.png)

È inoltre possibile limitare l'accesso di distribuzione nella scheda successiva. Per aggiungere/modificare/eliminare ogni regola, seguire il passaggio stesso come illustrato in precedenza.

![restrizioni di accesso di elenco](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

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
