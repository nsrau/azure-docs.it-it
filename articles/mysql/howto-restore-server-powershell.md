---
title: Backup e ripristino-Azure PowerShell-database di Azure per MySQL
description: Informazioni su come eseguire il backup e il ripristino di un server in database di Azure per MySQL usando Azure PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: how-to
ms.date: 4/28/2020
ms.openlocfilehash: 85c04c875e543a5c41e9ad5e736a7de77ac1dad0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119872"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mysql-server-using-powershell"></a>Come eseguire il backup e il ripristino di un server di Database di Azure per MySQL con PowerShell

Viene eseguito periodicamente il backup del database di Azure per i server MySQL per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MySql PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Quando il modulo Az.MySql PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

Al momento della creazione del server, è possibile scegliere tra la configurazione del server per i backup con ridondanza locale o con ridondanza geografica.

> [!NOTE]
> Dopo aver creato un server, il tipo di ridondanza, con ridondanza geografica e con ridondanza locale, non può essere modificato.

Durante la creazione di un server tramite il `New-AzMySqlServer` comando, il parametro **GeoRedundantBackup** decide l'opzione di ridondanza del backup. Se **abilitata**, vengono eseguiti backup con ridondanza geografica. In alternativa, se **disabilitato**, vengono eseguiti backup con ridondanza locale.

Il periodo di conservazione dei backup è impostato dal parametro **BackupRetentionDay** .

Per altre informazioni sull'impostazione di questi valori durante la creazione del server, vedere [creare un database di Azure per il server MySQL usando PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md).

È possibile modificare il periodo di conservazione dei backup di un server nel modo seguente:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

L'esempio precedente modifica il periodo di conservazione dei backup di mydemoserver impostandolo su 10 giorni.

Il periodo di conservazione dei backup determina il modo in cui è possibile recuperare un ripristino temporizzato, perché si basa su backup disponibili. Il ripristino temporizzato è descritto in modo più dettagliato nella sezione seguente.

## <a name="server-point-in-time-restore"></a>Ripristino temporizzato del server

È possibile ripristinare il server in base a una temporizzazione precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente non viene modificato. Se, ad esempio, una tabella è stata eliminata involontariamente, è possibile eseguire il ripristino a un qualsiasi momento prima dell'eliminazione. È possibile quindi recuperare la tabella e i dati mancanti dalla copia ripristinata del server.

Per ripristinare il server, usare il cmdlet `Restore-AzMySqlServer` di PowerShell.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino

Per ripristinare il server, eseguire l'esempio seguente da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Il set di parametri **PointInTimeRestore** del `Restore-AzMySqlServer` cmdlet richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| Nome | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selezionare un momento specifico per il ripristino. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. Ad esempio, è possibile usare il proprio fuso orario locale, ad esempio **2020-03-13T05:59:00-08:00**. È anche possibile usare il formato UTC Zulu, ad esempio **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Usare la modalità temporizzata per il ripristino. |

Quando si ripristina un server a una temporizzazione precedente, viene creato un nuovo server. Il server originale e i database della temporizzazione specificata vengono copiati nel nuovo server.

I valori relativi al percorso e al piano tariffario per il server ripristinato sono gli stessi del server di origine.

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha il nome e la password di accesso dell'amministratore del server che erano validi per il server esistente quando è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Queste regole devono essere configurate separatamente per il nuovo server. Vengono ripristinate le regole del firewall del server originale.

## <a name="geo-restore"></a>Ripristino geografico

Se il server è stato configurato per i backup con ridondanza geografica, è possibile creare un nuovo server dal backup del server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per MySQL.

Per creare un server usando un backup con ridondanza geografica, usare il `Restore-AzMySqlServer` comando con il parametro **UseGeoRestore** .

> [!NOTE]
> Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.

Per il ripristino geografico del server, eseguire l'esempio seguente da PowerShell:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Questo esempio crea un nuovo server denominato **mydemoserver-georipristinato** nell'area Stati Uniti orientali che appartiene a **myresourcegroup**. Si tratta di un server per utilizzo generico di quinta generazione con otto vCore. Il server viene creato dal backup con ridondanza geografica di **mydemoserver**, anche nel gruppo di risorse **myresourcegroup**.

Per creare il nuovo server in un gruppo di risorse diverso dal server esistente, specificare il nome del nuovo gruppo di risorse usando il parametro **ResourceGroupName** , come illustrato nell'esempio seguente:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Il set di parametri **Georestore** del `Restore-AzMySqlServer` cmdlet richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Nome del gruppo di risorse a cui appartiene il nuovo server.|
|Nome | mydemoserver-georestored | Nome del nuovo server. |
|Location | eastus | Posizione del nuovo server. |
|UseGeoRestore | `<SwitchParameter>` | Usare la modalità geografica per il ripristino. |

Quando si crea un nuovo server con il ripristino geografico, esso eredita le stesse dimensioni di archiviazione e il piano tariffario del server di origine, a meno che non venga specificato il parametro **SKU** .

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha il nome e la password di accesso dell'amministratore del server che erano validi per il server esistente quando è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Queste regole devono essere impostate separatamente per questo nuovo server. Vengono ripristinate le regole del firewall del server originale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Personalizzare i parametri del server di database di Azure per MySQL con PowerShell](howto-configure-server-parameters-using-powershell.md)
