---
title: Creare utenti - Hyperscale (Citus) - Database di Azure per PostgreSQLCreate users - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484928"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Creare utenti nel database di Azure per PostgreSQL - Hyperscale (Citus)Create users in Azure Database for PostgreSQL - Hyperscale (Citus)

> [!NOTE]
> Il termine "utenti" si riferisce agli utenti all'interno di un gruppo di server Hyperscale (Citus). Per informazioni sugli utenti della sottoscrizione di Azure e sui relativi privilegi, vedere l'articolo Controllo degli [accessi basato sui ruoli](../role-based-access-control/built-in-roles.md) di Azure o leggere come personalizzare i [ruoli.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Account amministratore del server

Il motore PostgreSQL utilizza [i ruoli](https://www.postgresql.org/docs/current/sql-createrole.html) per controllare l'accesso agli oggetti di database e un gruppo di server Hyperscale (Citus) appena creato viene fornito con diversi ruoli predefiniti:

* Ruoli [PostgreSQL predefiniti](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Poiché Hyperscale è un servizio PaaS gestito, `postgres` solo Microsoft può accedere con il ruolo utente con privilegi avanzati. Per un accesso amministrativo `citus` limitato, Hyperscale fornisce il ruolo.

Autorizzazioni per `citus` il ruolo:

* Leggere tutte le variabili di configurazione, anche le variabili normalmente visibili solo ai superutenti.
* Leggi tutte\_le\_ \* viste delle statistiche pg e usa varie estensioni relative alle statistiche - anche le viste o le estensioni normalmente visibili solo ai superutenti.
* Eseguire funzioni di monitoraggio che possono richiedere blocchi ACCESS SHARE sulle tabelle, potenzialmente per un lungo periodo di tempo.
* [Creare estensioni PostgreSQL](concepts-hyperscale-extensions.md) (perché il `azure_pg_admin`ruolo è membro di ).

In particolare, il `citus` ruolo ha alcune restrizioni:

* Impossibile creare ruoli
* Impossibile creare database

## <a name="how-to-create-additional-user-roles"></a>Come creare ruoli utente aggiuntivi

Come accennato in precedenza, l'account `citus` amministratore non dispone dell'autorizzazione per creare altri utenti. Per aggiungere un utente, usare l'interfaccia del portale di Azure.To add a user, use the Azure portal interface.

1. Passare alla pagina **Ruoli** per il gruppo di server Hyperscale e fare clic su **Aggiungi:**

   ![Pagina dei ruoli](media/howto-hyperscale-create-users/1-role-page.png)

2. Immettere il nome e la password del ruolo. Fare clic su **Salva**.

   ![Aggiungi ruolo](media/howto-hyperscale-create-users/2-add-user-fields.png)

L'utente verrà creato nel nodo coordinatore del gruppo di server e propagato a tutti i nodi di lavoro. I ruoli creati tramite `LOGIN` il portale di Azure hanno l'attributo, il che significa che sono veri utenti che possono accedere al database.

## <a name="how-to-modify-privileges-for-user-role"></a>Come modificare i privilegi per il ruolo utente

I nuovi ruoli utente vengono comunemente utilizzati per fornire l'accesso al database con privilegi limitati. Per modificare i privilegi utente, utilizzare i comandi PostgreSQL standard, utilizzando uno strumento come PgAdmin o psql. (Vedere [connessione con psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) nella guida introduttiva Hyperscale (Citus).

Ad esempio, `db_user` per `mytable`consentire la lettura , concedere l'autorizzazione:

```sql
GRANT SELECT ON mytable TO db_user;
```

L'hyperscale (Citus) propaga le istruzioni GRANT a tabella singola tramite l'intero cluster, applicandole a tutti i nodi di lavoro. Tuttavia GRANT a livello di sistema (ad esempio, per tutte le tabelle in uno schema) devono essere eseguiti su ogni nodo di data.  Utilizzare `run_command_on_workers()` la funzione di supporto:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Come eliminare un ruolo utente o modificare la password

Per aggiornare un utente, visitare la pagina **Ruoli** per il gruppo di server Hyperscale e fare clic sui puntini di **sospensione...** accanto all'utente. I puntini di sospensione apriranno un menu per eliminare l'utente o reimpostare la password.

   ![Modificare un ruolo](media/howto-hyperscale-create-users/edit-role.png)

Il `citus` ruolo è privilegiato e non può essere eliminato.

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [Creare e gestire le regole del firewall Hyperscale (Citus) usando il portale](howto-hyperscale-manage-firewall-using-portal.md)di Azure.

Per ulteriori informazioni sulla gestione degli account utente del database, vedere la documentazione del prodotto PostgreSQL:For more information about database user account management, see PostgreSQL product documentation:

* [Ruoli e privilegi del databaseDatabase Roles and Privileges](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintassi GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html)
