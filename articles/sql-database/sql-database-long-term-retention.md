---
title: Archiviazione di backup del database SQL di Azure per un massimo di 10 anni | Microsoft Docs
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
ms.date: 12/22/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 0cb96f97358378a723d7528e53f5c5a771e2000b
ms.lasthandoff: 03/14/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Archiviazione di backup del database SQL di Azure per un massimo di 10 anni
Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup automatici del database completo oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL. La funzionalità di **conservazione dei backup a lungo termine** consente di archiviare i backup del database SQL di Azure in un insieme di credenziali dei servizi di ripristino di Azure fino a 10 anni. È possibile archiviare fino a 1000 database per ogni insieme di credenziali. È possibile selezionare qualsiasi backup nell'insieme di credenziali per ripristinarlo come nuovo database.

> [!IMPORTANT]
> La conservazione dei backup a lungo termine è attualmente in anteprima ed è disponibile nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, India centrale, India meridionale, Giappone orientale, Giappone occidentale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Europa occidentale e Stati Uniti occidentali.
>

> [!NOTE]
> È possibile abilitare fino a 200 database per ogni insieme di credenziali in un periodo di 24 ore. È pertanto consigliabile usare insiemi di credenziali separati per ogni server al fine di ridurre al minimo l'impatto di questo limite. 
> 
> 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>Funzionamento della conservazione a lungo termine dei backup del database SQL

La conservazione a lungo termine dei backup consente di associare un server del database SQL di Azure a un insieme di credenziali di Servizi di ripristino di Azure. 

* L'insieme di credenziali deve essere creato nella stessa sottoscrizione di Azure in cui è stato creato il server SQL, nonché nella stessa area geografica e gruppo di risorse. 
* È quindi possibile configurare un criterio di conservazione per qualsiasi database. Il criterio consente di copiare i backup settimanali del database completo nell'insieme di credenziali dei servizi di ripristino e di conservare i backup per il periodo di memorizzazione specificato (fino a 10 anni). 
* Sarà quindi possibile ripristinare una copia di questi backup in un nuovo database in qualsiasi server nella sottoscrizione. La copia viene eseguita dal servizio di archiviazione di Azure partendo da backup esistenti e non ha alcun impatto sulle prestazioni del database esistente.

> [!TIP]
> Per un'esercitazione, vedere [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino tramite il portale di Azure](sql-database-get-started-backup-recovery-portal.md) o [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino con PowerShell](sql-database-get-started-backup-recovery-powershell.md)

## <a name="how-do-i-enable-long-term-backup-retention"></a>Attivazione della conservazione dei backup a lungo termine

Per configurare la conservazione a lungo termine dei backup per un database:

1. Creare un insieme di credenziali dei servizi di ripristino di Azure nella stessa area, sottoscrizione e gruppo di risorse in cui si trova il server del database SQL. 
2. Registrare il server nell'insieme di credenziali
3. Creare un criterio di protezione per i servizi di ripristino di Azure
4. Applicare il criterio di protezione ai database che richiedono la conservazione dei backup a lungo termine

Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere [Manage long-term backup retention usihg the Azure portal](sql-database-manage-long-term-backup-retention-portal.md) (Gestire la conservazione a lungo termine dei backup tramite il portale di Azure). Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere [Manage long-term backup retention usihg PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) (Gestire la conservazione a lungo termine dei backup tramite PowerShell).

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>Come ripristinare un database archiviato con la funzionalità di conservazione dei backup a lungo termine

Per ripristinare un database da un backup di conservazione a lungo termine:

1. Elencare l'insieme di credenziali in cui è archiviato il backup
2. Elencare il contenitore in cui viene eseguito il mapping al server logico
3. Elencare l'origine dei dati all'interno dell'insieme di credenziali in cui viene eseguito il mapping al database
4. Elencare i punti di ripristino disponibili per il ripristino
5. Eseguire il ripristino dal punto di ripristino verso il server di destinazione all'interno della sottoscrizione

Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere [Manage long-term backup retention usihg the Azure portal](sql-database-manage-long-term-backup-retention-portal.md) (Gestire la conservazione a lungo termine dei backup tramite il portale di Azure). Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere [Manage long-term backup retention usihg PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) (Gestire la conservazione a lungo termine dei backup tramite PowerShell).

## <a name="how-much-does-long-term-backup-retention-cost"></a>Costi della conservazione dei backup a lungo termine

