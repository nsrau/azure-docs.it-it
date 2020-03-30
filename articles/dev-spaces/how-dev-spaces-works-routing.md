---
title: Funzionamento del routing con azure Dev SpacesHow routing works with Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi che alimentano Azure Dev Spaces e come funziona il routing
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241387"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Funzionamento del routing con azure Dev SpacesHow routing works with Azure Dev Spaces

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes (AKS). Una volta che il progetto è in esecuzione in uno spazio di sviluppo, Azure Dev Spaces offre funzionalità di rete e routing aggiuntive per il progetto.

In questo articolo viene descritto il funzionamento del routing con Dev Spaces.This article describes how routing works with Dev Spaces.

## <a name="how-routing-works"></a>Funzionamento del routing

Uno spazio di sviluppo si basa su AKS e utilizza gli stessi concetti di [rete.](../aks/concepts-network.md) Azure Dev Spaces dispone inoltre di un servizio *ingressmanager* centralizzato e distribuisce il proprio controller in ingresso nel cluster AKS. Il servizio *ingressmanager* monitora i cluster AKS con spazi di sviluppo e aumenta il controller di ingresso degli spazi di sviluppo di Azure nel cluster con gli oggetti Ingress per il routing ai pod dell'applicazione. Il contenitore devspaces-proxy in `azds-route-as` ogni pod aggiunge un'intestazione HTTP per il traffico HTTP a uno spazio di sviluppo in base all'URL. Ad esempio, una richiesta *http://azureuser.s.default.serviceA.fedcba09...azds.io* all'URL otterrebbe un'intestazione HTTP con `azds-route-as: azureuser`. Il contenitore devspaces-proxy `azds-route-as` non aggiungerà un'intestazione se ne è già presente una.

Quando viene effettuata una richiesta HTTP a un servizio esterno al cluster, la richiesta viene inviata al controller in ingresso. Il controller Ingress instrada la richiesta direttamente al pod appropriato in base ai relativi oggetti e regole Ingress. Il contenitore devspaces-proxy nel pod riceve `azds-route-as` la richiesta, aggiunge l'intestazione in base all'URL e quindi instrada la richiesta al contenitore dell'applicazione.

Quando viene effettuata una richiesta HTTP a un servizio da un altro servizio all'interno del cluster, la richiesta passa prima attraverso il contenitore devspaces-proxy del servizio chiamante. Il contenitore devspaces-proxy esamina la `azds-route-as` richiesta HTTP e controlla l'intestazione. In base all'intestazione, il contenitore devspaces-proxy cercherà l'indirizzo IP del servizio associato al valore dell'intestazione. Se viene trovato un indirizzo IP, il contenitore devspaces-proxy reindirizza la richiesta a tale indirizzo IP. Se non viene trovato un indirizzo IP, il contenitore devspaces-proxy indirizza la richiesta al contenitore dell'applicazione padre.

Ad esempio, le applicazioni *serviceA* e *serviceB* vengono distribuite in uno spazio di sviluppo padre denominato *default*. *serviceA* si basa su *serviceB* ed effettua chiamate HTTP ad esso. Utente di Azure crea uno spazio di sviluppo figlio in base allo spazio *predefinito* denominato *azureuser*. Utente di Azure distribuisce anche la propria versione di *serviceA* nel proprio spazio figlio. Quando viene fatta *http://azureuser.s.default.serviceA.fedcba09...azds.io*una richiesta a:

![Routing di Azure Dev SpacesAzure Dev Spaces routing](media/how-dev-spaces-works/routing.svg)

