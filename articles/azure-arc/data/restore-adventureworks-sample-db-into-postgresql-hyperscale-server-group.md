---
title: Ripristinare il database di esempio AdventureWorks in Azure Arc abilitata per l'iperscalabilità PostgreSQL
description: Ripristinare il database di esempio AdventureWorks in Azure Arc abilitata per l'iperscalabilità PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b51c5ca2295671a30fa6c0aee8d313c4c333900
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938965"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Ripristinare il database di esempio AdventureWorks in Azure Arc abilitata per l'iperscalabilità PostgreSQL

[AdventureWorks](/sql/samples/adventureworks-install-configure) è un database di esempio contenente un database OLTP usato nelle esercitazioni ed esempi. Viene fornito e gestito da Microsoft come parte del [repository GitHub SQL Server Samples](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Un progetto open source ha convertito il database AdventureWorks in modo che sia compatibile con l'iperscalabilità PostgreSQL abilitata per Azure Arc.
- [Progetto originale](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Seguire il progetto che pre-converte i file CSV in modo che siano compatibili con PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

Questo documento descrive un processo semplice per ripristinare il database di esempio AdventureWorks nel gruppo di server di scalabilità di PostgreSQL.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Scaricare il file di backup AdventureWorks

Scaricare il file AdventureWorks. SQL nel contenitore del gruppo di server con iperscalabilità PostgreSQL. In questo esempio si userà il `kubectl exec` comando per eseguire in modalità remota un comando nel contenitore del gruppo di server di iperscala PostgreSQL per scaricare il file nel contenitore. È possibile scaricare questo file da qualsiasi percorso accessibile da `curl` . Utilizzare questo stesso metodo se si dispone di altri file di backup del database che si desidera effettuare il pull nel contenitore gruppo di server con iperscalabilità PostgreSQL. Una volta che si trova nel contenitore del gruppo di server con iperscalabilità PostgreSQL, è facile creare il database, lo schema e popolare i dati.

Eseguire un comando simile al seguente per scaricare i file sostituire il valore del nome del Pod e il nome dello spazio dei nomi prima di eseguirlo:

> [!NOTE]
>  Il contenitore dovrà avere connettività Internet superiore a 443 per scaricare il file da GitHub.

> [!NOTE]
>  Usare il nome Pod del nodo coordinatore del gruppo di server iperscalare postgres. Il nome è <server group name> -0.  Se non si è certi del nome del Pod, eseguire il comando `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>Passaggio 2: ripristinare il database AdventureWorks

Analogamente, è possibile eseguire un comando kubectl Exec per usare lo strumento dell'interfaccia della riga di comando di PSQL incluso nei contenitori del gruppo di server con iperscalabilità PostgreSQL per creare e caricare il database.

Eseguire un comando simile al seguente per creare il database vuoto sostituendo innanzitutto il valore del nome del Pod e il nome dello spazio dei nomi prima di eseguirlo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Eseguire quindi un comando simile al seguente per ripristinare il database sostituendo il valore del nome del Pod e il nome dello spazio dei nomi prima di eseguirlo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Nota: i vantaggi a livello di prestazioni dell'esecuzione in Azure Arc con scalabilità eccessiva di PostgreSQL non saranno visibili fino a quando non si esegue la scalabilità orizzontale e si partizionano/distribuiscono i dati o le tabelle tra i nodi di lavoro del gruppo di server con scalabilità in PostgreSQL. Vedere i [passaggi successivi suggeriti](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Passaggi successivi suggeriti
- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per distribuire i dati tra più nodi con scalabilità multipla PostgreSQL e sfruttare tutte le potenzialità dell'iperscalabilità di database di Azure per PostgreSQL. :
    * [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
    * [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* Nei documenti precedenti ignorare le sezioni accedere al **portale di Azure**& **creare un database di Azure per PostgreSQL-iperscalabilità (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

- [Scalabilità orizzontale del gruppo di server di scalabilità orizzontale per database di Azure per PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
