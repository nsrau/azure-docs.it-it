---
title: Eseguire la migrazione del certificato Transparent Data-istanza gestita
description: Eseguire la migrazione a un'istanza gestita del database SQL di Azure del certificato di protezione della chiave di crittografia di un database con Transparent Data Encryption
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: f0bc66260a0eaa66e71241148591146fa38d4120
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821797"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Eseguire la migrazione del certificato di un database protetto tramite TDE a un'istanza gestita del database SQL di Azure

In caso di migrazione di un database protetto tramite [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) a un'istanza gestita di database SQL di Azure con l'opzione del ripristino nativo, prima di ripristinare il database è necessario eseguire la migrazione del certificato corrispondente dall'istanza locale o IaaS di SQL Server. Questo articolo illustra il processo di migrazione manuale del certificato a Istanza gestita di database SQL di Azure:

> [!div class="checklist"]
> * Esportare il certificato in un file PFX (Personal Information Exchange)
> * Estrarre il certificato dal file a una stringa Base 64
> * Caricare il certificato con il cmdlet di PowerShell

Per un'opzione alternativa basata sull'uso di un servizio completamente gestito per una migrazione senza problemi sia del database protetto tramite TDE che del certificato corrispondente, vedere l'articolo su [come eseguire la migrazione di un database locale a Istanza gestita con Servizio Migrazione del database di Azure](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Il certificato di cui viene eseguita la migrazione viene usato solo per il ripristino del database protetto tramite TDE. Al termine del ripristino, il certificato migrato viene sostituito da una protezione diversa, ovvero un certificato gestito dal servizio o una chiave asimmetrica dall'insieme di credenziali delle chiavi, a seconda del tipo di Transparent Data Encryption impostato sull'istanza.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

- Strumento da riga di comando [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) installato nel server locale o in un altro computer con accesso al certificato esportato come file. La strumento Pvk2Pfx fa parte di [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), un ambiente della riga di comando completo e autonomo.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) versione 5.0 o successiva installato.
- Modulo Azure PowerShell [installato e aggiornato](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [AZ. SQL Module](https://www.powershellgallery.com/packages/Az.Sql).
  Eseguire questi comandi in PowerShell per installare/aggiornare il modulo di PowerShell:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Esportare il certificato TDE in un file PFX (Personal Information Exchange)

Il certificato può essere esportato direttamente dall'istanza di SQL Server di origine oppure dall'archivio certificati, se si trova in tale archivio.

### <a name="export-certificate-from-the-source-sql-server"></a>Esportare il certificato dall'istanza di SQL Server di origine

Usare la procedura seguente per esportare il certificato con SQL Server Management Studio e convertirlo in formato PFX. Nella procedura, come nomi e percorsi del certificato e del file vengono usati i nomi generici *TDE_Cert* e *full_path*, che dovranno essere sostituiti con i nomi effettivi.

1. In SSMS aprire una nuova finestra di query e connettersi all'istanza di SQL Server di origine.
2. Usare lo script seguente per elencare i database protetti tramite TDE e ottenere il nome del certificato di protezione della crittografia del database di cui deve essere eseguita la migrazione:

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

   ![Elenco dei certificati TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. Eseguire questo script per esportare il certificato in una coppia di file (con estensione cer e pvk) contenenti le informazioni relative alla chiave pubblica e privata:

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

   ![Eseguire il backup del certificato TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. Usare la console PowerShell per copiare le informazioni del certificato da una coppia di file appena creata a un file PFX (Personal Information Exchange) usando lo strumento Pvk2Pfx:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Esportare il certificato dall'archivio certificati

Se il certificato si trova nell'archivio certificati del computer locale di SQL Server, può essere esportato seguendo questa procedura:

1. Aprire la console PowerShell ed eseguire questo comando per aprire lo snap-in Certificati di Microsoft Management Console:

   ```powershell
   certlm
   ```

2. Nello snap-in Certificati di MMC espandere il percorso Personale -> Certificati per visualizzare l'elenco dei certificati.

3. Fare clic con il pulsante destro del mouse sul certificato e quindi scegliere Esporta.

4. Seguire la procedura guidata per esportare il certificato e la chiave privata nel formato PFX.

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Caricare il certificato in un'istanza gestita del database SQL di Azure con il cmdlet di Azure PowerShell

1. Per iniziare, eseguire i passaggi di preparazione in PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. Al termine dei passaggi di preparazione, eseguire questi comandi per caricare il certificato con codifica Base 64 nell'istanza gestita di destinazione:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Il certificato è ora disponibile per l'istanza gestita specificata e il backup del database protetto tramite TDE corrispondente può essere ripristinato correttamente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come eseguire la migrazione del certificato di protezione della chiave di crittografia del database con Transparent Data Encryption, dall'istanza locale o IaaS di SQL Server a un'istanza gestita del database SQL di Azure.

Per informazioni su come ripristinare un backup di database in un'istanza gestita di database SQL di Azure, vedere [Ripristinare un backup di database in un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started-restore.md).
