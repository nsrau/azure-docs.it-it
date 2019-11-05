---
title: Creare utenti in database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per PostgreSQL-overscale (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515941"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Creare utenti in database di Azure per PostgreSQL-iperscalabilità (CITUS)

Questo articolo descrive come creare utenti all'interno di un gruppo di server con iperscalabilità (CITUS). Per informazioni sugli utenti della sottoscrizione di Azure e sui relativi privilegi, vedere l' [articolo controllo degli accessi in base al ruolo (RBAC) di Azure](../role-based-access-control/built-in-roles.md) o vedere [come personalizzare i ruoli](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Account amministratore del server

Un gruppo di server con iperscalabilità (CITUS) appena creato presenta diversi ruoli predefiniti:

* [Ruoli di PostgreSQL predefiniti](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

L'utente amministratore del server, *CITUS*, è un membro del ruolo *azure_pg_admin* .
Tuttavia, non fa parte del ruolo *Postgres* (utente con privilegi avanzati).  Poiché iperscale è un servizio PaaS gestito, solo Microsoft fa parte del ruolo utente con privilegi avanzati.

Il motore PostgreSQL usa i privilegi per controllare l'accesso agli oggetti di database, come illustrato nella [documentazione del prodotto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
In Database di Azure per PostgreSQL all'utente amministratore del server vengono concessi questi privilegi: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

## <a name="how-to-create-additional-users"></a>Come creare utenti aggiuntivi

L'account amministratore di *CITUS* non dispone dell'autorizzazione per la creazione di utenti aggiuntivi. Per aggiungere un utente, usare invece il portale di Azure.

1. Andare alla pagina **ruoli** per il gruppo di server con iperscalabilità e fare clic su **+ Aggiungi**:

   ![Pagina ruoli](media/howto-hyperscale-create-users/1-role-page.png)

2. Immettere il nome e la password del ruolo. Fare clic su **Save**.

   ![Aggiungi ruolo](media/howto-hyperscale-create-users/2-add-user-fields.png)

L'utente verrà creato nel nodo coordinatore del gruppo di server e propagato a tutti i nodi del ruolo di lavoro.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Come eliminare un utente o modificare la password

Passare alla pagina **ruoli** per il gruppo di server con iperscalabilità e fare clic sui puntini di sospensione **...** accanto a un utente. I puntini di sospensione aprono un menu per eliminare l'utente o reimpostare la password.

   ![Modificare un ruolo](media/howto-hyperscale-create-users/edit-role.png)

Il ruolo *CITUS* è con privilegi e non può essere eliminato.

## <a name="how-to-modify-privileges-for-role"></a>Come modificare i privilegi per il ruolo

I nuovi ruoli vengono comunemente usati per fornire l'accesso al database con privilegi limitati. Per modificare i privilegi utente, usare i comandi standard di PostgreSQL, usando uno strumento come PgAdmin o PSQL. Vedere la pagina relativa alla [connessione con PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) nella Guida introduttiva a scalabilità (CITUS).

Ad esempio, per consentire a *DB_USER* di leggere *MyTable*, concedere l'autorizzazione:

```sql
GRANT SELECT ON mytable TO db_user;
```

Iperscale (CITUS) propaga le istruzioni GRANT a tabella singola attraverso l'intero cluster, applicando tali istruzioni in tutti i nodi del ruolo di lavoro. Tuttavia, le concessioni che sono a livello di sistema, ad esempio per tutte le tabelle in uno schema, devono essere eseguite in ogni nodo Data.  Usare la funzione helper *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [creare e gestire regole del firewall di iperscala (CITUS) usando il portale di Azure](howto-hyperscale-manage-firewall-using-portal.md).

Per ulteriori informazioni sulla gestione degli account utente del database, vedere la documentazione del prodotto PostgreSQL:

* [Privilegi e ruoli del database](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Concedi sintassi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html)
