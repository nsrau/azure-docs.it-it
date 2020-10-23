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
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311117"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configurare l'istanza gestita di SQL Azure Arc abilitata

Questo articolo illustra come configurare l'istanza gestita di SQL Azure Arc abilitata.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Configure resources

### <a name="configure-using-azure-data-cli-azdata"></a>Configurare con [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

È possibile modificare la configurazione delle istanze gestite di SQL Azure Arc abilitate con il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Eseguire il comando seguente per visualizzare le opzioni di configurazione. 

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
