---
title: Configurare l'istanza gestita di SQL Azure Arc abilitata
description: Configurare l'istanza gestita di SQL Azure Arc abilitata
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940635"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configurare l'istanza gestita di SQL Azure Arc abilitata

Questo articolo illustra come configurare l'istanza gestita di SQL Azure Arc abilitata.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Configurare le risorse per il Istanza gestita SQL abilitato per Azure Arc

### <a name="configure-using-azdata"></a>Configurare con azdata

È possibile modificare la configurazione delle istanze gestite di SQL Azure Arc abilitate con l'interfaccia della riga di comando `azdata` . Eseguire il comando seguente per visualizzare le opzioni di configurazione. 

```
azdata arc sql mi edit --help
```

Nell'esempio seguente vengono impostati i limiti e le richieste di memoria e di core CPU.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Per visualizzare le modifiche apportate all'istanza gestita di SQL, è possibile usare i comandi seguenti per visualizzare il file YAML di configurazione:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Configurare le opzioni del server

È possibile configurare le impostazioni di configurazione del server per l'istanza di SQL gestito abilitata per Azure Arc dopo la creazione. Questo articolo descrive come configurare impostazioni come l'abilitazione o la disabilitazione dell'agente MSSQL, abilitare flag di traccia specifici per gli scenari di risoluzione dei problemi.

Per cambiare una qualsiasi di queste impostazioni, eseguire la procedura seguente:

1. Creare un file `mssql-custom.conf` personalizzato che includa le impostazioni interessate. Nell'esempio seguente viene abilitato SQL Agent e viene abilitato il flag di traccia 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Copiare il file `mssql-custom.conf` in `/var/opt/mssql` nel contenitore `mssql-miaa` nel pod `master-0`. Sostituire `<namespaceName>` con il nome del cluster Big Data.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Riavviare l'istanza di SQL Server.  Sostituire `<namespaceName>` con il nome del cluster Big Data.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Limitazioni note**
- La procedura precedente richiede autorizzazioni di amministratore del cluster Kubernetes
- Questa operazione è soggetta a modifiche durante l'anteprima
