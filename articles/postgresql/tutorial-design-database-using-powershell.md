---
title: 'Esercitazione: Progettare un database di Azure per PostgreSQL - Server singolo - Azure PowerShell'
description: Questa esercitazione illustra come creare e configurare il primo database di Azure per PostgreSQL - Server singolo ed eseguire query usando Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73f5dc6bdc976bd0166cfb1f2621f57f558f944c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478593"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Esercitazione: Progettare un database di Azure per PostgreSQL - Server singolo usando PowerShell

Database di Azure per PostgreSQL è un servizio di database relazionale nel cloud Microsoft basato sul motore di database PostgreSQL Community Edition. Questa esercitazione illustra come eseguire le operazioni seguenti usando PowerShell e altre utilità:

> [!div class="checklist"]
> - Creare un database di Azure per PostgreSQL
> - Configurare il firewall del server
> - Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> - Caricare dati di esempio
> - Eseguire query sui dati
> - Aggiornare i dati
> - Ripristinare i dati

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Poiché il modulo Az.PostgreSql di PowerShell è in fase di anteprima, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Quando il modulo Az.PostgreSql di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se è la prima volta che si usa il servizio Database di Azure per PostgreSQL, è necessario registrare il provider di risorse **Microsoft.DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare un ID sottoscrizione specifico usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nell'area **Stati Uniti occidentali** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server di Database di Azure per PostgreSQL con il cmdlet `New-AzPostgreSqlServer`. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

Nell'esempio seguente nell'area **Stati Uniti occidentali** viene creato un server PostgreSQL denominato **mydemoserver** nel gruppo di risorse **myresourcegroup** con l'account di accesso di amministratore del server **myadmin** . Si tratta di un server di quinta generazione nel piano tariffario per utilizzo generico con 2 vCore e con i backup con ridondanza geografica abilitati. Prendere nota della password usata nella prima riga dell'esempio, perché si tratta della password per l'account amministratore del server PostgreSQL.

> [!TIP]
> Il nome del server è associato a un nome DNS e deve essere univoco a livello globale.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Il valore del parametro **Sku** segue la convenzione **piano tariffario\_generazione calcolo\_vCore** come illustrato negli esempi seguenti.

- `-Sku B_Gen5_1` esegue il mapping a Basic, Gen 5 e 1 vCore. Questa opzione corrisponde allo SKU più piccolo disponibile.
- `-Sku GP_Gen5_32` esegue il mapping a utilizzo generico, Gen 5 e 32 vCore.
- `-Sku MO_Gen5_2` esegue il mapping a ottimizzazione per la memoria, Gen 5 e 2 vCore.

Per informazioni sui valori di **SKU** validi per aree e livelli, vedere [Piani tariffari di Database di Azure per PostgreSQL](./concepts-pricing-tiers.md).

È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni di I/O sono adeguate per il carico di lavoro.

> [!IMPORTANT]
> Si noti che per i server creati nel piano tariffario Basic non è possibile aumentare le prestazioni in un secondo momento passando a un piano per utilizzo generico o con ottimizzazione della memoria e non è possibile abilitare la replica geografica.

## <a name="configure-a-firewall-rule"></a>Configurare una regola del firewall

Creare una regola del firewall a livello di server di Database di Azure per PostgreSQL usando il cmdlet `New-AzPostgreSqlFirewallRule`. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando `psql` o PostgreSQL Workbench, di connettersi al server tramite il firewall del servizio Database di Azure per PostgreSQL.

L'esempio seguente crea una regola del firewall denominata **AllowMyIP** che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire con un indirizzo IP o un intervallo di indirizzi IP corrispondente alla posizione da cui ci si connette.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Le connessioni a Database di Azure per PostgreSQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, è possibile connettersi solo al server se il reparto IT apre la porta 3306.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso. Usare questo esempio per determinare le informazioni di connessione. Prendere nota dei valori per **FullyQualifiedDomainName** e **AdministratorLogin** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Connettersi al database PostgreSQL tramite psql

Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/current/static/app-psql.html) per connettersi a un server PostgreSQL Azure. È anche possibile accedere a una versione preinstallata dello strumento da riga di comando `psql` in Azure Cloud Shell selezionando il pulsante **Prova** in un esempio di codice in questo articolo. In alternativa, per accedere ad Azure Cloud Shell è possibile selezionare il pulsante **>_** sulla barra degli strumenti in alto a destra nel portale di Azure oppure visitare la pagina [shell.azure.com](https://shell.azure.com/).

1. Connettersi al server PostgreSQL di Azure usando l'utilità da riga di comando `psql`.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente permette di connettersi al database predefinito chiamato **postgres** nel server PostgreSQL `mydemoserver.postgres.database.azure.com` usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se si preferisce usare un percorso URL per connettersi a Postgres, l'URL codifica il segno @ nel nome utente con `%40`. Ad esempio, la stringa di connessione per psql sarà `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Dopo aver eseguito la connessione al server, creare un database vuoto nel prompt.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato **mypgsqldb** :

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database

Una volta appreso come connettersi al database del servizio Database di Azure per PostgreSQL, completare alcune attività di base.

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle

Dopo aver creato una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, ci sono due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle

Eseguire la query seguente per recuperare informazioni dalla tabella del database.

```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e aggiornare i dati nelle tabelle.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente

È possibile ripristinare il server in base a una temporizzazione precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente non viene modificato. Se, ad esempio, una tabella è stata eliminata involontariamente, è possibile eseguire il ripristino a un qualsiasi momento prima dell'eliminazione. È possibile quindi recuperare la tabella e i dati mancanti dalla copia ripristinata del server.

Per ripristinare il server, usare il cmdlet `Restore-AzPostgreSqlServer` di PowerShell.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino

Per ripristinare il server, eseguire l'esempio seguente da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Quando si ripristina un server a una temporizzazione precedente, viene creato un nuovo server. Il server originale e i database della temporizzazione specificata vengono copiati nel nuovo server.

I valori relativi al percorso e al piano tariffario per il server ripristinato sono gli stessi del server di origine.

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto. Il nuovo server ha il nome e la password di accesso dell'amministratore del server che erano validi per il server esistente quando è stato avviato il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Queste regole devono essere configurate separatamente per il nuovo server. Vengono ripristinate le regole del firewall del server originale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come eseguire il backup e il ripristino di un server di Database di Azure per PostgreSQL con PowerShell](howto-restore-server-powershell.md)