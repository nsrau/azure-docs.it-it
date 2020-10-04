---
title: Backup e ripristino-Azure PowerShell-database di Azure per PostgreSQL
description: Informazioni su come eseguire il backup e il ripristino di un server in database di Azure per PostgreSQL usando Azure PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cf15898a7077f08ff4ab337cf5ad77ebcd2f3f1a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708102"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-postgresql-server-using-powershell"></a>Come eseguire il backup e il ripristino di un server di Database di Azure per PostgreSQL con PowerShell

Il backup dei server di database di Azure per PostgreSQL viene eseguito periodicamente per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- [Database di Azure per il server PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Poiché il modulo Az.PostgreSql di PowerShell è in fase di anteprima, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Quando il modulo Az.PostgreSql di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

Al momento della creazione del server, è possibile scegliere tra la configurazione del server per i backup con ridondanza locale o con ridondanza geografica.

> [!NOTE]
> Dopo aver creato un server, il tipo di ridondanza, con ridondanza geografica e con ridondanza locale, non può essere modificato.

Durante la creazione di un server tramite il `New-AzPostgreSqlServer` comando, il parametro **GeoRedundantBackup** decide l'opzione di ridondanza del backup. Se **abilitata**, vengono eseguiti backup con ridondanza geografica. In alternativa, se **disabilitato**, vengono eseguiti backup con ridondanza locale.

Il periodo di conservazione dei backup è impostato dal parametro **BackupRetentionDay** .

Per altre informazioni sull'impostazione di questi valori durante la creazione del server, vedere [creare un database di Azure per il server PostgreSQL usando PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md).

È possibile modificare il periodo di conservazione dei backup di un server nel modo seguente:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

L'esempio precedente modifica il periodo di conservazione dei backup di mydemoserver impostandolo su 10 giorni.

Il periodo di conservazione dei backup determina il modo in cui è possibile recuperare un ripristino temporizzato, perché si basa su backup disponibili. Il ripristino temporizzato è descritto in modo più dettagliato nella sezione seguente.

## <a name="server-point-in-time-restore"></a>Ripristino temporizzato del server

È possibile ripristinare il server in base a una temporizzazione precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente non viene modificato. Se, ad esempio, una tabella è stata eliminata involontariamente, è possibile eseguire il ripristino a un qualsiasi momento prima dell'eliminazione. È possibile quindi recuperare la tabella e i dati mancanti dalla copia ripristinata del server.

Per ripristinare il server, usare il cmdlet `Restore-AzPostgreSqlServer` di PowerShell.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino

Per ripristinare il server, eseguire l'esempio seguente da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Il set di parametri **PointInTimeRestore** del `Restore-AzPostgreSqlServer` cmdlet richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| Name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selezionare un momento specifico per il ripristino. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. Ad esempio, è possibile usare il proprio fuso orario locale, ad esempio **2020-03-13T05:59:00-08:00**. È anche possibile usare il formato UTC Zulu, ad esempio **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Usare la modalità temporizzata per il ripristino. |

Quando si ripristina un server a una temporizzazione precedente, viene creato un nuovo server. Il server originale e i database della temporizzazione specificata vengono copiati nel nuovo server.

I valori relativi al percorso e al piano tariffario per il server ripristinato sono gli stessi del server di origine.

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha il nome e la password di accesso dell'amministratore del server che erano validi per il server esistente quando è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Queste regole devono essere configurate separatamente per il nuovo server. Vengono ripristinate le regole del firewall del server originale.

## <a name="geo-restore"></a>Ripristino geografico

Se il server è stato configurato per i backup con ridondanza geografica, è possibile creare un nuovo server dal backup del server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per PostgreSQL.

Per creare un server usando un backup con ridondanza geografica, usare il `Restore-AzPostgreSqlServer` comando con il parametro **UseGeoRestore** .

> [!NOTE]
> Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.

Per il ripristino geografico del server, eseguire l'esempio seguente da PowerShell:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Questo esempio crea un nuovo server denominato **mydemoserver-georipristinato** nell'area Stati Uniti orientali che appartiene a **myresourcegroup**. Si tratta di un server per utilizzo generico di quinta generazione con otto vCore. Il server viene creato dal backup con ridondanza geografica di **mydemoserver**, anche nel gruppo di risorse **myresourcegroup**.

Per creare il nuovo server in un gruppo di risorse diverso dal server esistente, specificare il nome del nuovo gruppo di risorse usando il parametro **ResourceGroupName** , come illustrato nell'esempio seguente:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Il set di parametri **Georestore** del `Restore-AzPostgreSqlServer` cmdlet richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Nome del gruppo di risorse a cui appartiene il nuovo server.|
|Name | mydemoserver-georestored | Nome del nuovo server. |
|Percorso | eastus | Posizione del nuovo server. |
|UseGeoRestore | `<SwitchParameter>` | Usare la modalità geografica per il ripristino. |

Quando si crea un nuovo server con il ripristino geografico, esso eredita le stesse dimensioni di archiviazione e il piano tariffario del server di origine, a meno che non venga specificato il parametro **SKU** .

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha il nome e la password di accesso dell'amministratore del server che erano validi per il server esistente quando è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Queste regole devono essere impostate separatamente per questo nuovo server. Vengono ripristinate le regole del firewall del server originale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come generare una stringa di connessione per database di Azure per PostgreSQL con PowerShell](howto-connection-string-powershell.md)
