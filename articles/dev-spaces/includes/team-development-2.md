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
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367373"
---
### <a name="run-the-service"></a>Eseguire il servizio

1. Premere F5 (o digitare `azds up` nella finestra del terminale) per eseguire il servizio. Il servizio verrà automaticamente eseguito nello spazio appena selezionato `scott`. 
1. È possibile confermare che il servizio è in esecuzione nel proprio spazio eseguendo nuovamente `azds resource list`. In primo luogo, si noterà che è in esecuzione un'istanza di `mywebapi` nello spazio `scott` (la versione in esecuzione in `default` è ancora in esecuzione ma non è elencata). In secondo luogo, l'URL del punto di accesso `webfrontend` è preceduto dal prefisso *scott.s.*. Questo URL è univoco per lo spazio `scott` e indica che le richieste inviate a "URL scott" tenteranno prima di instradarsi ai servizi nello spazio `scott` e verrà eseguito il fallback ai servizi nello spazio `default`.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Questa funzionalità incorporata di Azure Dev Spaces consente di testare codice end-to-end in un ambiente condiviso senza richiedere a ogni sviluppatore ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede al codice dell'app di inoltrare le intestazioni di propagazione, come illustrato nel passaggio precedente di questa Guida.

## <a name="test-code-in-a-space"></a>Test del codice in uno spazio
Per testare la nuova versione di `mywebapi` in combinazione con `webfrontend`, aprire il browser all'URL del punto di accesso pubblico per WebFrontEnd e passare alla pagina delle informazioni. Dovrebbe essere visualizzato il nuovo messaggio.

A questo punto, rimuovere parte dell'URL "scott.s." e aggiornare il browser. Verrà visualizzato il comportamento precedente mostrato dalla versione di `mywebapi` in esecuzione in `default`