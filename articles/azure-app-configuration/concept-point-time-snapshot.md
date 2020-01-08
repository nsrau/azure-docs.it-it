---
title: Snapshot temporizzato della configurazione app Azure
description: Panoramica del funzionamento dello snapshot temporizzato in Configurazione app di Azure
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495252"
---
# <a name="point-in-time-snapshot"></a>Snapshot temporizzato

Configurazione app di Azure conserva i record dei momenti precisi in cui una nuova coppia chiave-valore viene creata e quindi modificata. Questi record formano una sequenza temporale completa relativa alle modifiche delle coppie chiave-valore. Un archivio di Configurazione app consente di ricostruire la cronologia di qualsiasi coppia chiave-valore e di risalire a un valore precedente in un momento qualsiasi, fino a quello attuale. Questa funzionalità consente di "viaggiare indietro nel tempo" e di recuperare una coppia chiave-valore precedente. È ad esempio possibile ottenere le impostazioni di configurazione del giorno precedente, subito prima della distribuzione più recente, in modo da poter recuperare una configurazione precedente ed eseguire il rollback dell'applicazione.

## <a name="key-value-retrieval"></a>Recupero di coppie chiave-valore

Per recuperare coppie chiave-valore precedenti, specificare il momento di acquisizione dello snapshot delle coppie chiave-valore nell'intestazione HTTP di una chiamata all'API REST. Ad esempio:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Attualmente, il servizio Configurazione app conserva sette giorni di cronologia delle modifiche.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
