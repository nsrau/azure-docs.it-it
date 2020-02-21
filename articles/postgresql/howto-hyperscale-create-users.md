---
title: Creare utenti-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per PostgreSQL-overscale (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484928"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Creare utenti in database di Azure per PostgreSQL-iperscalabilità (CITUS)

> [!NOTE]
> Il termine "Users" si riferisce agli utenti all'interno di un gruppo di server con iperscalabilità (CITUS). Per informazioni sugli utenti della sottoscrizione di Azure e sui relativi privilegi, vedere l' [articolo controllo degli accessi in base al ruolo (RBAC) di Azure](../role-based-access-control/built-in-roles.md) o vedere [come personalizzare i ruoli](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Account amministratore del server

Il motore PostgreSQL usa i [ruoli](https://www.postgresql.org/docs/current/sql-createrole.html) per controllare l'accesso agli oggetti di database e un gruppo di server CITUS (iperscale) appena creato presenta diversi ruoli predefiniti:

* [Ruoli di PostgreSQL predefiniti](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Poiché iperscale è un servizio PaaS gestito, solo Microsoft può accedere con il ruolo utente con privilegi avanzati `postgres`. Per un accesso amministrativo limitato, l'iperscalabilità fornisce il ruolo `citus`.

Autorizzazioni per il ruolo `citus`:

* Leggere tutte le variabili di configurazione, anche le variabili normalmente visibili solo agli utenti con superuser.
* Leggere tutti i\_stat\_\* visualizzazioni e usare varie estensioni correlate alle statistiche, anche le visualizzazioni o le estensioni sono normalmente visibili solo agli utenti con superuser.
* Eseguire funzioni di monitoraggio che potrebbero richiedere l'accesso a blocchi di condivisione sulle tabelle, potenzialmente per molto tempo.
* [Creare estensioni PostgreSQL](concepts-hyperscale-extensions.md) (poiché il ruolo è membro di `azure_pg_admin`).

In particolare, il ruolo `citus` presenta alcune restrizioni:

* Non è possibile creare ruoli
* Non è possibile creare database

## <a name="how-to-create-additional-user-roles"></a>Come creare ruoli utente aggiuntivi

Come indicato in precedenza, l'account amministratore `citus` non dispone dell'autorizzazione per la creazione di utenti aggiuntivi. Per aggiungere un utente, utilizzare l'interfaccia portale di Azure.

1. Andare alla pagina **ruoli** per il gruppo di server con iperscalabilità e fare clic su **+ Aggiungi**:

   ![Pagina ruoli](media/howto-hyperscale-create-users/1-role-page.png)

2. Immettere il nome e la password del ruolo. Fare clic su **Salva**.

   ![Aggiungi ruolo](media/howto-hyperscale-create-users/2-add-user-fields.png)

L'utente verrà creato nel nodo coordinatore del gruppo di server e propagato a tutti i nodi del ruolo di lavoro. I ruoli creati tramite il portale di Azure hanno l'attributo `LOGIN`, che indica che si tratta di utenti veri che possono accedere al database.

## <a name="how-to-modify-privileges-for-user-role"></a>Come modificare i privilegi per il ruolo utente

I nuovi ruoli utente vengono comunemente usati per fornire l'accesso al database con privilegi limitati. Per modificare i privilegi utente, usare i comandi standard di PostgreSQL, usando uno strumento come PgAdmin o PSQL. Vedere la pagina relativa alla [connessione con PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) nella Guida introduttiva a scalabilità (CITUS).

Ad esempio, per consentire `db_user` di leggere `mytable`, concedere l'autorizzazione:

```sql
GRANT SELECT ON mytable TO db_user;
```

Iperscale (CITUS) propaga le istruzioni GRANT a tabella singola attraverso l'intero cluster, applicando tali istruzioni in tutti i nodi del ruolo di lavoro. Tuttavia, le concessioni che sono a livello di sistema, ad esempio per tutte le tabelle in uno schema, devono essere eseguite in ogni nodo Data.  Utilizzare la funzione di supporto `run_command_on_workers()`:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Come eliminare un ruolo utente o modificare la password

Per aggiornare un utente, visitare la pagina **ruoli** per il gruppo di server con iperscalabilità e fare clic sui puntini di sospensione **...** accanto all'utente. I puntini di sospensione aprono un menu per eliminare l'utente o reimpostare la password.

   ![Modificare un ruolo](media/howto-hyperscale-create-users/edit-role.png)

Il ruolo `citus` è privilegiato e non può essere eliminato.

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [creare e gestire regole del firewall di iperscala (CITUS) usando il portale di Azure](howto-hyperscale-manage-firewall-using-portal.md).

Per ulteriori informazioni sulla gestione degli account utente del database, vedere la documentazione del prodotto PostgreSQL:

* [Privilegi e ruoli del database](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Concedi sintassi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html)
