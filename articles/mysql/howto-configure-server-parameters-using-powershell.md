---
title: Configurare i parametri del server-Azure PowerShell-database di Azure per MySQL
description: Questo articolo descrive come configurare i parametri del servizio in database di Azure per MySQL usando PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 0de816d25bbc1563885413d8dbd52dc7bda7d538
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615084"
---
# <a name="customize-azure-database-for-mysql-server-parameters-using-powershell"></a>Personalizzare i parametri del server di database di Azure per MySQL con PowerShell

È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un database di Azure per il server MySQL usando PowerShell. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mentre il modulo AZ. MySql PowerShell è in anteprima, è necessario installarlo separatamente dal modulo AZ PowerShell usando il comando seguente: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Una volta che il modulo AZ. MySql PowerShell è disponibile a livello generale, diventa parte delle future versioni del modulo AZ PowerShell e disponibile in modo nativo dall'interno Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server MySQL

Per elencare tutti i parametri modificabili in un server e i relativi `Get-AzMySqlConfiguration` valori, eseguire il cmdlet.

Nell'esempio seguente vengono elencati i parametri di configurazione del server per il server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MySQL nell'articolo sulle [variabili di sistema del server](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server

Per visualizzare i dettagli di un determinato parametro di configurazione per un server, `Get-AzMySqlConfiguration` eseguire il cmdlet e specificare il parametro **Name** .

Questo esempio mostra i dettagli del parametro di configurazione del server **Slow\_query\_log** per il server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server

È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server MySQL. Per aggiornare la configurazione, usare il `Update-AzMySqlConfiguration` cmdlet.

Per aggiornare il parametro di configurazione del server **Slow\_query\_log** del server **mydemoserver** nel gruppo di risorse **myresourcegroup**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Espandere automaticamente l'archiviazione nel database di Azure per il server MySQL usando PowerShell](howto-auto-grow-storage-powershell.md).
