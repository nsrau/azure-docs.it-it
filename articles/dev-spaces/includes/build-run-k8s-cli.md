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
ms.openlocfilehash: b18cfce173da562aa7cffa48f336ff623c868f21
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129269"
---
## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes
Eseguire il codice. Nella finestra del terminale, eseguire questo comando dalla **cartella radice del codice**, webfrontend:

```cmd
azds up
```

Monitorare l'output del comando; si noteranno varie cose importanti man mano che avanza:
- Il codice sorgente viene sincronizzato con lo spazio di sviluppo in Azure.
- Un'immagine del contenitore viene compilata in Azure, come specificato dagli asset di Docker nella cartella del codice.
- Vengono creati oggetti Kubernetes che usano l'immagine del contenitore come specificato dal grafico Helm nella cartella del codice.
- Vengono visualizzate informazioni sull'endpoint del contenitore. In questo caso, è previsto un URL HTTP pubblico.
- Presupponendo che le fasi precedenti siano state completate correttamente, si dovrebbe iniziare a visualizzare l'output `stdout` (e `stderr`) all'avvio del contenitore.

> [!Note]
> Questi passaggi richiederanno più tempo la prima volta che viene eseguito il comando `up`, ma le esecuzioni successive dovrebbero essere più rapide.

### <a name="test-the-web-app"></a>Testare l'app Web
Analizzare l'output della console per informazioni sull'URL pubblico che è stato creato con il comando `up`. Sarà nel formato: 

`Service 'webfrontend' port 'http' is available at <url>` 

Aprire l'URL in una finestra del browser; si dovrebbe visualizzare il caricamento dell'applicazione Web. Durante l'esecuzione del contenitore, l'output `stdout` e `stderr` vengono trasmessi nella finestra del terminale.

> [!Note]
> Alla prima esecuzione, possono essere necessari alcuni minuti prima che il DNS pubblico sia pronto. Se l'URL pubblico non viene risolto, è possibile usare l'URL http://localhost:<portnumber> alternativo visualizzato nell'output della console. Se si usa l'URL localhost, potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito in AKS. Per motivi di praticità e per semplificare l'interazione con il servizio nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure. È possibile tornare in seguito per tentare di usare l'URL pubblico quando il record DNS sarà pronto.
