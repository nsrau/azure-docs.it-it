---
title: Aumento automatico delle dimensioni di archiviazione-Azure PowerShell-database di Azure per MySQL
description: Questo articolo descrive come abilitare l'aumento automatico delle dimensioni di archiviazione usando PowerShell nel database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.openlocfilehash: 28dd9296eb0b2b2a014de9aeccf82baad5ef6e2c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112562"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Aumento automatico delle dimensioni di archiviazione nel database di Azure per il server MySQL con PowerShell

Questo articolo descrive come configurare l'archiviazione di un database di Azure per il server MySQL in modo che cresca senza compromettere il carico di lavoro.

L'aumento automatico dell'archiviazione impedisce al server di [raggiungere il limite di archiviazione](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) e di diventare di sola lettura. Per i server con almeno 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata di 5 GB quando lo spazio libero è inferiore al 10%. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata del 5% quando lo spazio libero è inferiore a 10 GB. I limiti di archiviazione massimi vengono applicati come specificato nella sezione archiviazione dei [piani tariffari di database di Azure per MySQL](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Tenere presente che lo spazio di archiviazione può essere scalato solo, non inattivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MySql PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Quando il modulo Az.MySql PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>Abilitare l'aumento automatico delle dimensioni dell'archiviazione di MySQL server

Abilitare l'aumento di dimensioni dell'archiviazione automatica del server in un server esistente con il comando seguente:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Abilitare l'archiviazione automatica delle dimensioni del server durante la creazione di un nuovo server con il comando seguente:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come creare e gestire le repliche di lettura nel database di Azure per MySQL usando PowerShell](howto-read-replicas-powershell.md).
