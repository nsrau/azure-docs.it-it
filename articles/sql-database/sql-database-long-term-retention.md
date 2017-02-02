---
title: Archiviazione di backup del database SQL di Azure per un massimo di 10 anni | Documentazione Microsoft
description: Informazioni sul supporto del database SQL di Azure dell&quot;archiviazione dei backup per un massimo di 10 anni.
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 8b13faf1f6cdac355cc4d22b825cc2362a50e8f9


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Archiviazione di backup del database SQL di Azure per un massimo di 10 anni
Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup automatici del database completo oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL.

La funzionalità di **conservazione dei backup a lungo termine** consente di archiviare i backup del database SQL di Azure in un insieme di credenziali dei servizi di ripristino di Azure fino a 10 anni. È possibile archiviare fino a 1000 database per ogni insieme di credenziali. È possibile selezionare qualsiasi backup nell'insieme di credenziali per ripristinarlo come nuovo database.

> [!NOTE]
> È possibile abilitare fino a 200 database per ogni insieme di credenziali in un periodo di 24 ore. È pertanto consigliabile usare insiemi di credenziali separati per ogni server al fine di ridurre al minimo l'impatto di questo limite. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione a lungo termine dei backup consente di associare un server del database SQL di Azure a un insieme di credenziali di Servizi di ripristino di Azure. 

* L'insieme di credenziali deve essere creato nella stessa sottoscrizione di Azure in cui è stato creato il server SQL, nonché nella stessa area geografica e gruppo di risorse. 
* È quindi possibile configurare un criterio di conservazione per qualsiasi database. Il criterio consente di copiare i backup settimanali del database completo nell'insieme di credenziali dei servizi di ripristino e di conservare i backup per il periodo di memorizzazione specificato (fino a 10 anni). 
* Sarà quindi possibile ripristinare una copia di questi backup in un nuovo database in qualsiasi server nella sottoscrizione. La copia viene eseguita dal servizio di archiviazione di Azure partendo da backup esistenti e non ha alcun impatto sulle prestazioni del database esistente.

## <a name="how-do-i-enable-long-term-retention"></a>Attivazione della conservazione a lungo termine

Per configurare la conservazione a lungo termine dei backup per un database:

1. Creare un insieme di credenziali dei servizi di ripristino di Azure nella stessa area, sottoscrizione e gruppo di risorse in cui si trova il server del database SQL. 
2. Registrare il server nell'insieme di credenziali
3. Creare un criterio di protezione per i servizi di ripristino di Azure
4. Applicare il criterio di protezione ai database che richiedono la conservazione dei backup a lungo termine

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>Come ripristinare un database archiviato con la funzionalità di conservazione a lungo termine

Per ripristinare un database da un backup di conservazione a lungo termine:

1. Elencare l'insieme di credenziali in cui è archiviato il backup
2. Elencare il contenitore in cui viene eseguito il mapping al server logico
3. Elencare l'origine dei dati all'interno dell'insieme di credenziali in cui viene eseguito il mapping al database
4. Elencare i punti di ripristino disponibili per il ripristino
5. Eseguire il ripristino dal punto di ripristino verso il server di destinazione all'interno della sottoscrizione

## <a name="how-much-does-long-term-retention-cost"></a>Costi della conservazione a lungo termine

