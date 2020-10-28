---
title: 'Avvio rapido: Creare un server - Azure PowerShell - Database di Azure per MariaDB'
description: Questa guida di avvio rapido descrive come usare PowerShell per creare un server di Database di Azure per MariaDB in un gruppo di risorse di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 7db89d315e0df51aad7f4660ec5da64425eae2aa
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424437"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Avvio rapido: Creare un server di Database di Azure per MariaDB usando PowerShell

Questa guida di avvio rapido descrive come usare PowerShell per creare un server di Database di Azure per MariaDB in un gruppo di risorse di Azure. È possibile usare PowerShell per creare e gestire le risorse di Azure in modo interattivo o negli script.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MariaDb PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Quando il modulo Az.MariaDb di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se è la prima volta che si usa il servizio Database di Azure per MariaDB, è necessario registrare il provider di risorse **Microsoft.DBforMariaDB** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB

Creare un server di Database di Azure per MariaDB con il cmdlet `New-AzMariaDbServer`. Un server può gestire più database. In genere, viene usato un database separato per ogni progetto o per ogni utente.

La tabella seguente contiene un elenco di parametri e valori di esempio usati di frequente per il cmdlet `New-AzMariaDbServer`.

|        **Impostazione**         | **Valore di esempio** |                                                                                                                                                             **Descrizione**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome                       | mydemoserver     | Scegliere un nome univoco a livello globale in Azure per identificare il server di Database di Azure per MariaDB. Il nome può contenere solo lettere, numeri e trattini. I caratteri maiuscoli specificati vengono automaticamente convertiti in minuscoli durante il processo di creazione. e deve avere una lunghezza compresa tra 3 e 63 caratteri. |
| ResourceGroupName          | myresourcegroup  | Specificare il nome del gruppo di risorse di Azure.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | Il nome della SKU. Segue la convenzione **piano tariffario\_generazione di calcolo\_vCore** in sintassi abbreviata. Per altre informazioni sul parametro Sku, vedere le informazioni disponibili dopo questa tabella.                                                                                                                                           |
| BackupRetentionDay         | 7                | Specifica per quanto tempo deve essere conservato un backup. L'unità è giorni. L'intervallo è da 7 a 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Attivato          | Indica se abilitare i backup con ridondanza geografica per questo server. Questo valore non può essere abilitato per i server nel piano tariffario Basic e non può essere modificato dopo la creazione del server. Valori consentiti: Enabled, Disabled.                                                                                                      |
| Location                   | westus           | Area di Azure per il server.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Attivato          | Indica se abilitare SSL per questo server. Valori consentiti: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Capacità di archiviazione del server (l'unità è MB). Per essere valido, StorageInMb deve essere di almeno 5120 MB con aumenti in incrementi di 1024 MB. Per altre informazioni sui limiti delle dimensioni di archiviazione, vedere [Piani tariffari di Database di Azure per MariaDB](./concepts-pricing-tiers.md).                                                                               |
| Versione                    | 5.7              | La versione principale di MariaDB.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Nome utente per l'account di accesso dell'amministratore. Non può essere **azure_superuser** , **admin** , **administrator** , **root** , **guest** o **public** '.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Password dell'utente amministratore sotto forma di stringa sicura. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.                                       |

Il valore del parametro **Sku** segue la convenzione **piano tariffario\_generazione calcolo\_vCore** come illustrato negli esempi seguenti.

- `-Sku B_Gen5_1` esegue il mapping a Basic, Gen 5 e 1 vCore. Questa opzione corrisponde allo SKU più piccolo disponibile.
- `-Sku GP_Gen5_32` esegue il mapping a utilizzo generico, Gen 5 e 32 vCore.
- `-Sku MO_Gen5_2` esegue il mapping a ottimizzazione per la memoria, Gen 5 e 2 vCore.

Per informazioni sui valori di **Sku** validi per aree e livelli, vedere [Piani tariffari di Database di Azure per MariaDB](./concepts-pricing-tiers.md).

Nell'esempio seguente nell'area **Stati Uniti occidentali** viene creato un server MariaDB denominato **mydemoserver** nel gruppo di risorse **myresourcegroup** con l'account di accesso di amministratore del server **myadmin** . Si tratta di un server di quinta generazione nel piano tariffario per utilizzo generico con 2 vCore e con i backup con ridondanza geografica abilitati. Prendere nota della password usata nella prima riga dell'esempio, perché si tratta della password per l'account amministratore del server MariaDB.

> [!TIP]
> Il nome del server è associato a un nome DNS e deve essere univoco a livello globale.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni di I/O sono adeguate per il carico di lavoro.

> [!IMPORTANT]
> Si noti che per i server creati nel piano tariffario Basic non è possibile aumentare le prestazioni in un secondo momento passando a un piano per utilizzo generico o con ottimizzazione della memoria e non è possibile abilitare la replica geografica.

## <a name="configure-a-firewall-rule"></a>Configurare una regola del firewall

Creare una regola del firewall a livello di server per Database di Azure per MariaDB usando il cmdlet `New-AzMariaDbFirewallRule`. Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio lo strumento della riga di comando `mysql` o MariaDB Workbench, di connettersi al server tramite il firewall del servizio Database di Azure per MariaDB.

L'esempio seguente crea una regola del firewall denominata **AllowMyIP** che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire con un indirizzo IP o un intervallo di indirizzi IP corrispondente alla posizione da cui ci si connette.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Le connessioni al database di Azure per MariaDB comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, è possibile connettersi solo al server se il reparto IT apre la porta 3306.

## <a name="configure-ssl-settings"></a>Configurare le impostazioni SSL

Per impostazione predefinita, tra il server e le applicazioni client vengono applicate connessioni SSL. Questa impostazione predefinita garantisce la sicurezza dei dati _in movimento_ tramite la crittografia del flusso dei dati tramite Internet. Per questa guida introduttiva, disabilitare le connessioni SSL per il server. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MariaDB](./howto-configure-ssl.md).

