---
title: Gestire le repliche di lettura-Azure PowerShell-database di Azure per MariaDB
description: Informazioni su come configurare e gestire le repliche di lettura nel database di Azure per MariaDB con PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 0280d69dc552b776457ff28d19968f6494a846ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707948"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando PowerShell

Questo articolo illustra come creare e gestire le repliche di lettura nel database di Azure per il servizio MariaDB usando PowerShell. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

È possibile creare e gestire le repliche di lettura usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MariaDb PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Quando il modulo Az.MariaDb di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

### <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando viene creata una replica per un master senza repliche esistenti, il master verrà prima riavviato per prepararsi alla replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura usando il comando seguente:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Il comando `New-AzMariaDbServerReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Il gruppo di risorse in cui viene creato il server di replica.  |
| Nome | mydemoreplicaserver | Nome del nuovo server di replica creato. |

Per creare una replica di lettura tra aree, usare il parametro **location** . Nell'esempio seguente viene creata una replica nell'area **Stati Uniti occidentali** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md).

Per impostazione predefinita, le repliche di lettura vengono create con la stessa configurazione del server del database master, a meno che non sia stato specificato il parametro **SKU** .

> [!NOTE]
> È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

### <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Il comando `Get-AzMariaDReplica` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| ServerName | mydemoserver | Nome o ID del server master. |

### <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riavviare il database di Azure per il server MariaDB con PowerShell](howto-restart-server-powershell.md)