1. Il controller Ingress cerca l'INDIRIZZO IP per il pod associato all'URL, ovvero *serviceA.azureuser*.
1. Il controller Ingress trova l'IP per il pod nello spazio di sviluppo dell'utente di Azure e instrada la richiesta al pod *serviceA.azureuser.*
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* `azds-route-as: azureuser` riceve la richiesta e aggiunge come intestazione HTTP.
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* indirizza la richiesta al contenitore dell'applicazione *serviceA* nel pod *serviceA.azureuser.*
1. L'applicazione *serviceA* nel pod *serviceA.azureuser* effettua una chiamata a *serviceB*. L'applicazione *serviceA* contiene anche `azds-route-as` il codice per mantenere `azds-route-as: azureuser`l'intestazione esistente, che in questo caso è .
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* riceve la richiesta e cerca l'IP di *serviceB* in base al valore dell'intestazione. `azds-route-as`
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* non trova un IP per *serviceB.azureuser*.
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* cerca l'IP di *serviceB* nello spazio padre, ovvero *serviceB.default*.
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* trova l'IP per *serviceB.default* e instrada la richiesta al pod *serviceB.default.*
1. Il contenitore devspaces-proxy nel pod *serviceB.default* riceve la richiesta e instrada la richiesta al contenitore dell'applicazione *serviceB* nel pod *serviceB.default.*
1. L'applicazione *serviceB* nel pod *serviceB.default* restituisce una risposta al pod *serviceA.azureuser.*
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* riceve la risposta e instrada la risposta al contenitore dell'applicazione *serviceA* nel pod *serviceA.azureuser.*
1. L'applicazione *serviceA* riceve la risposta e quindi restituisce la propria risposta.
1. Il contenitore devspaces-proxy nel pod *serviceA.azureuser* riceve la risposta dal contenitore dell'applicazione *serviceA* e instrada la risposta al chiamante originale all'esterno del cluster.

Tutto il resto del traffico TCP che non è HTTP passa attraverso il controller in ingresso e devspaces-server proxy contenitori non modificati.

## <a name="sharing-a-dev-space"></a>Condivisione di uno spazio di sviluppo

Quando si lavora con un team, è possibile condividere uno spazio di [sviluppo in un intero team](how-to/share-dev-spaces.md) e creare spazi di sviluppo derivati. Uno spazio di sviluppo può essere usato da chiunque abbia accesso come collaboratore al gruppo di risorse dello spazio di sviluppo.

È anche possibile creare un nuovo spazio di sviluppo derivato da un altro spazio di sviluppo. Quando si crea uno spazio di sviluppo derivato, l'etichetta *azds.io/parent-space=PARENT-SPACE-NAME* viene aggiunta allo spazio dei nomi dello spazio dei nomi del vano di sviluppo derivato. Inoltre, tutte le applicazioni dello spazio di sviluppo padre vengono condivise con lo spazio di sviluppo derivato. Se si distribuisce una versione aggiornata di un'applicazione nello spazio di sviluppo derivato, questa esisterà solo nello spazio di sviluppo derivato e lo spazio di sviluppo padre rimarrà inalterato. È possibile avere un massimo di tre livelli di spazi dev derivati o *non per elementi padre.*

Lo spazio di sviluppo derivato instrada inoltre in modo intelligente le richieste tra le proprie applicazioni e le applicazioni condivise dal relativo elemento padre. Il routing funziona tentando di instradare la richiesta a un'applicazione nello spazio di sviluppo derivato ed eseguendo il fallback all'applicazione condivisa dallo spazio di sviluppo padre. Il routing eseguirà il rollback all'applicazione condivisa nello spazio nonno se l'applicazione non si trova nello spazio padre.

Ad esempio:
* Il *valore predefinito* dello spazio di sviluppo include applicazioni *serviceA* e *serviceB*.
* Lo spazio di sviluppo *azureuser* è derivato *dall'impostazione predefinita.*
* Una versione aggiornata di *serviceA* viene distribuita *in azureuser*.

Quando si usa *azureuser*, tutte le richieste a *serviceA* verranno instradate alla versione aggiornata in *azureuser*. Una richiesta a *serviceB* tenterà innanzitutto di essere instradata alla versione *azureuser* di *serviceB*. Poiché non esiste, verrà instradato alla versione *predefinita* di *serviceB*. Se la versione *azureuser* di *serviceA* viene rimossa, tutte le richieste a *serviceA* verranno rieliminate utilizzando la versione *predefinita* di *serviceA*.

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare alcuni esempi di come Azure Dev Spaces usa il routing per fornire un'iterazione e uno sviluppo rapidi, vedere [Come funziona la connessione del computer][how-it-works-connect]di sviluppo allo spazio di sviluppo , Come funziona il debug remoto del codice con Azure Dev [Spaces][how-it-works-remote-debugging]e [GitHub Actions & Azure Kubernetes Service][pr-flow].

Per iniziare a usare il routing con Azure Dev Spaces per lo sviluppo in team, vedere la guida introduttiva allo sviluppo di team in Azure Dev Spaces.To get started using routing with Azure Dev Spaces for team development, see the [team development in Azure Dev Spaces][quickstart-team] quickstart.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md