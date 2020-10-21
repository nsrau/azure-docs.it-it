---
title: Integrare Apache Kafka connettersi all'hub eventi di Azure (anteprima) con Debezium for Change Data Capture
description: Questo articolo fornisce informazioni su come usare Debezium con hub eventi di Azure per Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: e4bd6cdf6d3a5dc30b90abc5094202360181ae0b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318511"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>Integrare il supporto di Apache Kafka Connect nell'hub eventi di Azure (anteprima) con Debezium for Change Data Capture

**Change Data Capture (CDC)** è una tecnica utilizzata per tenere traccia delle modifiche a livello di riga nelle tabelle di database in risposta alle operazioni di creazione, aggiornamento ed eliminazione. [Debezium](https://debezium.io/) è una piattaforma distribuita che si basa sulle funzionalità di Change Data Capture disponibili in database diversi, ad esempio la [decodifica logica in PostgreSQL](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html). Fornisce un set di [connettori Kafka Connect](https://debezium.io/documentation/reference/1.2/connectors/index.html) che consentono di accedere alle modifiche a livello di riga nelle tabelle di database e di convertirle in flussi di eventi che vengono quindi inviati a [Apache Kafka](https://kafka.apache.org/).

Questa esercitazione illustra come configurare un sistema basato su Change Data Capture in Azure con [Hub eventi di Azure](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (per Kafka), [database di Azure per PostgreSQL](../postgresql/overview.md) e Debezium. Verrà usato il [connettore Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) per trasmettere le modifiche del database da PostgreSQL a Kafka in hub eventi di Azure

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Configurare e configurare database di Azure per PostgreSQL
> * Configurare ed eseguire Kafka Connect con Debezium PostgreSQL Connector
> * Change Data Capture test
> * Opzionale Utilizzare gli eventi di modifica dati con un `FileStreamSink` connettore

## <a name="pre-requisites"></a>Prerequisiti
Per completare questa procedura dettagliata, sarà necessario:

- Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/).
- Linux/MacOS
- Kafka (versione 1.1.1, versione di Scala 2.11), disponibile su [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Leggere con attenzione l'articolo introduttivo [Hub eventi per Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Per l'invio e la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Per istruzioni su come creare uno spazio dei nomi e un hub eventi, vedere [creazione di un hub eventi](event-hubs-create.md) . Ottenere la stringa di connessione di Hub eventi e il nome di dominio completo (FQDN) da usare successivamente. Per istruzioni, vedere [Ottenere una stringa di connessione ad Hub eventi](event-hubs-get-connection-string.md). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Configurare e configurare database di Azure per PostgreSQL
[Database di Azure per PostgreSQL](../postgresql/overview.md) è un servizio di database relazionale basato sulla versione community del motore di database PostgreSQL open source ed è disponibile in due opzioni di distribuzione: server singolo e iperscalabilità (CITUS). [Seguire queste istruzioni](../postgresql/quickstart-create-server-database-portal.md) per creare un database di Azure per il server PostgreSQL usando il portale di Azure. 

## <a name="setup-and-run-kafka-connect"></a>Configurare ed eseguire Kafka Connect
In questa sezione vengono trattati gli argomenti seguenti:

- Installazione del connettore Debezium
- Configurazione di Kafka Connect per hub eventi
- Avviare il cluster Kafka Connect con Debezium Connector

### <a name="download-and-setup-debezium-connector"></a>Scaricare e configurare Debezium Connector
Seguire le istruzioni più recenti nella [documentazione di Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) per scaricare e configurare il connettore.

- Scaricare l'archivio di plug-in del connettore. Ad esempio, per scaricare `1.2.0` la versione del connettore, usare questo collegamento- https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Estrarre i file JAR e copiarli nel plug-in di [Kafka Connect. Path](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Configurare Kafka Connect per Hub eventi
Quando si reindirizza la velocità effettiva di Kafka Connect da Kafka a Hub eventi, è necessaria una riconfigurazione minima.  Il codice di esempio `connect-distributed.properties` seguente illustra come configurare Connect per autenticare e comunicare con l'endpoint Kafka in Hub eventi:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

### <a name="run-kafka-connect"></a>Eseguire Kafka Connect
In questo passaggio un ruolo di lavoro Kafka Connect viene avviato localmente in modalità distribuita, usando Hub eventi per gestire lo stato del cluster.

1. Salvare il file `connect-distributed.properties` in locale.  Assicurarsi di sostituire tutti i valori racchiusi tra parentesi graffe.
2. Passare alla posizione della versione di Kafka nel computer.
3. Eseguire `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` e attendere l'avvio del cluster.

> [!NOTE]
> Kafka Connect usa l'API Kafka AdminClient per creare automaticamente argomenti con le configurazioni consigliate, inclusa la compattazione. Un rapido controllo dello spazio dei nomi nel portale di Azure conforma che gli argomenti interni del ruolo di lavoro Connect sono stati creati automaticamente.
>
> Gli argomenti interni di Kafka Connect **devono usare la compattazione**.  Il team di Hub eventi non è responsabile della correzione di configurazioni non corrette se gli argomenti interni di Kafka Connect non sono configurati correttamente.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Configurare e avviare il connettore origine PostgreSQL Debezium

Creare un file di configurazione ( `pg-source-connector.json` ) per il connettore di origine PostgreSQL: sostituire i valori in base all'istanza di Azure PostgreSQL.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` attribute è un nome logico che identifica e fornisce uno spazio dei nomi per il server di database PostgreSQL o il cluster in corso di monitoraggio. Per informazioni dettagliate, vedere la [documentazione di Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

Per creare un'istanza del connettore, usare l'endpoint dell'API REST Connect di Kafka:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Per controllare lo stato del connettore:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Change Data Capture test
Per visualizzare Change Data Capture in azione, è necessario creare/aggiornare/eliminare i record nel database PostgreSQL di Azure.

Per iniziare, connettersi al database PostgreSQL di Azure (l'esempio seguente usa [PSQL](https://www.postgresql.org/docs/12/app-psql.html))

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Creare una tabella e inserire i record**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Il connettore dovrebbe ora rientrare in azione e inviare gli eventi dei dati di modifica a un argomento di hub eventi con i seguenti elementi na, e `my-server.public.todos` , supponendo `my-server` di avere come valore per `database.server.name` e `public.todos` è la tabella di cui si stanno verificando le modifiche (in base alla `table.whitelist` configurazione)

**Controllare l'argomento di hub eventi**

Analizzare il contenuto dell'argomento per verificare che tutto funzioni come previsto. Nell'esempio seguente viene usato [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , ma è anche possibile [creare un consumer usando una delle opzioni elencate di](apache-kafka-developer-guide.md) seguito.

Creare un file denominato `kafkacat.conf` con il contenuto seguente:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Aggiornare `metadata.broker.list` `sasl.password` gli attributi e in `kafkacat.conf` come per le informazioni di hub eventi. 

In un terminale diverso avviare un consumer:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Verranno visualizzati i payload JSON che rappresentano gli eventi dei dati delle modifiche generati in PostgreSQL in risposta alle righe appena aggiunte alla `todos` tabella. Ecco un frammento del payload:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

L'evento è costituito da `payload` insieme al relativo `schema` (omesso per brevità). Nella `payload` sezione si noti che l'operazione di creazione ( `"op": "c"` ) è rappresentata. `"before": null` indica che si tratta di una nuova `INSERT` riga, `after` fornisce valori per le colonne nella riga, `source` fornisce i metadati dell'istanza di PostgreSQL da dove è stato selezionato questo evento e così via.

È possibile provare a eseguire la stessa operazione anche con le operazioni Update o DELETE e analizzare gli eventi dei dati delle modifiche. Ad esempio, per aggiornare lo stato dell'attività `configure and install connector` (presupponendo che `id` sia `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Opzionale Installare FileStreamSink Connector
Ora che tutte le `todos` modifiche apportate alla tabella sono state acquisite nell'argomento Hub eventi, si userà il connettore FileStreamSink, disponibile per impostazione predefinita in Kafka Connect, per utilizzare questi eventi.

Creare un file di configurazione ( `file-sink-connector.json` ) per il connettore: sostituire l' `file` attributo in base all'file System

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Per creare il connettore e controllarne lo stato:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Inserire/aggiornare/eliminare i record del database e monitorare i record nel file di sink di output configurato:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Pulizia
Kafka Connect crea argomenti di Hub eventi in cui archiviare le configurazioni, gli offset e lo stato che rimangono persistenti anche dopo che il cluster Connect viene disattivato. A meno che tale persistenza non sia voluta, è consigliabile eliminare questi argomenti. È anche possibile eliminare l' `my-server.public.todos` Hub eventi creato nel corso di questa procedura dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)