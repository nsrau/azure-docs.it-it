---
title: Generare una stringa di connessione con PowerShell-database di Azure per PostgreSQL
description: Questo articolo fornisce un esempio di Azure PowerShell per generare una stringa di connessione per la connessione a database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909902"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Come generare una stringa di connessione per database di Azure per PostgreSQL con PowerShell

Questo articolo illustra come generare una stringa di connessione per un database di Azure per il server PostgreSQL. È possibile usare una stringa di connessione per connettersi a un database di Azure per PostgreSQL da molte applicazioni diverse.

## <a name="requirements"></a>Requisiti

Questo articolo usa le risorse create nella Guida seguente come punto di partenza:

* [Guida introduttiva: creare un server di database di Azure per PostgreSQL con PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il `Get-AzPostgreSqlConnectionString` cmdlet viene usato per generare una stringa di connessione per la connessione di applicazioni a database di Azure per PostgreSQL. Nell'esempio seguente viene restituita la stringa di connessione per un client PHP da **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

I valori validi per il `Client` parametro includono:

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
