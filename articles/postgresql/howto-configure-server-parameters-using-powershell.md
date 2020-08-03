---
title: Configurare i parametri del server-Azure PowerShell-database di Azure per PostgreSQL
description: Questo articolo descrive come configurare i parametri del servizio in database di Azure per PostgreSQL usando PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2948e0b916eab75f0e0cf1d0ba1b5be1fcf188f4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87493466"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Personalizzare i parametri del server di database di Azure per PostgreSQL con PowerShell

È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un database di Azure per il server PostgreSQL usando PowerShell. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- [Database di Azure per il server PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Poiché il modulo Az.PostgreSql di PowerShell è in fase di anteprima, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Quando il modulo Az.PostgreSql di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server PostgreSQL

Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il `Get-AzPostgreSqlConfiguration` cmdlet.

Nell'esempio seguente vengono elencati i parametri di configurazione del server per il server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di PostgreSQL sulle [variabili di ambiente](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server

Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il `Get-AzPostgreSqlConfiguration` cmdlet e specificare il parametro **Name** .

Questo esempio mostra i dettagli del parametro di configurazione del server **Slow \_ query \_ log** per il server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server

È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server PostgreSQL. Per aggiornare la configurazione, usare il `Update-AzPostgreSqlConfiguration` cmdlet.

Per aggiornare il parametro di configurazione del server **Slow \_ query \_ log** del server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Espandere automaticamente l'archiviazione nel database di Azure per il server PostgreSQL usando PowerShell](howto-auto-grow-storage-powershell.md).
