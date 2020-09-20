---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255812"
---
## <a name="robots933456-in-logs"></a>robots933456 nei log

È possibile che nei log del contenitore venga visualizzato il messaggio seguente:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

È possibile ignorare tale errore. `/robots933456.txt` è un percorso URL fittizio usato dal servizio app per verificare se il contenitore è in grado di gestire le richieste. Una risposta 404 indica semplicemente che il percorso non esiste, ma consente al servizio app di capire che il contenitore è integro e pronto per rispondere alle richieste.

