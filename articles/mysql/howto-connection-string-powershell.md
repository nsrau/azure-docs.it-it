---
title: Generare una stringa di connessione con PowerShell - Database di Azure per MySQL
description: Questo articolo fornisce un esempio di Azure PowerShell per generare una stringa di connessione per la connessione a Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: fcba366a0322c3c1b5c6dcdf0fc3571646053fad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337184"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Come generare una stringa di connessione di Database di Azure per MySQL con PowerShell

Questo articolo illustra come generare una stringa di connessione per un database di Azure per il server MySQL. È possibile usare una stringa di connessione per connettersi a un database di Azure per MySQL da numerose applicazioni diverse.

## <a name="requirements"></a>Requisiti

Questo articolo usa le risorse create nella guida seguente come punto di partenza:

* [Avvio rapido: Creare un database di Azure per il server MySQL tramite PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il cmdlet `Get-AzMySqlConnectionString` viene usato per generare una stringa di connessione per connettere le applicazioni a Database di Azure per MySQL. L'esempio seguente restituisce la stringa di connessione per un client PHP da **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Personalizzare i parametri del server di Database di Azure per MySQL con PowerShell](howto-configure-server-parameters-using-powershell.md)
