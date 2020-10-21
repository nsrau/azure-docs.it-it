---
title: Configurare i parametri del server del motore Postgres per il gruppo di server con iperscalabilità PostgreSQL in Azure Arc
titleSuffix: Azure Arc enabled data services
description: Configurare i parametri del server del motore Postgres per il gruppo di server con iperscalabilità PostgreSQL in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311054"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Configurare le impostazioni del motore di database per PostgreSQL Hyperscale abilitato per Azure Arc

Questo documento descrive la procedura per configurare le impostazioni del motore di database del gruppo di server di PostgreSQL Hyperscale su valori personalizzati, ovvero non predefiniti. Per informazioni dettagliate sui parametri del motore di database che è possibile impostare e sul valore predefinito, vedere la documentazione di PostgreSQL [qui](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> L'anteprima non supporta l'impostazione dei seguenti parametri: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Sintassi

Il formato generale del comando per configurare le impostazioni del motore di database è:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>Mostra valori personalizzati correnti

### <a name="with-azure-data-cli-azdata-command"></a>Con [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] comando

```console
azdata arc postgres server show -n <server group name>
```

Ad esempio:

```console
azdata arc postgres server show -n postgres01
```

Questo comando restituisce le specifiche del gruppo di server in cui verranno visualizzati i parametri impostati. Se non è presente alcuna sezione engine\settings, significa che tutti i parametri sono in esecuzione sul valore predefinito:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>Con comando kubectl

Attenersi ai passaggi seguenti.

1. Recuperare il tipo di definizione di risorsa personalizzata per il gruppo di server

   Eseguire:

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   Ad esempio:

   ```console
   azdata arc postgres server show -n postgres01
   ```

   Questo comando restituisce le specifiche del gruppo di server in cui verranno visualizzati i parametri impostati. Se non è presente alcuna sezione engine\settings, significa che tutti i parametri sono in esecuzione sul valore predefinito:

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   Nei risultati di output cercare il campo e accantonare `kind` il valore, ad esempio: `postgresql-12` .

2. Descrizione della risorsa personalizzata Kubernetes corrispondente al gruppo di server 

   Il formato generale del comando è:

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   Ad esempio:

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   Se sono presenti valori personalizzati impostati per le impostazioni del motore, verranno restituiti. Ad esempio:

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   Se non sono stati impostati valori personalizzati per nessuna delle impostazioni del motore, la sezione impostazioni del motore di `resultset` sarà vuota, ad esempio:

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>Impostare valori personalizzati per le impostazioni del motore

I comandi seguenti consentono di impostare i parametri del nodo coordinatore e i nodi di lavoro dell'iperscalabilità PostgreSQL sugli stessi valori. Non è ancora possibile impostare i parametri per ogni ruolo nel gruppo di server. Ovvero non è ancora possibile configurare un determinato parametro in un oggetto specifico nel nodo coordinatore e in un altro valore per i nodi del ruolo di lavoro.

### <a name="set-a-single-parameter"></a>Imposta un singolo parametro

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Ad esempio:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>Impostare più parametri con un unico comando

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Ad esempio:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>Reimposta il valore predefinito di un parametro

Per reimpostare il valore predefinito di un parametro, impostarlo senza indicare un valore. 

Ad esempio:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>Reimposta tutti i parametri sui valori predefiniti

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Ad esempio:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Considerazioni speciali

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Imposta un parametro il cui valore contiene una virgola, uno spazio o un carattere speciale

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Ad esempio:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Passare una variabile di ambiente in un valore di parametro

La variabile di ambiente deve essere racchiusa tra "''" in modo che non venga risolta prima di essere impostata.

Ad esempio: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [ridimensionamento (aggiunta di nodi di lavoro)](scale-out-postgresql-hyperscale-server-group.md) al gruppo di server
- Per informazioni su come [aumentare o ridurre la scalabilità (aumento/riduzione della memoria/VCore)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) del gruppo di server
