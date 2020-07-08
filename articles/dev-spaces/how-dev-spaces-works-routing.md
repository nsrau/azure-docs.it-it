---
title: Funzionamento del routing con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Vengono descritti i processi di Power Azure Dev Spaces e il funzionamento del routing
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 126a534cec2ee4b07aa3a127fb3f47f9931f0031
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84307419"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Funzionamento del routing con Azure Dev Spaces

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes. Quando il progetto viene eseguito in uno spazio di sviluppo, Azure Dev Spaces fornisce funzionalità di rete e di routing aggiuntive per il progetto.

Questo articolo descrive il funzionamento del routing con gli spazi di sviluppo.

## <a name="how-routing-works"></a>Funzionamento del routing

Uno spazio di sviluppo si basa su AKS e usa gli stessi [concetti di rete](../aks/concepts-network.md). Azure Dev Spaces dispone anche di un servizio *ingressmanager* centralizzato e distribuisce il proprio controller di ingresso nel cluster AKS. Il servizio *ingressmanager* monitora i cluster AKS con spazi di sviluppo e aumenta il controller di ingresso Azure Dev Spaces nel cluster con gli oggetti in ingresso per il routing ai pod dell'applicazione. Il contenitore devspaces-proxy in ogni pod aggiunge un' `azds-route-as` intestazione HTTP per il traffico HTTP a uno spazio di sviluppo basato sull'URL. Ad esempio, una richiesta all'URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* otterrebbe un'intestazione HTTP con `azds-route-as: azureuser` . Il contenitore devspaces-proxy non aggiunge un' `azds-route-as` intestazione se ne è già presente una.

Quando viene effettuata una richiesta HTTP a un servizio dall'esterno del cluster, la richiesta viene inviata al controller di ingresso. Il controller di ingresso instrada la richiesta direttamente al Pod appropriato in base alle regole e agli oggetti in ingresso. Il contenitore devspaces-proxy nel pod riceve la richiesta, aggiunge l' `azds-route-as` intestazione in base all'URL, quindi instrada la richiesta al contenitore dell'applicazione.

Quando viene effettuata una richiesta HTTP a un servizio da un altro servizio all'interno del cluster, la richiesta passa prima di tutto al contenitore devspaces-proxy del servizio chiamante. Il contenitore devspaces-proxy esamina la richiesta HTTP e controlla l' `azds-route-as` intestazione. In base all'intestazione, il contenitore del proxy di devspaces cercherà l'indirizzo IP del servizio associato al valore dell'intestazione. Se viene trovato un indirizzo IP, il contenitore devspaces-Proxy reindirizza la richiesta a tale indirizzo IP. Se non viene trovato un indirizzo IP, il contenitore devspaces-proxy instrada la richiesta al contenitore dell'applicazione padre.

Ad esempio, le applicazioni *servicea* e *serviceB* vengono distribuite in uno spazio di sviluppo padre denominato *default*. *servicea* si basa su *serviceB* ed effettua chiamate http. L'utente di Azure crea uno spazio di sviluppo figlio basato sullo spazio *predefinito* denominato *azureuser*. L'utente di Azure distribuisce anche la propria versione di *servicea* nello spazio figlio. Quando viene effettuata una richiesta a *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Routing Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Il controller di ingresso cerca l'IP per il Pod associato all'URL, che è *servicea. azureuser*.
1. Il controller di ingresso trova l'indirizzo IP per il Pod nello spazio di sviluppo dell'utente di Azure e instrada la richiesta al Pod *servicea. azureuser* .
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la richiesta e aggiunge `azds-route-as: azureuser` come intestazione HTTP.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* instrada la richiesta al contenitore dell'applicazione *servicea* nel pod *servicea. azureuser* .
1. L'applicazione *servicea* nel pod *servicea. azureuser* effettua una chiamata a *serviceB*. L'applicazione *servicea* contiene anche il codice per mantenere l' `azds-route-as` intestazione esistente, che in questo caso è `azds-route-as: azureuser` .
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la richiesta e cerca l'IP di *serviceB* in base al valore dell' `azds-route-as` intestazione.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* non trova un IP per *serviceB. azureuser*.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* cerca l'IP per *serviceB* nello spazio padre, ovvero *serviceB. default*.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* trova l'indirizzo IP per *serviceB. default* e instrada la richiesta al Pod *serviceB. default* .
1. Il contenitore devspaces-proxy nel pod *serviceB. default* riceve la richiesta e instrada la richiesta al contenitore dell'applicazione *serviceb* nel pod *serviceB. default* .
1. L'applicazione *serviceB* nel pod *serviceB. default* restituisce una risposta al Pod *servicea. azureuser* .
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la risposta e instrada la risposta al contenitore dell'applicazione *servicea* nel pod *servicea. azureuser* .
1. L'applicazione *servicea* riceve la risposta e quindi restituisce la propria risposta.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la risposta dal contenitore dell'applicazione *servicea* e instrada la risposta al chiamante originale all'esterno del cluster.

