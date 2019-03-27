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
ms.openlocfilehash: 5d66dcaccc6ca2e40fbd516f535ec56c1baf6b17
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195625"
---
### <a name="run-the-service"></a>Eseguire il servizio

1. Premere F5 (o digitare `azds up` nella finestra del terminale) per eseguire il servizio. Il servizio verrà automaticamente eseguito nello spazio _dev/scott_ appena selezionato. 
1. È possibile confermare che il servizio è in esecuzione nel proprio spazio eseguendo nuovamente `azds list-up`. Si noterà che è in esecuzione un'istanza di *mywebapi* nello spazio _dev/scott_ (la versione in esecuzione in _dev_ è ancora in esecuzione ma non è elencata).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Eseguire `azds list-uris` e notare l'URL del punto di accesso per *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Usare l'URL con il prefisso *scott.s* per passare all'applicazione. Si noti che questo URL aggiornato viene ancora risolto. Questo URL è univoco per lo spazio _dev/scott_. Questo URL speciale indica che le richieste inviate a "URL scott" proveranno prima di instradarsi ai servizi nello spazio _dev/scott_ ma, in caso di esito negativo, verrà eseguito il fallback ai servizi nello spazio _dev_.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

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