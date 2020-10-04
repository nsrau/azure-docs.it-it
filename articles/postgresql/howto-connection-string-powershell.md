---
title: Generare una stringa di connessione con PowerShell-database di Azure per PostgreSQL
description: Questo articolo fornisce un esempio di Azure PowerShell per generare una stringa di connessione per la connessione a database di Azure per PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708170"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Come generare una stringa di connessione per database di Azure per PostgreSQL con PowerShell

Questo articolo illustra come generare una stringa di connessione per un database di Azure per il server PostgreSQL. È possibile usare una stringa di connessione per connettersi a un database di Azure per PostgreSQL da molte applicazioni diverse.

## <a name="requirements"></a>Requisiti

Questo articolo usa le risorse create nella guida seguente come punto di partenza:

* [Guida introduttiva: creare un server di database di Azure per PostgreSQL con PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il `Get-AzPostgreSqlConnectionString` cmdlet viene usato per generare una stringa di connessione per la connessione di applicazioni a database di Azure per PostgreSQL. L'esempio seguente restituisce la stringa di connessione per un client PHP da **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

I valori validi per il parametro `Client` sono:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Personalizzare i parametri del server di database di Azure per PostgreSQL con PowerShell](howto-configure-server-parameters-using-powershell.md)
