---
title: Migrare i dati da un database PostgreSQL in un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc
titleSuffix: Azure Arc enabled database services
description: Migrare i dati da un database PostgreSQL in un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 521fd61f18d6673e21c23dbca4cfc12d2ee4bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939642"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrare il database PostgreSQL in un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

Questo documento descrive i passaggi per ottenere il database PostgreSQL esistente (uno che non è ospitato in Azure Arc Enabled Data Services) nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Considerazioni

Il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc è la versione community di PostgreSQL e viene eseguito con l'estensione CitusData abilitata. Quindi, qualsiasi strumento che funziona in PostgreSQL all'esterno di Azure Arc dovrebbe funzionare con il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc.


Di conseguenza, con il set di strumenti usato oggi per Postgres, dovrebbe essere possibile:
1. Eseguire il backup del database postgres dall'istanza ospitata all'esterno di Azure Arc
2. Ripristinarlo nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

Le operazioni da eseguire sono le seguenti:
- reimpostare i parametri del server
- Reimposta i contesti di sicurezza: ricrea utenti, ruoli e Reimposta autorizzazioni...

Per eseguire questa operazione di backup/ripristino, è possibile usare qualsiasi strumento in grado di eseguire il backup o il ripristino per postgres. Ad esempio:
- Azure Data Studio e la relativa estensione Postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Esempio
Verranno illustrati questi passaggi utilizzando lo `pgAdmin` strumento.
Si consideri la seguente configurazione:
- **Source:**  
    Server Postgres in esecuzione in locale su un server bare metal e denominato JEANYDSRV. È di versione 12 e ospita un database denominato MyOnPremPostgresDB con una tabella T1 con 1 riga :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migrate-source":::

- **Destinazione**  
    Un server Postgres in esecuzione in un ambiente Azure Arc e denominato postgres01. È della versione 12. Non dispone di alcun database ad eccezione del database Postgres standard.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="migrate-source":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Eseguire un backup del database di origine in locale

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="migrate-source":::

Configurare:
1. Assegnare un nome file: **MySourceBackup**
2. Impostare il formato su **Custom** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="migrate-source":::

Il backup è stato completato correttamente:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="migrate-source":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Creare un database vuoto nel sistema di destinazione nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

> [!NOTE]
> Per registrare un'istanza di Postgres nello `pgAdmin` strumento, è necessario usare l'indirizzo IP pubblico dell'istanza nel cluster Kubernetes e impostare la porta e il contesto di sicurezza appropriati. Questi dettagli sono disponibili nella riga dell' `psql` endpoint dopo l'esecuzione del comando seguente:

```console
azdata arc postgres endpoint list -n postgres01
```
Che restituisce un output simile al seguente:
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

Assegnare un nome al database di destinazione **RESTORED_MyOnPremPostgresDB**  
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrate-Destination-DB-create"lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Ripristinare il database nella configurazione dell'arco
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="migrate-source":::

Configurare il ripristino:
1. Puntare al file che contiene il backup da ripristinare: **MySourceBackup**
2. Mantieni il formato impostato su **Custom o tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="migrate-source":::

3. Fare clic su **Ripristina**.  

   Il ripristino è riuscito.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="migrate-source":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verificare che il database sia stato ripristinato nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

Usare uno dei metodi seguenti:

**Da `pgAdmin` :**  

Espandere l'istanza Postgres ospitata nel programma di installazione di Azure Arc. Verrà visualizzata la tabella nel database ripristinato e, quando si selezionano i dati, viene visualizzata la stessa riga dell'istanza locale:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="migrate-source"
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

1. Dalla `psql` stringa di connessione utilizzare il `-d` parametro per indicare il nome del database. Con il comando seguente verrà richiesto di immettere la password:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` collega.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Selezionare la tabella per visualizzare i dati ripristinati dall'istanza Postgres locale:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - I vantaggi a livello di prestazioni dell'esecuzione in Azure Arc abilitano l'iperscalabilità di PostgreSQL fino a quando non si scala orizzontalmente e si partizionano/distribuiscono i dati tra i nodi del ruolo di lavoro del gruppo di server con iperscalabilità PostgreSQL. Vedere i [passaggi successivi](#next-steps).
>
> - Attualmente non è possibile eseguire l'onboarding in Azure Arc, un'istanza di Postgres esistente che verrebbe eseguita in locale o in qualsiasi altro cloud. In altre parole, non è possibile installare una sorta di "agente di Azure Arc" nell'istanza Postgres esistente per renderla un'installazione Postgres abilitata da Azure Arc. È invece necessario creare una nuova istanza di Postgres e trasferirvi i dati. Per eseguire questa operazione, è possibile usare la tecnica illustrata in precedenza oppure è possibile usare qualsiasi strumento ETL di propria scelta.

## <a name="next-steps"></a>Passaggi successivi

- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per la distribuzione dei dati in più nodi con iperscalabilità PostgreSQL e per sfruttare tutte le potenzialità dell'iperscalabilità di database di Azure per PostgreSQL:
    * [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
    * [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * In questi documenti ignorare le sezioni **accedere al portale di Azure**e **creare un database di Azure per Postgres-overscale (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

- [Aumentare le istanze in Database di Azure per il gruppo di server PostgreSQL Hyperscale](scale-out-postgresql-hyperscale-server-group.md)
