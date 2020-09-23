---
title: Mostra la configurazione di un gruppo di server con iperscalabilità PostgreSQL abilitata per Arc
titleSuffix: Azure Arc enabled data services
description: Mostra la configurazione di un gruppo di server con iperscalabilità PostgreSQL abilitata per Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ceab9af7e6556b2d957fafce8cd89d4a0daf9508
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940770"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Mostra la configurazione di un gruppo di server con iperscalabilità PostgreSQL abilitata per Arc

Questo articolo illustra come visualizzare la configurazione dei gruppi di server. Questa operazione viene eseguita anticipando alcune domande che potrebbero essere richieste a se stessi e le risposte. Talvolta possono essere presenti diverse risposte valide. Questo articolo illustra le più comuni o più utili. Raggruppa tali domande per tema:

- da un punto di vista Kubernetes
- da un punto di vista dei servizi dati abilitato per Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Da un punto di vista Kubernetes

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Quanti Pod vengono usati da Azure Arc abilitata per l'iperscalabilità di PostgreSQL?

Elenca le risorse di Kubernetes di tipo postgres. Eseguire il comando:

```console
kubectl get postgresqls [-n <namespace name>]
```

L'output di questo comando Mostra l'elenco dei gruppi di server creati. Per ogni, indica il numero di Pod. Ad esempio:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Questo esempio mostra che vengono creati due gruppi di server e ognuno viene eseguito su 3 Pod (1 Coordinator + 2 ruoli di lavoro). Ciò significa che i gruppi di server creati in questo Azure Arc data controller usano 6 POD.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Quali Pod vengono usati dai gruppi di server di scalabilità PostgreSQL abilitati per Azure Arc?

Eseguire:

```console
kubectl get pods [-n <namespace name>]
```

Viene restituito l'elenco dei pod. Verranno visualizzati i pod usati dai gruppi di server in base ai nomi assegnati ai gruppi di server. Ad esempio:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

In questo esempio, i sei pod che ospitano i due gruppi di server creati sono:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Quale Pod del gruppo di server viene utilizzato per il ruolo del gruppo di server?

Qualsiasi nome di Pod con suffisso `-0` rappresenta un nodo coordinatore. Qualsiasi nome di nodo con suffisso in `-x` cui è uguale a 1 o maggiore è un nodo del ruolo di lavoro. Nell'esempio precedente:
- I coordinatori sono: `postgres01-0` , `postgres02-0`
- I ruoli di lavoro sono:,, `postgres01-2` `postgres01-2` `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Qual è lo stato dei Pod?

Eseguire `kubectl get pods` ed esaminare la colonna `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Quali attestazioni del volume permanente (PVC) vengono utilizzate? 

Per comprendere quali PVC vengono usati, oltre a quelli usati per dati, log e backup, eseguire: 

```console
kubectl get pvc [-n <namespace name>]
```

Per impostazione predefinita, il prefisso del nome di un PVC ne indica l'utilizzo:

- `backups-`...: è un PVC usato per i backup
- `data-`...: è il PVC usato per i file di dati
- `logs-`...: è un PVC usato per i file di log delle transazioni/WAL

Ad esempio:

```output
NAME                   STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Da un punto di vista dei servizi dati abilitato per Azure Arc:

* Quanti gruppi di server vengono creati in un controller di dati Arc?
* Quali sono i nomi?
* Quanti nodi di lavoro utilizzano?
* Quale versione di Postgres viene eseguita?

Usare uno dei comandi seguenti.
- **Con kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Ad esempio, produce l'output seguente, in cui ogni riga è un oggetto `servergroup` . La struttura del nome nella visualizzazione seguente è formata come:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   L'output precedente Mostra 2 gruppi di server di Postgres versione 12. Se la versione è Postgres 11, il nome della CRD è invece postgresql-11.arcdata.microsoft.com.

   Ognuno di essi viene eseguito in 3 nodi/pod: 1 coordinatore e 2 ruoli di lavoro.

- **Con azdata:**

Eseguire il comando seguente. L'output Mostra informazioni simili a quelle visualizzate da kubectl:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Quantità di memoria e di Vcore in uso e quali estensioni sono state create per un gruppo?

Eseguire uno dei comandi seguenti

**Con Kubectl:**

Usare kubectl per descrivere le risorse postgres. A tale scopo, è necessario il relativo tipo (nome della risorsa Kubernetes (CRD) per la versione Postgres corrispondente come illustrato in precedenza) e il nome del gruppo di server.
Il formato generale del comando è il seguente:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Ad esempio:

```console
kubectl describe postgresql-12/postgres02
```

Questo comando Mostra la configurazione del gruppo di server:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Backups:
    Delta Minutes:  3
    Full Minutes:   10
    Tiers:
      Retention:
        Maximums:
          6
          512MB
        Minimums:
          3
      Storage:
        Volume Size:  1Gi
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

Si espostano alcuni punti di interesse specifici nella descrizione della `servergroup` figura precedente. Che cosa indica questo gruppo di server?

- È della versione 12 di Postgres: 
   > Tipo         `postgresql-12`
- È stato creato durante il mese di agosto 2020:
   > Timestamp di creazione:  `2020-08-31T21:01:07Z`
- In questo gruppo di server sono state create due estensioni Postgres: `citus` e `pg_stat_statements`
   > Motore: Extensions: Nome:  `citus` Nome:  `pg_stat_statements`
- USA due nodi del ruolo di lavoro
   > Ridimensiona: partizionamenti:  `2`
- Si prevede di usare 1 CPU/vCore e 512 MB di RAM per ogni nodo. Utilizzerà più di 4 CPU/Vcore e 1024MB di memoria:
   > Pianificazione: impostazione predefinita: risorse: limiti: CPU: 4 memoria: 1024Mi richieste: CPU: 1 memoria: 512Mi
 - È disponibile per le query e non presenta alcun problema. Tutti i nodi sono operativi:
   > Stato:... Pod pronti: 3/3 stato: pronto

**Con azdata:**

Il formato generale del comando è:

```console
azdata arc postgres server show -n <server group name>
```

Ad esempio:

```console
azdata arc postgres server show -n postgres02
```

Restituisce l'output seguente in un formato e contenuto molto simile a quello restituito da kubectl.

```output
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "backups": {
      "deltaMinutes": 3,
      "fullMinutes": 10,
      "tiers": [
        {
          "retention": {
            "maximums": [
              "6",
              "512MB"
            ],
            "minimums": [
              "3"
            ]
          },
          "storage": {
            "volumeSize": "1Gi"
          }
        }
      ]
    },
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sui concetti relativi all'iperscalabilità di PostgreSQL abilitata per Azure Arc](concepts-distributed-postgres-hyperscale.md)
- [Leggere le informazioni su come aumentare il livello di prestazioni (aggiungere nodi di lavoro) a un gruppo di server](scale-out-postgresql-hyperscale-server-group.md)
- [Leggere le informazioni su come eseguire la scalabilità verticale (aumento o riduzione della memoria e/o VCore) di un gruppo di server](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Ulteriori informazioni sulla configurazione dell'archiviazione](storage-configuration.md)
- [Informazioni su come monitorare un'istanza di database](monitor-grafana-kibana.md)
- [Usare le estensioni di PostgreSQL nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Configurare la sicurezza per il gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc](configure-security-postgres-hyperscale.md)
