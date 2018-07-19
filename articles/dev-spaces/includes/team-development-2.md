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
ms.openlocfilehash: 2f2177b289ba9d13ba635bddcd61748e1e8ab0d1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39063106"
---
### <a name="run-the-service"></a>Eseguire il servizio

1. Premere F5 (o digitare `azds up` nella finestra del terminale) per eseguire il servizio. Il servizio verrà automaticamente eseguito nello spazio appena selezionato `default/scott`. 
1. È possibile confermare che il servizio è in esecuzione nel proprio spazio eseguendo nuovamente `azds list-up`. In primo luogo, si noterà che è in esecuzione un'istanza di `mywebapi` nello spazio `default/scott` (la versione in esecuzione in `default` è ancora in esecuzione ma non è elencata). In secondo luogo, l'URL del punto di accesso `webfrontend` è preceduto dal prefisso "scott.s". Questo URL è univoco per lo spazio `default/scott`. Questo URL speciale indica che le richieste inviate a "URL scott" tenteranno prima di instradarsi ai servizi nello spazio `default/scott` ma, in caso di esito negativo, verrà eseguito il fallback ai servizi nello spazio `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Questa funzionalità incorporata di Azure Dev Spaces consente di testare codice in uno spazio condiviso senza richiedere a ogni sviluppatore ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede al codice dell'app di inoltrare le intestazioni di propagazione, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-in-a-space"></a>Test del codice in uno spazio
Per testare la nuova versione di `mywebapi` con `webfrontend`, aprire il browser all'URL di punto di accesso pubblico per `webfrontend` e passare alla pagina delle informazioni. Dovrebbe essere visualizzato il nuovo messaggio.

A questo punto, rimuovere parte dell'URL "scott.s." e aggiornare il browser. Verrà visualizzato il comportamento precedente con la versione di `mywebapi` in esecuzione in `default`
