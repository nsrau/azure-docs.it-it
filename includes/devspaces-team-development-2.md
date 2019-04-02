---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439520"
---
### <a name="run-the-service"></a>Eseguire il servizio

Per eseguire il servizio, premere F5 oppure digitare `azds up` nella finestra del terminale. Il servizio verrà automaticamente eseguito nello spazio _dev/scott_ appena selezionato. Verificare se il servizio è in esecuzione nel proprio spazio eseguendo `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Notare che ora un'istanza di *mywebapi* è in esecuzione nello spazio _dev/scott_. La versione in esecuzione in _dev_ è ancora in esecuzione ma non è elencata.

Eseguire `azds list-uris` per elencare gli URL per lo spazio corrente.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

All'URL del punto di accesso pubblico per *webfrontend* è stato aggiunto il prefisso *scott.s*. Questo URL è univoco per lo spazio _dev/scott_. Il prefisso dell'URL indica al controller Ingress di instradare le richieste alla versione _dev/scott_ di un servizio. Quando una richiesta con questo URL viene gestita da Dev Spaces, il controller Ingress prova prima a instradarla al servizio *webfrontend* nello spazio _dev/scott_. Se il tentativo non riesce, la richiesta verrà instradata al servizio *webfrontend* nello spazio _dev_ come fallback. Si noti inoltre che è presente un URL di localhost per l'accesso al servizio tramite localhost usando la funzionalità *port-forward* di Kubernetes. Per altre informazioni sugli URL e sul routing in Azure Dev Spaces, vedere [Come funziona e come p configurato Azure Dev Spaces](../articles/dev-spaces/how-dev-spaces-works.md).



![Routing dello spazio](../articles/dev-spaces/media/common/Space-Routing.png)

Questa funzionalità incorporata di Azure Dev Spaces consente di testare codice in uno spazio condiviso senza richiedere a ogni sviluppatore ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede al codice dell'app di inoltrare le intestazioni di propagazione, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-in-a-space"></a>Test del codice in uno spazio
Per testare la nuova versione di *mywebapi* in combinazione con *webfrontend*, aprire il browser all'URL del punto di accesso pubblico per *webfrontend* e passare alla pagina delle informazioni. Dovrebbe essere visualizzato il nuovo messaggio.

A questo punto, rimuovere parte dell'URL "scott.s." e aggiornare il browser. Verrà visualizzato il comportamento precedente con la versione di *mywebapi* in esecuzione in _dev_.

Dopo aver impostato uno spazio _dev_ che contiene sempre le ultime modifiche, e presupponendo che l'applicazione sia progettata per trarre vantaggio dal routing basato su spazio di DevSpace descritto in questa sezione dell'esercitazione, dovrebbe risultare evidente quanto può essere utile usare Dev Spaces per testare le nuove funzionalità nel contesto dell'applicazione più grande. Invece di dover distribuire _tutti_ i servizi nello spazio privato, è possibile creare uno spazio privato che deriva da _dev_ e attivare solo i servizi effettivamente in uso. L'infrastruttura di routing di Dev Spaces gestirà il resto utilizzando tutti i servizi dello spazio privato che riesce a trovare, mentre per impostazione predefinita torna all'ultima versione in esecuzione nello spazio _dev_. E soprattutto _più_ sviluppatori possono sviluppare attivamente diversi servizi contemporaneamente nel proprio spazio senza interrompersi a vicenda.

### <a name="well-done"></a>Ecco fatto!
La Guida introduttiva è stata completata. Si è appreso come:

> [!div class="checklist"]
> * Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
> * Sviluppare codice in modo iterativo nei contenitori.
> * Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
> * Sviluppare e testare il codice in modo produttivo in un ambiente di team.
> * Stabilire una baseline di funzionalità usando Dev Spaces per facilitare il test delle modifiche isolate nel contesto di un'applicazione di microservizi più grande

Ora che si è preso visione di Azure Dev Spaces, [condividere lo spazio di sviluppo con un membro del team](../articles/dev-spaces/how-to/share-dev-spaces.md) e iniziare a collaborare.

## <a name="clean-up"></a>Eseguire la pulizia
Per eliminare completamente un'istanza di Azure Dev Spaces in un cluster, inclusi tutti gli spazi di sviluppo e i servizi in esecuzione al suo interno, usare il comando `az aks remove-dev-spaces`. Tenere presente che questa operazione è irreversibile. È possibile aggiungere di nuovo il supporto per Azure Dev Spaces nel cluster, ma sarà come iniziare da zero. I vecchi spazi e servizi non verranno ripristinati.

L'esempio seguente elenca i controller Azure Dev Spaces nella sottoscrizione attiva e quindi elimina il controller Azure Dev Spaces associato al cluster servizio Azure Kubernetes "myservizio Azure Kubernetes" nel gruppo di risorse "myservizio Azure Kubernetes-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```