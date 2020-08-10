---
title: Generare una stringa di connessione con PowerShell - Database di Azure per MySQL
description: Questa guida di avvio rapido fornisce un esempio di Azure PowerShell per generare una stringa di connessione per la connessione a Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544918"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Database di Azure per MySQL: Generare una stringa di connessione con PowerShell

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
> [Progettare un'istanza di Database di Azure per MySQL con PowerShell](tutorial-design-database-using-powershell.md)
