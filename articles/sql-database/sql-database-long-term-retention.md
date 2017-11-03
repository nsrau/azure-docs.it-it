---
title: Archiviare i backup del database SQL di Azure per un massimo di 10 anni | Microsoft Docs
description: Informazioni sul supporto del database SQL di Azure dell'archiviazione dei backup per un massimo di 10 anni.
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
ms.workload: On Demand
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: e44c92c3f37b3f1e3397d1c8cdb8c8f6d0f9942e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Archiviare i backup del database SQL di Azure per un massimo di 10 anni
Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. La funzionalità di conservazione dei backup a lungo termine consente di archiviare i backup del database SQL in un insieme di credenziali dei servizi di ripristino di Azure fino a 10 anni. È possibile archiviare fino a 1.000 database per ogni insieme di credenziali. È possibile quindi selezionare qualsiasi backup nell'insieme di credenziali per ripristinarlo come nuovo database.

> [!IMPORTANT]
> La conservazione dei backup a lungo termine è attualmente in anteprima ed è disponibile nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, India centrale, India meridionale, Giappone orientale, Giappone occidentale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Europa occidentale e Stati Uniti occidentali.
>

> [!NOTE]
> È possibile abilitare fino a 200 database per ogni insieme di credenziali in un periodo di 24 ore. È consigliabile usare insiemi di credenziali separati per ogni server al fine di ridurre al minimo l'impatto di questo limite. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Funzionamento della conservazione a lungo termine dei backup del database SQL

La conservazione a lungo termine dei backup consente di associare un server del database SQL a un insieme di credenziali di Servizi di ripristino di Azure. 

* L'insieme di credenziali deve essere creato nella stessa sottoscrizione di Azure in cui è stato creato il server SQL, nonché nella stessa area geografica e gruppo di risorse. 
* È quindi possibile configurare un criterio di conservazione per qualsiasi database. Il criterio consente di copiare i backup settimanali del database completo nell'insieme di credenziali dei servizi di ripristino e di conservare i backup per il periodo di memorizzazione specificato, ovvero fino a 10 anni. 
* Sarà quindi possibile ripristinare il database da una copia di questi backup in un nuovo database in qualsiasi server nella sottoscrizione. Partendo da backup esistenti Archiviazione di Azure crea una copia che non ha alcun impatto sulle prestazioni del database esistente.