Il costo della conservazione a lungo termine di un database SQL di Azure dipende dalle [tariffe dei servizi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

Dopo che il server del database SQL di Azure viene registrato nell'insieme di credenziali, viene addebitata l'archiviazione totale usata per i backup settimanali archiviati nell'insieme di credenziali.

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Configurazione della conservazione a lungo termine nel portale di Azure

Nel pannello del server del database SQL di Azure è possibile configurare la conservazione a lungo termine e, se necessario, creare un insieme di credenziali dei servizi di ripristino di Azure.

- Per configurare la conservazione a lungo termine dei backup automatici nell'insieme di credenziali di Servizi di ripristino di Azure, vedere [Configurare la conservazione a lungo termine dei backup](sql-database-configure-long-term-retention.md)
- Per ripristinare un database da un backup con conservazione a lungo termine, vedere [Eseguire il ripristino da un backup con conservazione a lungo termine](sql-database-restore-from-long-term-retention.md)
- Per visualizzare i backup nell'insieme di credenziali di Servizi di ripristino di Azure, vedere [Visualizzare i backup con conservazione a lungo termine](sql-database-view-backups-in-vault.md)

> [!TIP]
> Per un'esercitazione, vedere [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery.md)
>

## <a name="configuring-long-term-retention-using-powershell"></a>Configurazione della conservazione a lungo termine tramite PowerShell

Attenersi alla procedura seguente per configurare la conservazione a lungo termine tramite PowerShell.
1. Creare un insieme di credenziali dei servizi di ripristino
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. Registrare il server del database SQL di Azure nell'insieme di credenziali in modo che i backup del database all'interno del server possano essere archiviati a lungo termine.
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. Creare un criterio di conservazione per l'archiviazione dei backup.
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. Abilitare la conservazione a lungo termine per il database SQL da backup archiviati in un insieme di credenziali.
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. Elencare il server associato all'insieme di credenziali. Ogni server è associato a un contenitore specifico nell'insieme di credenziali. È possibile elencare i server registrati tramite i comandi seguenti:
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. Elencare i database che dispongono di un criterio di conservazione nel contenitore. Ogni database dispone di un elemento di backup associato nel rispettivo contenitore. Il nome dell'elemento di backup deriva dal nome del database.
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>Ripristinare un database da un backup di conservazione a lungo termine

Attenersi alla procedura seguente per ripristinare un database da un backup nell'insieme di credenziali del servizio di ripristino di Azure:

1. Trovare il contenitore dei servizi di ripristino associato al server SQL.
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. Trovare l'elemento di backup associato al database.
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. Trovare il backup da cui si desidera eseguire il ripristino.
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. Eseguire il ripristino dal punto di ripristino in un nuovo database SQL di Azure.
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>Disattivazione della conservazione a lungo termine

Il servizio di ripristino gestisce in automatico l'eliminazione dei backup in base ai criteri di conservazione specificati. 

* Per interrompere l'invio di backup di un database specifico all'insieme di credenziali, rimuovere i criteri di conservazione relativi al database specifico.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> I backup che si trovano già nell'insieme di credenziali non subiscono variazioni. I backup vengono automaticamente eliminati dal servizio di ripristino alla scadenza del periodo di conservazione.
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Eliminazione dei backup dall'insieme di credenziali dei servizi di ripristino di Azure

Per rimuovere manualmente i backup dall'insieme di credenziali.

1. Identificare il contenitore nell'insieme di credenziali per 'myserver'.
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. Individuare l'elemento di backup da eliminare.
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. Eliminare gli elementi di backup (tutti i backup per il database 'mydb')
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. Eliminare il contenitore associato a 'myserver'.
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>Domande frequenti sulla conservazione a lungo termine:

1. D: È possibile eliminare manualmente backup specifici nell'insieme di credenziali?
   R: Non in questo momento, ma l'insieme di credenziali pulisce automaticamente i backup quando il periodo di conservazione è scaduto.
2. D: È possibile registrare un server per archiviare backup di più di un insieme di credenziali?
   R: No, oggi è possibile archiviare solo i backup di un inseme di credenziali per volta.
3. D: È possibile disporre di un insieme di credenziali e un server in sottoscrizioni diverse?
   R: No, attualmente l'insieme di credenziali e il server devono trovarsi nella stessa sottoscrizione e nello stesso gruppo di risorse.
4. D: È possibile usare un insieme di credenziali creato in un'area diversa rispetto all'area del mio server?
   R: No, l'insieme di credenziali e il server devono trovarsi nella stessa area per ridurre al minimo il tempo di copia ed evitare costi di traffico.
5. D: Quanti database è possibile archiviare in un insieme di credenziali?
   R: Attualmente è supportato un massimo di 1000 database per ogni insieme di credenziali. 
6. D: Quanti insiemi di credenziali è possibile creare per ogni sottoscrizione? R. È possibile creare 25 insiemi di credenziali per ogni sottoscrizione.
7. D: Quanti database è possibile configurare al giorno per ogni insieme di credenziali? R: È possibile configurare solo fino a 200 database al giorno per ogni insieme di credenziali.
8. D: La conservazione a lungo termine funziona con i pool elastici?
   A: Sì. Qualsiasi database nel pool può essere configurato con i criteri di conservazione.
9. D: È possibile scegliere l'ora di creazione del backup?
   R: No, il database SQL controlla la pianificazione dei backup per ridurre al minimo l'impatto sulle prestazioni dei database.
10. D: Ho attivato la TDE per il database. È possibile utilizzarla nell'insieme di credenziali? R: Sì, la TDE è supportata. Anche se il database originale non esiste più, è possibile ripristinare il database dall'insieme di credenziali.
11. D: Cosa succede ai backup nell'insieme di credenziali se la mia sottoscrizione viene sospesa? R. Se la sottoscrizione viene sospesa, manteniamo i database e i backup esistenti, ma i nuovi backup non vengono copiati nell'insieme di credenziali. Dopo la riattivazione della sottoscrizione, il servizio riprende la copia dei backup nell'insieme di credenziali. L'insieme di credenziali diventa inaccessibile per le operazioni di ripristino che usano i backup copiati prima della sospensione della sottoscrizione. 
12. D: È possibile accedere ai file di backup del database SQL per scaricarli o ripristinarli in SQL Server?
   R: No, non attualmente.
13. D: È possibile disporre di più pianificazioni (giornaliera, settimanale, mensile, annuale) in un criterio di conservazione SQL?
   R: No, attualmente questa funzione è disponibile solo per il backup di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi
I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).




<!--HONumber=Dec16_HO2-->