Tutto il resto del traffico TCP che non è HTTP passa attraverso il controller di ingresso e i contenitori devspaces-proxy non modificati.

## <a name="sharing-a-dev-space"></a>Condivisione di uno spazio di sviluppo

Quando si lavora con un team, è possibile [condividere uno spazio di sviluppo in un intero team](how-to/share-dev-spaces.md) e creare spazi di sviluppo derivati. Uno spazio di sviluppo può essere usato da chiunque disponga dell'accesso come collaboratore al gruppo di risorse dello spazio di sviluppo.

È anche possibile creare un nuovo spazio di sviluppo derivato da un altro spazio di sviluppo. Quando si crea uno spazio di sviluppo derivato, l'etichetta *azds.io/Parent-Space=Parent-Space-Name* viene aggiunta allo spazio dei nomi dello spazio di sviluppo derivato. Inoltre, tutte le applicazioni dallo spazio di sviluppo padre vengono condivise con lo spazio di sviluppo derivato. Se si distribuisce una versione aggiornata di un'applicazione nello spazio di sviluppo derivato, questa sarà presente solo nello spazio di sviluppo derivato e lo spazio dev padre rimarrà inalterato. È possibile disporre di un massimo di tre livelli di spazi di sviluppo o di spazi *padre* derivati.

Lo spazio di sviluppo derivato effettuerà anche il routing intelligente delle richieste tra le proprie applicazioni e le applicazioni condivise dal relativo elemento padre. Il routing funziona tentando di indirizzare la richiesta a un'applicazione nello spazio di sviluppo derivato e di eseguire il fallback all'applicazione condivisa dallo spazio di sviluppo padre. Se l'applicazione non si trova nello spazio padre, il routing verrà eseguito nuovamente nell'applicazione condivisa nell'area del padre.

Ad esempio:
* Il *valore predefinito* dello spazio di sviluppo include le applicazioni *servicea* e *serviceB*.
* Il *valore predefinito*è derivato dallo spazio di sviluppo *azureuser* .
* Una versione aggiornata di *servicea* viene distribuita a *azureuser*.

Quando si usa *azureuser*, tutte le richieste a *servicea* verranno indirizzate alla versione aggiornata in *azureuser*. Una richiesta a *serviceB* tenterà prima di essere indirizzata alla versione *azureuser* di *serviceB*. Poiché non esiste, verrà instradato alla versione *predefinita* di *serviceB*. Se viene rimossa la versione *azureuser* di *servicea* , tutte le richieste a *servicea* eseguiranno il fallback utilizzando la versione *predefinita* di *servicea*.

## <a name="next-steps"></a>Passaggi successivi

Per vedere alcuni esempi di come Azure Dev Spaces usa il routing per fornire iterazioni e sviluppo rapidi, vedere [come funziona il processo locale con Kubernetes][how-it-works-local-process-kubernetes], [come eseguire il debug remoto del codice con Azure Dev Spaces funziona][how-it-works-remote-debugging]e le [azioni di GitHub & servizio Azure Kubernetes][pr-flow].

Per iniziare a usare il routing con Azure Dev Spaces per lo sviluppo in team, vedere la pagina relativa [allo sviluppo di team in Azure Dev Spaces][quickstart-team] Guida introduttiva.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-local-process-kubernetes]: how-dev-spaces-works-local-process-kubernetes.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md