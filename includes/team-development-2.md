---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 8580e19f1955becb2cfaee13742054af1a9edb7f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410463"
---
### <a name="run-the-service"></a>Eseguire il servizio

1. Premere F5 (o digitare `azds up` nella finestra del terminale) per eseguire il servizio. Il servizio verrà automaticamente eseguito nello spazio appena selezionato `default/scott`. 
1. È possibile confermare che il servizio è in esecuzione nel proprio spazio eseguendo nuovamente `azds list-up`. In primo luogo, si noterà che è in esecuzione un'istanza di `mywebapi` nello spazio `default/scott` (la versione in esecuzione in `default` è ancora in esecuzione ma non è elencata). Se si esegue `azds list-uris`, si noterà che l'URL del punto di accesso per `webfrontend` è preceduto dal prefisso "scott.s.". Questo URL è univoco per lo spazio `default/scott`. Questo URL speciale indica che le richieste inviate a "URL scott" tenteranno prima di instradarsi ai servizi nello spazio `default/scott` ma, in caso di esito negativo, verrà eseguito il fallback ai servizi nello spazio `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Questa funzionalità incorporata di Azure Dev Spaces consente di testare codice in uno spazio condiviso senza richiedere a ogni sviluppatore ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede al codice dell'app di inoltrare le intestazioni di propagazione, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-in-a-space"></a>Test del codice in uno spazio
Per testare la nuova versione di `mywebapi` con `webfrontend`, aprire il browser all'URL di punto di accesso pubblico per `webfrontend` e passare alla pagina delle informazioni. Dovrebbe essere visualizzato il nuovo messaggio.

A questo punto, rimuovere parte dell'URL "scott.s." e aggiornare il browser. Verrà visualizzato il comportamento precedente con la versione di `mywebapi` in esecuzione in `default`.
