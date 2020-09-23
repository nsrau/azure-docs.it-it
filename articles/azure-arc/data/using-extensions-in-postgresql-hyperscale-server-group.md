---
title: Usare le estensioni di PostgreSQL
description: Usare le estensioni di PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19b2ec283619df0cc8d3c880cb2df6f53f6fb332
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939102"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usare le estensioni di PostgreSQL nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

PostgreSQL è al meglio quando viene usato con le estensioni. Infatti, un elemento chiave della nostra funzionalità di iperscalabilità è l' `citus` estensione fornita da Microsoft che viene installata per impostazione predefinita, che consente a Postgres di partizionare in modo trasparente i dati tra più nodi.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Elenco delle estensioni
Oltre alle estensioni in [`contrib`](https://www.postgresql.org/docs/12/contrib.html) , l'elenco di estensioni presenti nei contenitori del gruppo di server di scalabilità di PostgreSQL abilitato per Azure Arc è il seguente:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2

Questo elenco è in continua evoluzione e gli aggiornamenti verranno pubblicati in questo documento. Non è ancora possibile aggiungere estensioni oltre a quelle elencate in precedenza.

In questa guida verrà usato uno scenario per usare due di queste estensioni:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Gestire le estensioni

### <a name="enable-extensions"></a>Abilita estensioni
Questo passaggio non è necessario per le estensioni che fanno parte di `contrib` .
Il formato generale del comando per abilitare le estensioni è il seguente:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Abilitare un'estensione al momento della creazione di un gruppo di server:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Abilitare un'estensione in un'istanza già esistente:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Ottenere l'elenco di estensioni abilitate:
Eseguire uno dei comandi seguenti.

##### <a name="with-azdata"></a>Con azdata
```console
azdata arc postgres server show -n <server group name>
```
Scorrere l'output e notare le sezioni engine\extensions nelle specifiche del gruppo di server. Ad esempio:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>Con kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Scorrere l'output e notare le sezioni engine\extensions nelle specifiche del gruppo di server. Ad esempio:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Crea estensioni:
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Ottenere l'elenco di estensioni creato nel gruppo di server:
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Eliminare un'estensione dal gruppo di server:
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Usare PostGIS e le estensioni Pg_cron

### <a name="the-postgis-extension"></a>Estensione PostGIS

È possibile abilitare l'estensione PostGIS in un gruppo di server esistente o crearne uno nuovo con l'estensione già abilitata:

**Abilitazione di un'estensione al momento della creazione di un gruppo di server:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Abilitazione di un'estensione in un'istanza già esistente:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Per verificare quali estensioni sono installate, usare il seguente comando PostgreSQL standard dopo la connessione all'istanza con lo strumento client PostgreSQL preferito, ad esempio Azure Data Studio:
```console
select * from pg_extension;
```

Per un esempio PostGIS, ottenere prima di tutto i [dati di esempio](http://duspviz.mit.edu/tutorials/intro-postgis/) dal dipartimento del mit di studi urbani & pianificazione. Potrebbe essere necessario eseguire `apt-get install unzip` per installare unzip quando si usa la macchina virtuale per il test.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Connettersi al database e creare l'estensione PostGIS:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Se si desidera utilizzare una delle estensioni nel `postgis` pacchetto (ad esempio,,, `postgis_raster` .. `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` .) è necessario innanzitutto creare l'estensione PostGIS e quindi creare l'altra estensione. Ad esempio: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

E creare lo schema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

A questo punto, è possibile combinare PostGIS con la funzionalità di scalabilità orizzontale, rendendo distribuita la tabella coffee_shops:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Caricare alcuni dati:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E riempire il `geom` campo con la latitudine e la longitudine codificate correttamente nel tipo di `geometry` dati PostGIS:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

A questo punto è possibile elencare le caffetterie più vicine al MIT (77 Massachusetts Ave a 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Estensione pg_cron

`pg_cron`Per abilitare il gruppo di server PostgreSQL, oltre a PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Si noti che in questo modo verranno riavviati i nodi e verranno installate le estensioni aggiuntive, che potrebbero richiedere 2-3 minuti.

È ora possibile connettersi di nuovo e creare l' `pg_cron` estensione:

```sql
CREATE EXTENSION pg_cron;
```

A scopo di test, è possibile creare una tabella `the_best_coffee_shop` che accetta un nome casuale dalla `coffee_shops` tabella precedente e imposta il contenuto della tabella:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

È possibile utilizzare `cron.schedule` più istruzioni SQL, per ottenere un nome di tabella casuale (si noti l'utilizzo di una tabella temporanea per archiviare i risultati di una query distribuita) e archiviarlo in `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

E ora, una volta al minuto, otterremo un nome diverso:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Per informazioni dettagliate sulla sintassi, vedere il [file leggimi pg_cron](https://github.com/citusdata/pg_cron) .

>[!NOTE]
>Non è supportato per eliminare l' `citus` estensione. L' `citus` estensione è necessaria per fornire l'esperienza di iperscalabilità.

