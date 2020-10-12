---
title: Aumento automatico delle dimensioni di archiviazione-Azure PowerShell-database di Azure per PostgreSQL
description: Questo articolo descrive come abilitare l'aumento automatico delle dimensioni di archiviazione usando PowerShell nel database di Azure per PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a186c6e176cde20474cdf4772b8724ad3f356a3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87493619"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Espansione automatica dell'archiviazione nel database di Azure per il server PostgreSQL con PowerShell

Questo articolo descrive come configurare un database di Azure per l'archiviazione del server PostgreSQL in modo che cresca senza alcun effetto sul carico di lavoro.

L'aumento automatico dell'archiviazione impedisce al server di [raggiungere il limite di archiviazione](/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit) e di diventare di sola lettura. Per i server con almeno 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata di 5 GB quando lo spazio libero è inferiore al 10%. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata del 5% quando lo spazio libero è inferiore a 10 GB. I limiti di archiviazione massimi vengono applicati come specificato nella sezione archiviazione dei [piani tariffari di database di Azure per PostgreSQL](/azure/postgresql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Tenere presente che lo spazio di archiviazione può essere scalato solo, non inattivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- [Database di Azure per il server PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Poiché il modulo Az.PostgreSql di PowerShell è in fase di anteprima, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Quando il modulo Az.PostgreSql di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Abilitare l'aumento automatico delle dimensioni di archiviazione del server PostgreSQL

Abilitare l'aumento di dimensioni dell'archiviazione automatica del server in un server esistente con il comando seguente:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Abilitare l'archiviazione automatica delle dimensioni del server durante la creazione di un nuovo server con il comando seguente:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come creare e gestire le repliche di lettura nel database di Azure per PostgreSQL usando PowerShell](howto-read-replicas-powershell.md).