> [!TIP]
> Per informazioni di guida, vedere [Configurare e ripristinare dalla conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Abilitare la conservazione del backup a lungo termine

Per configurare la conservazione a lungo termine dei backup per un database:

1. Creare un insieme di credenziali dei servizi di ripristino di Azure nella stessa area, sottoscrizione e gruppo di risorse in cui si trova il server del database SQL. 
2. Registrare il server nell'insieme di credenziali.
3. Creare un criterio di protezione per i servizi di ripristino di Azure.
4. Applicare il criterio di protezione ai database che richiedono la conservazione dei backup a lungo termine.

Per configurare, gestire e ripristinare un database dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure, eseguire una delle operazioni seguenti:

* Tramite il portale di Azure: fare clic su **Conservazione backup a lungo termine**, selezionare un database e quindi fare clic su **Configura**. 

   ![Selezionare un database per la conservazione backup a lungo termine](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Tramite PowerShell: passare a [Configurare e ripristinare dalla conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Ripristinare un database archiviato con la funzionalità di conservazione dei backup a lungo termine

Per ripristinare un database da un backup di conservazione a lungo termine:

1. Elencare l'insieme di credenziali in cui è archiviato il backup.
2. Elencare il contenitore in cui viene eseguito il mapping al server logico.
3. Elencare l'origine dei dati all'interno dell'insieme di credenziali in cui viene eseguito il mapping al database.
4. Elencare i punti di ripristino disponibili per il ripristino.
5. Eseguire il ripristino dal punto di ripristino verso il server di destinazione all'interno della sottoscrizione.

Per configurare, gestire e ripristinare un database dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure, eseguire una delle operazioni seguenti:

* Tramite il portale di Azure: passare a [Gestire la conservazione del backup a lungo termine usando il portale di Azure](sql-database-long-term-backup-retention-configure.md). 

* Tramite PowerShell: passare a [Gestire la conservazione del backup a lungo termine usando PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Trovare i prezzi per la conservazione del backup a lungo termine

Il costo della conservazione dei backup a lungo termine di un database SQL dipende dalle [tariffe dei servizi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

Dopo che il server del database SQL viene registrato nell'insieme di credenziali, viene addebitata l'archiviazione totale usata per i backup settimanali archiviati nell'insieme di credenziali.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Visualizzare i backup disponibili archiviati nella conservazione dei backup a lungo termine

Per configurare, gestire e ripristinare un database dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, eseguire una delle operazioni seguenti:

* Tramite il portale di Azure: passare a [Gestire la conservazione del backup a lungo termine usando il portale di Azure](sql-database-long-term-backup-retention-configure.md). 

* Tramite PowerShell: passare a [Gestire la conservazione del backup a lungo termine usando PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Disabilitare la conservazione a lungo termine

Il servizio di ripristino gestisce in automatico l'eliminazione dei backup in base ai criteri di conservazione specificati. 

Per interrompere l'invio di backup di un database specifico all'insieme di credenziali, rimuovere i criteri di conservazione relativi al database specifico.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> I backup che sono già nell'insieme di credenziali non sono interessati. Questi vengono automaticamente eliminati dal servizio di ripristino alla scadenza del periodo di conservazione.

## <a name="long-term-backup-retention-faq"></a>Domande frequenti sulla conservazione del backup a lungo termine

**È possibile eliminare manualmente backup specifici nell'insieme di credenziali?**

No, per il momento. L'insieme di credenziali pulisce automaticamente i backup quando il periodo di conservazione è scaduto.

**È possibile registrare un server per archiviare backup di più di un insieme di credenziali?**

No, attualmente è possibile archiviare solo i backup di un insieme di credenziali per volta.

**È possibile disporre di un insieme di credenziali e un server in sottoscrizioni diverse?**

No, attualmente l'insieme di credenziali e il server devono trovarsi nella stessa sottoscrizione e nello stesso gruppo di risorse.

**È possibile usare un insieme di credenziali creato in un'area diversa rispetto all'area del mio server?**

No, l'insieme di credenziali e il server devono trovarsi nella stessa area per ridurre al minimo il tempo di copia ed evitare costi di traffico.

**Quanti database è possibile archiviare in un insieme di credenziali?**

Attualmente è supportato un massimo di 1.000 database per ogni insieme di credenziali. 

**Quanti insiemi di credenziali è possibile creare per ogni sottoscrizione?**

È possibile creare fino a 25 insiemi di credenziali per sottoscrizione.

**Quanti database è possibile configurare al giorno per ogni insieme di credenziali?**

È possibile configurare 200 database al giorno per ogni insieme di credenziali.

**La conservazione dei backup a lungo termine funziona con i pool elastici?**

Sì. Qualsiasi database nel pool può essere configurato con i criteri di conservazione.

**È possibile scegliere l'ora di creazione del backup?**

No, il database SQL controlla la pianificazione dei backup per ridurre al minimo l'impatto sulle prestazioni dei database.

**Per il database è attiva Transparent Data Encryption . È possibile usarla nell'insieme di credenziali?** 

Sì, Transparent Data Encryption è supportata. Anche se il database originale non esiste più, è possibile ripristinare il database dall'insieme di credenziali.

**Cosa succede ai backup nell'insieme di credenziali se la mia sottoscrizione viene sospesa?** 

Se la sottoscrizione viene sospesa, vengono mantenuti i database e i backup esistenti. I nuovi backup non vengono copiati nell'insieme di credenziali. Dopo la riattivazione della sottoscrizione, il servizio riprende la copia dei backup nell'insieme di credenziali. L'insieme di credenziali diventa inaccessibile per le operazioni di ripristino che usano i backup copiati prima della sospensione della sottoscrizione. 

**È possibile accedere ai file di backup del database SQL per scaricarli o ripristinarli nel server di SQL?**

No, non attualmente.

**È possibile disporre di più pianificazioni, ad esempio giornaliere, settimanali, mensili, annuali, in un criterio di conservazione SQL?**

No, le pianificazioni multiple sono attualmente disponibili solo per i backup della macchina virtuale.

**Cosa accade se si configura la conservazione dei backup a lungo termine in un database che è una replica geografica attiva secondaria?**

Attualmente non vengono eseguiti backup sulle repliche e pertanto non è possibile la conservazione dei backup a lungo termine nei database secondari. Tuttavia, è importante per un utente configurare la conservazione dei backup a lungo termine in un database secondario della replica geografica attivo per questi motivi:
* Quando si verifica un failover e il database diventa un database primario, viene eseguito un backup completo, che verrà caricato nell'insieme di credenziali.
* La configurazione della conservazione dei backup a lungo termine in un database secondario non prevede alcun costo aggiuntivo per il cliente.

## <a name="next-steps"></a>Passaggi successivi
Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