> [!WARNING]
> Si sconsiglia di disabilitare le connessioni SSL per i server di produzione.

L'esempio seguente disabilita SSL nel server di Database di Azure per MariaDB.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso. Usare questo esempio per determinare le informazioni di connessione. Prendere nota dei valori per **FullyQualifiedDomainName** e **AdministratorLogin** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Connettersi al server usando lo strumento da riga di comando mysql

Connettersi al server tramite lo strumento da riga di comando `mysql`. Per scaricare e installare lo strumento da riga di comando, vedere [Download della community MySQL](https://dev.mysql.com/downloads/shell/). È anche possibile accedere a una versione preinstallata dello strumento da riga di comando `mysql` in Azure Cloud Shell selezionando il pulsante **Prova** in un esempio di codice in questo articolo. In alternativa, per accedere ad Azure Cloud Shell è possibile selezionare il pulsante **>_** sulla barra degli strumenti in alto a destra nel portale di Azure oppure visitare la pagina [shell.azure.com](https://shell.azure.com/).

1. Connettersi al server usando lo strumento da riga di comando `mysql`.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Visualizzare lo stato del server.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

Per altri comandi, vedere il capitolo 4.5.1 di [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manuale di riferimento di MySQL 5.7).

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Connettersi al server con MariaDB Workbench

1. Avviare l'applicazione MySQL Workbench nel computer client. Per scaricare e installare MySQL Workbench, vedere [Scaricare MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

   ![Setup New Connection (Configura nuova connessione)](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Impostazione**    |            **Valore consigliato**            |                      **Descrizione**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Connection Name (Nome connessione)   | Connessione in uso                             | Specificare un'etichetta per la connessione                        |
    | Connection Method (Metodo di connessione) | Standard (TCP/IP)                         | Usare il protocollo TCP/IP per connettersi a Database di Azure per MariaDB |
    | nomehost          | `mydemoserver.mariadb.database.azure.com` | Nome del server annotato in precedenza                           |
    | Porta              | 3306                                      | Porta predefinita per MariaDB                                 |
    | Username          | myadmin@mydemoserver                      | Accesso amministratore server annotato in precedenza                |
    | Password          | *************                             | Usare la password dell'account amministratore configurata in precedenza      |

1. Fare clic su **Test connessione** per verificare che tutti i parametri siano configurati correttamente.

1. Selezionare la connessione per connettersi al server.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse usate in questa guida di avvio rapido non sono necessarie per un'altra guida o esercitazione, è possibile eliminarle usando il comando seguente.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questa guida di avvio rapido, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Per eliminare solo il server creato in questa guida di avvio rapido senza eliminare il gruppo di risorse, usare il cmdlet `Remove-AzMariaDbServer`.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare un'istanza di Database di Azure per MariaDB con PowerShell](tutorial-design-database-using-powershell.md)