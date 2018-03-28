---
title: PowerShell per l'alias DNS per Azure SQL | Microsoft Docs
description: I cmdlet di PowerShell, ad esempio New-AzureRMSqlServerDNSAlias, consentono di reindirizzare le nuove connessioni client a un server di database SQL di Azure diverso senza modificare la configurazione client.
keywords: DNS per il database SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: 2aae669364c3de2e9b00ffe8e28b1521d3a781e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell per l'alias DNS per il database SQL di Azure

Questo articolo fornisce uno script di PowerShell che illustra come gestire un alias DNS per il database SQL di Azure. Lo script esegue i cmdlet seguenti che svolgono le operazioni indicate:


I cmdlet usati nell'esempio di codice sono i seguenti:
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): crea un nuovo alias DNS nel sistema del servizio di database SQL di Azure. L'alias fa riferimento al server di database SQL di Azure 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): ottiene ed elenca tutti gli alias DNS assegnati al server di database SQL 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): modifica il nome del server a cui l'alias fa riferimento dal server 1 al server di database SQL 2.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): rimuove l'alias DNS dal server di database SQL 2, usando il nome dell'alias.


I cmdlet di PowerShell precedenti sono stati aggiunti al modulo **AzureRm.Sql** a partire dalla versione 5.1.1.




#### <a name="dns-alias-in-connection-string"></a>Alias DNS nella stringa di connessione

Per connettere un determinato server di database SQL di Azure, un client come SQL Server Management Studio (SSMS) può specificare il nome dell'alias DNS al posto del nome reale del server. Nella stringa server di esempio seguente, l'alias *any-unique-alias-name* sostituisce il primo nodo delimitato da punto nella stringa server di quattro nodi:
- Stringa server di esempio: `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>prerequisiti

Se si vuole eseguire lo script di PowerShell di esempio di questo articolo, sono necessari i prerequisiti seguenti:

- Una sottoscrizione e un account di Azure. Per ottenere una versione di valutazione gratuita, fare clic su [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].

- Modulo di Azure PowerShell, con il cmdlet **New-AzureRMSqlServerDNSAlias**.
    - Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell][install-azurerm-ps-84p].
    - Eseguire `Get-Module -ListAvailable AzureRM;` in powershell\_ise.exe per trovare la versione.

- Due server di database SQL di Azure.

## <a name="code-example"></a>Esempio di codice

Il codice di PowerShell di esempio seguente inizia assegnando valori letterali a diverse variabili. Per eseguire il codice, è prima di tutto necessario modificare tutti i valori segnaposto sostituendoli con i valori reali del sistema. In alternativa, è possibile semplicemente esaminare il codice. Viene fornito anche l'output della console del codice.


```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Login-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>Output effettivo della console per l'esempio di PowerShell

L'output della console seguente è stato copiato e incollato da un'esecuzione effettiva.


```
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.
 
[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount : 

 
[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName    
----------------- ----------         ------------------    
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>> 
```

## <a name="next-steps"></a>Passaggi successivi

Per una spiegazione completa della funzionalità di alias DNS per il database SQL, vedere [Alias DNS per il database SQL di Azure][dns-alias-overview-37v].



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

