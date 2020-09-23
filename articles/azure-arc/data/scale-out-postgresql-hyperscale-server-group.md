---
title: Gruppo di server con scalabilità orizzontale di database di Azure per PostgreSQL
description: Gruppo di server con scalabilità orizzontale di database di Azure per PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e267a30d6f73b48f825c4b61b3bc1106133b8cdf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938096"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Scalabilità orizzontale del gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc aggiungendo più nodi di lavoro
Questo documento illustra come aumentare il livello di scalabilità orizzontale di un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc. Questa operazione viene eseguita tramite uno scenario. **Se non si vuole eseguire lo scenario e si vuole semplicemente leggere le informazioni su come aumentare la scalabilità orizzontale, passare al paragrafo [scale out (scalabilità orizzontale](#scale-out)**).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Introduzione
Se si ha già familiarità con il modello di scalabilità di Azure Arc abilitata per l'iperscalabilità PostgreSQL o con la scalabilità di database di Azure per PostgreSQL (CITUS), è possibile ignorare questo paragrafo. In caso contrario, è consigliabile iniziare leggendo questo modello di scalabilità nella pagina della documentazione relativa alla scalabilità di database di Azure per PostgreSQL (CITUS). Il database di Azure per PostgreSQL (CITUS) è la stessa tecnologia ospitata come servizio in Azure (piattaforma come servizio anche noto come PAAS) anziché essere offerta come parte di Azure Arc Enabled Data Services:
- [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
- [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
- [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
- [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* Nei documenti precedenti ignorare le sezioni accedere al **portale di Azure**& **creare un database di Azure per PostgreSQL-iperscalabilità (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

## <a name="scenario"></a>Scenario
Questo scenario si riferisce al gruppo di server di iperscala PostgreSQL che è stato creato come esempio nella documentazione relativa alla [creazione di un gruppo di server con iperscalabilità in Azure Arc Enabled PostgreSQL](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Dati del test di carico
Lo scenario usa un campione di dati GitHub disponibili pubblicamente, disponibile nel [sito Web CITUS data](https://www.citusdata.com/) (i dati di CITUS sono parte di Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Connettersi al gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

##### <a name="list-the-connection-information"></a>Elencare le informazioni di connessione
Connettersi al gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc ottenendo prima le informazioni di connessione: il formato generale di questo comando è
```console
azdata arc postgres endpoint list -n <server name>
```
Ad esempio:
```console
azdata arc postgres endpoint list -n postgres01
```

Output di esempio:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Connettersi con lo strumento client preferito.

Eseguire la query seguente per verificare che siano presenti due o più nodi di lavoro con iperscalabilità, ognuno corrispondente a un pod Kubernetes:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Creazione di uno schema di esempio
Creare due tabelle eseguendo la query seguente:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB è il tipo di dati JSON in formato binario in PostgreSQL. Archivia uno schema flessibile in una singola colonna e con PostgreSQL. Lo schema includerà un indice GIN per indicizzare ogni chiave e valore al suo interno. Con un indice GIN, diventa veloce e facile eseguire query con varie condizioni direttamente sul payload. Quindi, creiamo un paio di indici prima di caricare i dati:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Per partizionare le tabelle standard, eseguire una query per ogni tabella. Specificare la tabella di cui si vuole eseguire la partizione e la chiave su cui si vuole eseguire il partizionamento. La tabella degli eventi e degli utenti verrà partizionata in user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Caricare dati di esempio
Carica i dati con la copia... DAL PROGRAMMA:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Eseguire una query sui dati
E ora misurare il tempo impiegato da una semplice query con due nodi:

```sql
SELECT COUNT(*) FROM github_events;
```
Prendere nota del tempo di esecuzione della query.


## <a name="scale-out"></a>Aumentare il numero di istanze
Il formato generale del comando di scalabilità orizzontale è:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

Ad esempio, aumentare il numero di nodi del ruolo di lavoro da 2 a 4 eseguendo il comando seguente:
```console
azdata arc postgres server edit -n postgres01 -w 4
```

Quando si aggiungono nodi, verrà visualizzato uno stato in sospeso per il gruppo di server. Ad esempio:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Quando i nodi sono disponibili, il ribilanciamento delle partizioni con iperscalabilità viene eseguito automaticamente e ridistribuisce i dati nei nuovi nodi. L'operazione di scalabilità orizzontale è un'operazione online. Mentre i nodi vengono aggiunti e i dati vengono ridistribuiti tra i nodi, i dati rimangono disponibili per le query.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Verificare la nuova forma del gruppo di server (facoltativo)
Usare uno dei metodi seguenti per verificare che il gruppo di server stia ora usando i nodi di lavoro aggiuntivi aggiunti.

#### <a name="with-azdata"></a>Con azdata:
Eseguire il comando:
```console
azdata arc postgres server list
```

Restituisce l'elenco dei gruppi di server creati nello spazio dei nomi e indica il numero di nodi del ruolo di lavoro. Ad esempio:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Con kubectl:
Eseguire il comando:
```console
kubectl get postgresql-12
```

Restituisce l'elenco dei gruppi di server creati nello spazio dei nomi e indica il numero di nodi del ruolo di lavoro. Ad esempio:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> **Nota:** Se è stato creato un gruppo di server della versione 11 di PostgreSQL invece di 12, eseguire invece il comando seguente: _kubectl Get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>Con una query SQL:
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query seguente:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Tornare allo scenario

Se si desidera confrontare il tempo di esecuzione della query SELECT Count rispetto al set di dati di esempio, utilizzare la stessa query count. Può essere usato nei quattro nodi del ruolo di lavoro, senza modifiche nell'istruzione SQL.

```sql
SELECT COUNT(*) FROM github_events;
```
Prendere nota del tempo di esecuzione.


> [!NOTE]
> A seconda dell'ambiente, ad esempio se è stato distribuito il gruppo di server di prova con `kubeadm` in una macchina virtuale a nodo singolo, è possibile che venga visualizzato un modesto miglioramento del tempo di esecuzione. Per avere un'idea più approfondita del tipo di miglioramento delle prestazioni che è possibile raggiungere con l'iperscalabilità di PostgreSQL abilitata per Azure Arc, guardare i brevi video seguenti:
>* [HTAP ad alte prestazioni con Azure PostgreSQL iperscalabile (CITUS)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Creazione di applicazioni HTAP con Python & l'iperscalabilità di Azure PostgreSQL (CITUS)](https://www.youtube.com/watch?v=YDT8_riLLs0)

> La versione di anteprima non supporta la scalabilità in. Ad esempio, non è ancora possibile ridurre il numero di nodi del ruolo di lavoro. Se è necessario eseguire questa operazione, è necessario estrarre o eseguire il backup dei dati, eliminare il gruppo di server, creare un nuovo gruppo di server con meno nodi di lavoro e quindi importare i dati.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come eseguire la [scalabilità verticale (memoria, VCore) del gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Leggere le informazioni su come impostare i parametri del server nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc
- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per la distribuzione dei dati in più nodi di iperscala PostgreSQL e per sfruttare tutte le potenzialità del database di Azure per la scalabilità Ipergres. :
    * [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
    * [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* Nei documenti precedenti ignorare le sezioni accedere al **portale di Azure**& **creare un database di Azure per PostgreSQL-iperscalabilità (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Espansione di attestazioni di volume permanenti](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
