---
title: Imporre restrizioni agli indirizzi IP dei client - Servizio app di Azure | Microsoft Docs
description: Come usare le restrizioni IP con il Servizio app di Azure
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
ms.openlocfilehash: 337d71c84ace7f44c2668cf2344d9083c4a85bee
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651063"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrizioni IP statico del Servizio app di Azure #

Le restrizioni IP consentono di definire un elenco consenti/nega basato sulle priorità di indirizzi IP a cui è consentito accedere all'app. L'elenco consenti può includere gli indirizzi IPv4 e IPv6. In presenza di una o più voci, alla fine dell'elenco è presente un nega tutto implicito. 

La funzionalità Restrizioni IP è applicabile a tutti i carichi di lavoro ospitati dal servizio app, incluse app Web, app API, app Linux, app contenitore Linux e Funzioni. 

Quando viene avanzata una richiesta all'app, l'indirizzo IP FROM viene valutato rispetto all'elenco Restrizioni IP. Se all'indirizzo non viene consentito l'accesso in base alle regole nell'elenco, il servizio replica con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La funzionalità Restrizioni IP viene implementata nei ruoli front-end del servizio app, che si trovano a valle rispetto agli host ruolo di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni IP sono effettivamente gli ACL di rete.  

![Flusso di restrizioni IP](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Nel portale la funzionalità IP era un layer sulla funzionalità ipSecurity in IIS. La funzionalità Restrizioni IP corrente è diversa. È comunque possibile configurare ipSecurity in web.config dell'applicazione, ma le regole delle Restrizioni IP front-end verranno applicate prima che il traffico raggiunga IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Aggiunta e modifica delle regole delle Restrizioni IP nel portale ##

Per aggiungere una regola di restrizione IP all'app, usare il menu per aprire **Rete**>**Restrizioni IP** e fare clic su **Configura restrizioni IP**

![Opzioni di connettività di rete del servizio app](media/app-service-ip-restrictions/ip-restrictions.png)  

Dall'interfaccia utente delle Restrizioni IP, è possibile rivedere l'elenco delle regole di restrizione IP definite per l'app.

![elencare le restrizioni IP](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Se le regole sono state configurate come in questa immagine, l'app accetta solo il traffico da 131.107.159.0/24 e nega quello proveniente da qualsiasi altro indirizzo IP.

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizioni IP. Dopo l'aggiunta una regola diventa effettiva immediatamente. Le regole vengono applicate in base alle priorità dal numero più basso al più alto. È presente un nega tutto implicito anche dopo l'aggiunta di una singola regola. 

![aggiungere una regola di restrizioni IP](media/app-service-ip-restrictions/ip-restrictions-add.png)

La notazione Indirizzo IP deve essere specificata nella notazione CIDR per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo esatto, è possibile usare un indirizzo simile a 1.2.3.4/32, dove i primi quattro otteti rappresentano l'indirizzo IP e /32 è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, è possibile leggere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

È possibile fare clic su qualsiasi riga per modificare una regola di restrizione IP esistente. Le modifiche diventano effettive immediatamente, incluse le modifiche nell'ordine di priorità.

![modificare una regola di restrizione IP](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Per eliminare una regola, fare clic su **...** nella regola e su **rimuovi**. 

![eliminare una regola di restrizione IP](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Manipolazione programmatica delle regole di restrizione IP ##

Al momento non vi è alcuna interfaccia della riga di comando o PowerShell per la nuova funzionalità Restrizioni IP, ma i valori possono essere impostati manualmente con un'operazione PUT sulla configurazione dell'app in Gestione risorse. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario. 

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