Il costo della conservazione dei backup a lungo termine di un database SQL di Azure dipende dalle [tariffe dei servizi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

Dopo che il server del database SQL di Azure viene registrato nell'insieme di credenziali, viene addebitata l'archiviazione totale usata per i backup settimanali archiviati nell'insieme di credenziali.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>Visualizzare i backup disponibili archiviati nella conservazione dei backup a lungo termine

Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere [Manage long-term backup retention usihg the Azure portal](sql-database-manage-long-term-backup-retention-portal.md) (Gestire la conservazione a lungo termine dei backup tramite il portale di Azure). Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere [Manage long-term backup retention usihg PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) (Gestire la conservazione a lungo termine dei backup tramite PowerShell).

## <a name="disabling-long-term-retention"></a>Disattivazione della conservazione a lungo termine

Il servizio di ripristino gestisce in automatico l'eliminazione dei backup in base ai criteri di conservazione specificati. 

* Per interrompere l'invio di backup di un database specifico all'insieme di credenziali, rimuovere i criteri di conservazione relativi al database specifico.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> I backup che si trovano già nell'insieme di credenziali non subiscono variazioni. I backup vengono automaticamente eliminati dal servizio di ripristino alla scadenza del periodo di conservazione.


## <a name="removing-long-term-backup-retention-backups-from-the-azure-recovery-services-vault"></a>Eliminazione dei backup con conservazione a lungo termine dall'insieme di credenziali di Servizi di ripristino di Azure

Per rimuovere i backup con conservazione a lungo termine dall'insieme di credenziali, vedere [Delete long-term backup retention backups](sql-database-manage-long-term-backup-retention-powershell.md) (Eliminare i backup con conservazione a lungo termine)

## <a name="long-term-backup-retention-faq"></a>Domande frequenti sulla conservazione del backup a lungo termine:

1. D: È possibile eliminare manualmente backup specifici nell'insieme di credenziali?

    R: Non in questo momento, ma l'insieme di credenziali pulisce automaticamente i backup quando il periodo di conservazione è scaduto.
2. D: È possibile registrare un server per archiviare backup di più di un insieme di credenziali?

    R: No, oggi è possibile archiviare solo i backup di un insieme di credenziali per volta.
3. D: È possibile disporre di un insieme di credenziali e un server in sottoscrizioni diverse?

    R: No, attualmente l'insieme di credenziali e il server devono trovarsi nella stessa sottoscrizione e nello stesso gruppo di risorse.
4. D: È possibile usare un insieme di credenziali creato in un'area diversa rispetto all'area del mio server?

    R: No, l'insieme di credenziali e il server devono trovarsi nella stessa area per ridurre al minimo il tempo di copia ed evitare costi di traffico.
5. D: Quanti database è possibile archiviare in un insieme di credenziali?

    R: Attualmente è supportato un massimo di 1000 database per ogni insieme di credenziali. 
6. D: Quanti insiemi di credenziali è possibile creare per ogni sottoscrizione?

    R. È possibile creare fino a 25 insiemi di credenziali per sottoscrizione.
7. D: Quanti database è possibile configurare al giorno per ogni insieme di credenziali?

    R. È possibile configurare solo 200 database al giorno per ogni insieme di credenziali.
8. D: La conservazione dei backup a lungo termine funziona con i pool elastici?

    A: Sì. Qualsiasi database nel pool può essere configurato con i criteri di conservazione.
9. D: È possibile scegliere l'ora di creazione del backup?

    R: No, il database SQL controlla la pianificazione dei backup per ridurre al minimo l'impatto sulle prestazioni dei database.
10. D: Ho attivato la TDE per il database. È possibile utilizzarla nell'insieme di credenziali? 

    R. Sì, è possibile. Anche se il database originale non esiste più, è possibile ripristinare il database dall'insieme di credenziali.
11. D: Cosa succede ai backup nell'insieme di credenziali se la mia sottoscrizione viene sospesa? 

    R. Se la sottoscrizione viene sospesa, vengono mantenuti i database e i backup esistenti, ma i nuovi backup non vengono copiati nell'insieme di credenziali. Dopo la riattivazione della sottoscrizione, il servizio riprende la copia dei backup nell'insieme di credenziali. L'insieme di credenziali diventa inaccessibile per le operazioni di ripristino che usano i backup copiati prima della sospensione della sottoscrizione. 
12. D: È possibile accedere ai file di backup del database SQL per scaricarli o ripristinarli in SQL Server?

    R: No, non attualmente.
13. D: È possibile disporre di più pianificazioni (giornaliera, settimanale, mensile, annuale) in un criterio di conservazione SQL?

    R: No, attualmente questa funzione è disponibile solo per il backup di macchine virtuali.
14. D: Cosa accade se si imposta la conservazione dei backup a lungo termine in un database che è una replica geografica attiva secondaria?

    R: Attualmente non vengono eseguiti backup sulle repliche, e pertanto, non è possibile la conservazione dei backup a lungo termine nelle repliche secondarie. Tuttavia, è importante per un cliente impostare la conservazione dei backup a lungo termine in una replica geografica attiva secondaria per questi motivi:
    - Quando si verifica un failover e il database diventa un database primario, viene eseguito un backup completo, il quale verrà caricato per l'insieme di credenziali.
    - L'impostazione della conservazione dei backup a lungo termine in un database secondario non prevede alcun costo aggiuntivo per il cliente.



## <a name="next-steps"></a>Passaggi successivi
I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).


