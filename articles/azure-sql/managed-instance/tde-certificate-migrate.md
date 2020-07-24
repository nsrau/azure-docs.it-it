---
title: Eseguire la migrazione del certificato Transparent Data-istanza gestita
description: Eseguire la migrazione di un certificato che protegge la chiave di crittografia del database di un database con Transparent Data Encryption ad Azure SQL Istanza gestita
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: ba2dd167cdf49b5f1a4b4f2dcd0edd48ea969fae
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073328"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Eseguire la migrazione di un certificato di un database protetto con Transparent Data Encryption in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Quando si esegue la migrazione di un database protetto da Transparent Data Encryption (Transparent Data [Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) ad Azure SQL istanza gestita con l'opzione di ripristino nativo, è necessario eseguire la migrazione del certificato corrispondente dall'istanza di SQL Server prima del ripristino del database. Questo articolo illustra il processo di migrazione manuale del certificato al Istanza gestita SQL di Azure:

> [!div class="checklist"]
>
> * Esportare il certificato in un file di scambio di informazioni personali (con estensione pfx)
> * Estrarre il certificato da un file in una stringa in base 64
> * Caricarla usando un cmdlet di PowerShell

Per un'opzione alternativa che usa un servizio completamente gestito per la migrazione senza problemi di un database protetto con Transparent Data Encryption e di un certificato corrispondente, vedere [come eseguire la migrazione del database locale in Azure SQL istanza gestita usando il servizio migrazione del database di Azure](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Un certificato migrato viene usato solo per il ripristino del database protetto da Transparent Data Encryption. Al termine del ripristino, il certificato migrato viene sostituito da una protezione diversa, ovvero un certificato gestito dal servizio o una chiave asimmetrica dall'insieme di credenziali delle chiavi, a seconda del tipo di Transparent Data Encryption impostato nell'istanza.

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

* Strumento da riga di comando [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) installato nel server locale o in un altro computer con accesso al certificato esportato come file. Lo strumento Pvk2Pfx fa parte di [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), un ambiente della riga di comando autonomo.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) versione 5.0 o successiva installato.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Assicurarsi che sia disponibile quanto segue:

* Modulo Azure PowerShell [installato e aggiornato](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [AZ. SQL Module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Istanza gestita SQL di Azure, ma tutto lo sviluppo futuro riguarda il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRM sono sostanzialmente identici.

Eseguire i comandi seguenti in PowerShell per installare/aggiornare il modulo:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Esportare il certificato Transparent Data Encryption in un file con estensione pfx

Il certificato può essere esportato direttamente dall'istanza di SQL Server di origine o dall'archivio certificati se viene mantenuto.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Esportare il certificato dall'istanza di SQL Server di origine

Usare la procedura seguente per esportare il certificato con SQL Server Management Studio e convertirlo in formato pfx. I nomi generici *TDE_Cert* e *full_path* vengono usati per i nomi di file e di certificati e i percorsi nei passaggi. che dovranno essere sostituiti con i nomi effettivi.

1. In SSMS aprire una nuova finestra di query e connettersi all'istanza di SQL Server di origine.

1. Usare lo script seguente per elencare i database protetti con Transparent Data Encryption e ottenere il nome del certificato che protegge la crittografia del database di cui eseguire la migrazione:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Elenco di certificati Transparent Data Encryption](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. Eseguire questo script per esportare il certificato in una coppia di file (con estensione cer e pvk) contenenti le informazioni relative alla chiave pubblica e privata:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Backup certificato Transparent Data Encryption](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Usare la console di PowerShell per copiare le informazioni del certificato da una coppia di file appena creati in un file con estensione pfx, usando lo strumento Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Esportare il certificato da un archivio certificati

Se il certificato viene mantenuto nell'archivio certificati del computer locale SQL Server, può essere esportato seguendo questa procedura:

1. Aprire la console di PowerShell ed eseguire il comando seguente per aprire lo snap-in certificati di Microsoft Management Console:

   ```cmd
   certlm
   ```

2. Nello snap-in MMC certificati espandere il percorso Personal > Certificates per visualizzare l'elenco dei certificati.

3. Fare clic con il pulsante destro del mouse sul certificato e scegliere **Esporta**.

4. Eseguire la procedura guidata per esportare il certificato e la chiave privata in un formato pfx.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Caricare il certificato in Istanza gestita SQL di Azure usando un cmdlet Azure PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Per iniziare, eseguire i passaggi di preparazione in PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Al termine di tutti i passaggi di preparazione, eseguire i comandi seguenti per caricare il certificato con codifica base 64 nell'istanza gestita di destinazione:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È necessario prima di tutto [configurare un insieme](/azure/key-vault/key-vault-manage-with-cli2) di credenziali delle chiavi di Azure con il file con *estensione pfx* .

1. Per iniziare, eseguire i passaggi di preparazione in PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Al termine di tutti i passaggi di preparazione, eseguire i comandi seguenti per caricare il certificato con codifica base 64 nell'istanza gestita di destinazione:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Il certificato è ora disponibile per l'istanza gestita specificata ed è possibile ripristinare correttamente il backup del database protetto con Transparent Data Encryption corrispondente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire la migrazione di un certificato che protegge la chiave di crittografia di un database con Transparent Data Encryption dall'istanza di SQL Server locale o IaaS al Istanza gestita SQL di Azure.

Vedere [ripristinare un backup del database in un istanza gestita SQL di Azure](restore-sample-database-quickstart.md) per informazioni su come ripristinare un backup del database in azure SQL istanza gestita.
