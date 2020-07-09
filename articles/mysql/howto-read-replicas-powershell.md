---
title: Gestire le repliche di lettura-Azure PowerShell-database di Azure per MySQL
description: Informazioni su come configurare e gestire le repliche di lettura nel database di Azure per MySQL usando PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: f6d24ba0d31020b82669947189da180348f2a46b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107989"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Come creare e gestire le repliche di lettura nel database di Azure per MySQL usando PowerShell

Questo articolo illustra come creare e gestire le repliche di lettura nel database di Azure per il servizio MySQL usando PowerShell. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

È possibile creare e gestire le repliche di lettura usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MySql PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Quando il modulo Az.MySql PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La funzionalità relativa alle repliche in lettura è disponibile solo per i server di Database di Azure per MySQL nei piani tariffari Utilizzo generico o Con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

### <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando viene creata una replica per un master senza repliche esistenti, il master verrà prima riavviato per prepararsi alla replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura usando il comando seguente:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Il comando `New-AzMySqlServerReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Il gruppo di risorse in cui viene creato il server di replica.  |
| Nome | mydemoreplicaserver | Nome del nuovo server di replica creato. |

Per creare una replica di lettura tra aree, usare il parametro **location** . Nell'esempio seguente viene creata una replica nell'area **Stati Uniti occidentali** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md).

Per impostazione predefinita, le repliche di lettura vengono create con la stessa configurazione del server del database master, a meno che non sia stato specificato il parametro **SKU** .

> [!NOTE]
> È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

### <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Il comando `Get-AzMySqlReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| ServerName | mydemoserver | Nome o ID del server master. |

### <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il `Remove-AzMySqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il `Remove-AzMySqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riavviare il database di Azure per il server MySQL con PowerShell](howto-restart-server-powershell.md)
