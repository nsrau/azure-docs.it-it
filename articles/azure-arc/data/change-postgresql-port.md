---
title: Modificare la porta PostgreSQL
description: Modificare la porta su cui è in ascolto il gruppo di server con iperscalabilità di PostgreSQL abilitato per Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328718"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Modificare la porta su cui è in ascolto il gruppo di server 

La modifica della porta è un'operazione di modifica standard del gruppo di server. Per modificare la porta, eseguire il comando seguente:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Si supponga, ad esempio, che il nome del gruppo di server sia _postgres01_ e che sia in ascolto sulla porta _866_. Eseguire il comando seguente:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Verificare che la porta sia stata modificata

Per verificare che la porta sia stata modificata, eseguire il comando seguente per visualizzare la configurazione del gruppo di server:
```console
azdata arc postgres server show -n <server group name>
```

Nell'output del comando esaminare il numero di porta visualizzato per l'elemento "Port" nella sezione "Service" delle specifiche del gruppo di server.
In alternativa, è possibile verificare nell'elemento externalEndpoint della sezione stato delle specifiche del gruppo di server che l'indirizzo IP è seguito dal numero di porta configurato.

Come immagine, se si continua l'esempio precedente, eseguire il comando:
```console
azdata arc postgres server show -n postgres01
```

verrà visualizzata la porta 866 a cui viene fatto riferimento:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
e questo articolo.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Passaggi successivi
- Leggere le informazioni su [come connettersi al gruppo di server](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Per informazioni sulla configurazione di altri aspetti del gruppo di server, vedere la sezione How-to\Manage\Configure & scale della documentazione.
