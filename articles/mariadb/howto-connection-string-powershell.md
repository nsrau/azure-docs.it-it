---
title: Generare una stringa di connessione con PowerShell - Database di Azure per MariaDB
description: Questo articolo fornisce un esempio di Azure PowerShell per generare una stringa di connessione per la connessione a Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: b08a1c60b1225b2786ea869f96ac40cc651c82f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185605"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Come generare una stringa di connessione di Database di Azure per MariaDB con PowerShell

Questo articolo illustra come generare una stringa di connessione per un server di Database di Azure per MariaDB. È possibile usare una stringa di connessione per connettersi a un'istanza di Database di Azure per MariaDB da numerose applicazioni diverse.

## <a name="requirements"></a>Requisiti

Questo articolo usa le risorse create nella guida seguente come punto di partenza:

* [Avvio rapido: Creare un server di Database di Azure per MariaDB usando PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il cmdlet `Get-AzMariaDbConnectionString` viene usato per generare una stringa di connessione per connettere le applicazioni a Database di Azure per MariaDB. L'esempio seguente restituisce la stringa di connessione per un client PHP da **mydemoserver**.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Personalizzare i parametri del server di Database di Azure per MariaDB con PowerShell](howto-configure-server-parameters-using-powershell.md)
