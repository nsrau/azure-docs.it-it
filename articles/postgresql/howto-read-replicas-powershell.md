---
title: Gestire le repliche di lettura-Azure PowerShell-database di Azure per PostgreSQL
description: Informazioni su come configurare e gestire le repliche di lettura nel database di Azure per PostgreSQL usando PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8bacb7a434cfa04dbdfdaf39d9fd3a0baab5f11a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489813"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Come creare e gestire le repliche di lettura nel database di Azure per PostgreSQL con PowerShell

Questo articolo illustra come creare e gestire le repliche di lettura nel database di Azure per il servizio PostgreSQL usando PowerShell. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

È possibile creare e gestire le repliche di lettura usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- [Database di Azure per il server PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Poiché il modulo Az.PostgreSql di PowerShell è in fase di anteprima, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Quando il modulo Az.PostgreSql di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per PostgreSQL nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server primario si trovi in uno di questi piani tariffari.

### <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura usando il comando seguente:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Il comando `New-AzPostgreSqlServerReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Il gruppo di risorse in cui viene creato il server di replica.  |
| Nome | mydemoreplicaserver | Nome del nuovo server di replica creato. |

Per creare una replica di lettura tra aree, usare il parametro **location** . Nell'esempio seguente viene creata una replica nell'area **Stati Uniti occidentali** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md).

Per impostazione predefinita, le repliche di lettura vengono create con la stessa configurazione del server primaria, a meno che non sia specificato il parametro **SKU** .

> [!NOTE]
> È consigliabile mantenere la configurazione del server di replica con valori uguali o maggiori rispetto al database primario, per garantire che la replica sia in grado di rimanere al passo con il master.

### <a name="list-replicas-for-a-primary-server"></a>Elencare le repliche per un server primario

Per visualizzare tutte le repliche per un determinato server primario, eseguire il comando seguente:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Il comando `Get-AzMariaDReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| ServerName | mydemoserver | Nome o ID del server primario. |

### <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Eliminare un server primario

> [!IMPORTANT]
> L'eliminazione di un server primario interrompe la replica a tutti i server di replica ed Elimina il server primario stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server primario, è possibile eseguire il `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riavviare il database di Azure per il server PostgreSQL con PowerShell](howto-restart-server-powershell.md)