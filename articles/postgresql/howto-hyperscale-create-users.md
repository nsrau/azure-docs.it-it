---
title: Create users in Azure Database for PostgreSQL - Hyperscale (Citus)
description: This article describes how you can create new user accounts to interact with an Azure Database for PostgreSQL - Hyperscale (Citus).
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
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Create users in Azure Database for PostgreSQL - Hyperscale (Citus)

This article describes how you can create users within a Hyperscale (Citus) server group. To learn instead about Azure subscription users and their privileges, visit the [Azure role based access control (RBAC) article](../role-based-access-control/built-in-roles.md) or review [how to customize roles](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Account amministratore del server

A newly created Hyperscale (Citus) server group comes with several roles pre-defined:

* The [default PostgreSQL roles](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

Your server admin user, *citus*, is a member of the *azure_pg_admin* role.
However, it isn't part of the *postgres* (super user) role.  Since Hyperscale is a managed PaaS service, only Microsoft is part of the super user role.

Il motore PostgreSQL usa i privilegi per controllare l'accesso agli oggetti di database, come illustrato nella [documentazione del prodotto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
In Database di Azure per PostgreSQL all'utente amministratore del server vengono concessi questi privilegi: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

## <a name="how-to-create-additional-users"></a>How to create additional users

The *citus* admin account lacks permission to create additional users. To add a user, use the Azure portal instead.

1. Go to the **Roles** page for your Hyperscale server group, and click **+ Add**:

   ![The roles page](media/howto-hyperscale-create-users/1-role-page.png)

2. Enter the role name and password. Fare clic su **Salva**

   ![Aggiungi ruolo](media/howto-hyperscale-create-users/2-add-user-fields.png)

The user will be created on the coordinator node of the server group, and propagated to all the worker nodes.

## <a name="how-to-delete-a-user-or-change-their-password"></a>How to delete a user or change their password

Go to the **Roles** page for your Hyperscale server group, and click the ellipses **...** next to a user. The ellipses will open a menu to delete the user or reset their password.

   ![Edit a role](media/howto-hyperscale-create-users/edit-role.png)

The *citus* role is privileged and can't be deleted.

## <a name="how-to-modify-privileges-for-role"></a>How to modify privileges for role

New roles are commonly used to provide database access with restricted privileges. To modify user privileges, use standard PostgreSQL commands, using a tool such as PgAdmin or psql. (See [connecting with psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) in the Hyperscale (Citus) quickstart.)

For example, to allow *db_user* to read *mytable*, grant the permission:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) propagates single-table GRANT statements through the entire cluster, applying them on all worker nodes. However GRANTs that are system-wide (e.g. for all tables in a schema) need to be run on every date node.  Use the *run_command_on_workers()* helper function:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Passaggi successivi

Open the firewall for the IP addresses of the new users' machines to enable them to connect: [Create and manage Hyperscale (Citus) firewall rules using the Azure portal](howto-hyperscale-manage-firewall-using-portal.md).

Per ulteriori informazioni sulla gestione degli account utente del database, vedere la documentazione del prodotto PostgreSQL:

* [Privilegi e ruoli del database](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Concedi sintassi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html)
