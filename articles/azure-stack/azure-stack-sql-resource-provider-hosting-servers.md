---
title: SQL Server nello Stack di Azure di Hosting | Documenti Microsoft
description: Come aggiungere istanze SQL per il provisioning tramite il Provider di risorse di Adapter SQL
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 0a29ef133a045b2828777050f2d7a204c0add4a8
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>Aggiungere i server di hosting per l'utilizzo dall'adapter SQL

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare le istanze di SQL in macchine virtuali all'interno del [Azure Stack](azure-stack-poc.md), o un'istanza all'esterno dell'ambiente dello Stack di Azure, fornito al provider di risorse può connettersi ad essa. I requisiti generali sono:

* L'istanza SQL deve essere dedicata per l'utilizzo dei carichi di lavoro RP e utente. È possibile utilizzare un'istanza di SQL utilizzato da qualsiasi altro consumer, inclusi i servizi di App.
* L'adapter di relying Party non è aggiunto a un dominio e può connettersi solo utilizzando l'autenticazione SQL.
* È necessario configurare un account con privilegi appropriati per l'utilizzo dal componente.
* Gli utenti, ad esempio le app Web RP utilizzano la rete dell'utente, pertanto è necessaria una connessione all'istanza di SQL su questa rete. Questo requisito significa in genere che l'indirizzo IP per le istanze SQL deve essere in una rete pubblica.
* Gestione di istanze di SQL e dei relativi host è responsabilità dell'utente; il componente non eseguire l'applicazione di patch, backup, credenziali rotazione e così via.
* SKU consente di creare classi diverse funzionalità di SQL, ad esempio le prestazioni, sempre in, e così via.


Un numero di immagini di macchina virtuale SQL IaaS è disponibile tramite la funzionalità di gestione di Marketplace. Assicurarsi sempre di scaricare la versione più recente dell'estensione SQL IaaS prima di distribuire una macchina virtuale usando un elemento del Marketplace. Le immagini SQL sono gli stessi come macchine virtuali di SQL disponibili in Azure. Per le macchine virtuali SQL creata da queste immagini, l'estensione di IaaS e corrispondente miglioramenti del portale di fornire funzionalità quali l'applicazione automatica di patch e funzionalità di backup.

Sono disponibili altre opzioni per la distribuzione di macchine virtuali di SQL, inclusi i modelli nel [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> È necessario creare tutti i server di hosting, installati in uno Stack di Azure a più nodi da una sottoscrizione utente. Non possono essere creati dalla sottoscrizione di Provider predefinito. Essi devono essere creati dal portale per gli utenti o da una sessione di PowerShell con un account di accesso appropriato. Tutti i server di hosting sono addebitabile macchine virtuali e devono disporre delle licenze SQL appropriate. L'amministratore del servizio _possibile_ essere il proprietario della sottoscrizione.


### <a name="required-privileges"></a>Privilegi necessari

È possibile creare un nuovo utente amministratore con privilegi sysadmin inferiore a quella completa. Le operazioni specifiche che devono essere consentiti sono:

- Database: Create, Alter, con l'opzione Containment (Always On solo), eliminazione, Backup
- Gruppo di disponibilità: Alter, creare un Join, Aggiungi/Rimuovi Database
- Account di accesso: Creare, selezionare, modificare, eliminare, revocare
- Le operazioni di selezione: \[master\].\[ Sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[master\].\[ Sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ Sys\].\[ availability_groups\] (AlwaysOn), sys. master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Offrono capacità connettendosi a un computer autonomo che ospita SQL server
È possibile utilizzare autonomi (non-a disponibilità elevata) SQL Server utilizzando qualsiasi edizione di SQL Server 2014 o SQL Server 2016. Assicurarsi di avere le credenziali per un account con privilegi di amministratore di sistema.

Per aggiungere un computer autonomo che contiene il server che è già stato effettuato il provisioning, seguire questi passaggi:

1. Accedere al portale di amministrazione di Azure Stack come un amministratore del servizio

2. Fare clic su **Sfoglia** &gt; **risorse amministrative** &gt; **server di Hosting SQL**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  Il **server di Hosting SQL** pannello è dove è possibile connettersi il Provider di risorse di SQL Server per le istanze effettive di SQL Server che fungono da back-end del provider di risorse.

  ![Server di hosting](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Riempire il form con i dettagli della connessione dell'istanza di SQL Server.

  ![Nuovo Server di Hosting](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    È possibile includere un nome di istanza e un numero di porta può essere fornito se l'istanza non viene assegnato alla porta predefinita 1433.

  > [!NOTE]
  > Fino a quando l'istanza SQL è possibile accedere dall'utente e amministratore Gestione risorse di Azure, può essere inserita nel controllo del codice del provider di risorse. L'istanza SQL __deve__ essere allocate esclusivamente al relying Party.

4. Quando si aggiungono server, è necessario assegnare a uno SKU di nuovo o esistente per differenziare le offerte di servizio. Ad esempio, è possibile avere un'istanza di SQL Enterprise fornisce:
  - capacità di database
  - backup automatico
  - server ad alte prestazioni per i singoli reparti di riserva
  - E così via.

  Il nome SKU deve riflettere le proprietà in modo che gli utenti possono effettuare i database in modo appropriato. Tutti i server di hosting in un'unità SKU devono avere le stesse funzionalità.

    Esempio:

![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU può richiedere a un'ora per essere visibile nel portale. Gli utenti non è possibile creare un database fino a quando non lo SKU creazione è stata completata.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Offrono capacità di utilizzo SQL gruppi di disponibilità AlwaysOn
Configurazione delle istanze di SQL Always On richiede passaggi aggiuntivi e include almeno tre macchine virtuali (o computer fisici).

> [!NOTE]
> L'adapter SQL RP _solo_ supporta SQL 2016 SP1 Enterprise Edition o istanze successive per Always On, poiché richiede la nuova funzionalità di SQL, ad esempio il seeding automatico. Oltre all'elenco precedente comune di requisiti:

* È necessario fornire un file server oltre i computer SQL Always On. È presente un [modello di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) che può creare questo ambiente. Inoltre può fungere da una Guida per la creazione di un'istanza personalizzata.

* È necessario configurare SQL Server. In particolare, è necessario abilitare [il Seeding automatico](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) in ogni gruppo di disponibilità per ogni istanza di SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

Nelle istanze secondarie
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Per aggiungere i server di hosting SQL Always On, seguire questi passaggi:

1. Accedere al portale di amministrazione di Azure Stack come un amministratore del servizio

2. Fare clic su **Sfoglia** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **+ Aggiungi**.

    Il **server di Hosting SQL** pannello è dove è possibile connettersi il Provider di risorse di SQL Server per le istanze effettive di SQL Server che fungono da back-end del provider di risorse.


3. Riempire il form con i dettagli della connessione dell'istanza di SQL Server, assicurarsi di usare l'indirizzo FQDN o IPv4 del sempre sul Listener (e il numero di porta facoltativa). Fornire le informazioni sull'account per l'account che è configurato con privilegi di amministratore di sistema.

4. Selezionare questa casella per abilitare il supporto per le istanze di SQL gruppo di disponibilità AlwaysOn.

    ![Server di hosting](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Aggiungere l'istanza SQL Always On a uno SKU. Con Always On le istanze nella stessa SKU non è possibile combinare i server autonomi. Che verrà determinato quando si aggiunge il primo server di hosting. Si tenta di combinare diversi tipi in un secondo momento verrà generato un errore.


## <a name="making-sql-databases-available-to-users"></a>Rendere disponibili agli utenti i database SQL

Creare i piani e le offerte per rendere disponibili i database SQL per gli utenti. Aggiungere il servizio Microsoft.SqlAdapter al piano e aggiungere una Quota di un esistente o crearne uno nuovo. Se si crea una quota, specificare la capacità per consentire all'utente.

![Creare i piani e le offerte per includere i database](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>Manutenzione dell'Adapter SQL RP

Manutenzione delle istanze di SQL non è trattata in questo caso, ad eccezione delle informazioni relative alle password rotazione. È responsabile per l'applicazione di patch e backup/ripristino dei server di database utilizzata con l'Adapter SQL.

### <a name="patching-and-updating"></a>L'applicazione di patch e aggiornamento
 L'Adapter SQL non viene gestita come parte dello Stack di Azure perché è un componente aggiuntivo. Microsoft fornirà gli aggiornamenti all'adapter SQL in base alle esigenze. L'Adapter SQL viene creata un'istanza in un _utente_ macchina virtuale con la sottoscrizione del Provider predefinito. Pertanto, è necessario fornire le patch di Windows, le firme antivirus, e così via. Aggiornamenti di Windows di pacchetti forniti come parte del ciclo di patch e aggiornamento può essere utilizzati per applicare gli aggiornamenti per la macchina virtuale Windows. Quando viene rilasciato un adapter aggiornato, viene fornito uno script per applicare l'aggiornamento. Questo script crea una nuova VM RP e qualsiasi stato che si dispone già di eseguire la migrazione.

 ### <a name="backuprestoredisaster-recovery"></a>Backup/Restore/ripristino di emergenza
 L'Adapter SQL non è backup come parte del processo di Azure Stack BC ripristino di emergenza, perché è un componente aggiuntivo. Verranno forniti per semplificare gli script:
- Backup delle informazioni necessarie per lo stato (archiviate in un account di archiviazione di Azure Stack)
- Ripristino al relying Party non è più necessario un ripristino completo dello stack.
Server di database devono essere recuperati prima (se necessario), prima che venga ripristinato al relying Party.

### <a name="updating-sql-credentials"></a>Aggiornamento delle credenziali SQL

È responsabile per la creazione e gestione di account di amministratore di sistema nel computer SQL Server. Il componente necessita di un account con i privilegi per gestire i database per conto degli utenti: non è necessario accedere ai dati in tali database. Se è necessario aggiornare le password sa di SQL Server, è possibile utilizzare la funzionalità di aggiornamento dell'interfaccia di amministrazione della relying Party per modificare la password archiviata utilizzata dal componente. Queste password sono archiviate in un insieme di credenziali chiave sull'istanza dello Stack di Azure.

Per modificare le impostazioni, fare clic su **Sfoglia** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **Account di accesso SQL** e selezionare un nome di account di accesso. La modifica deve essere apportata nell'istanza di SQL prima (e tutte le repliche, se necessario). Nel **impostazioni** pannello, fare clic su **Password**.

![Aggiornare la password dell'amministratore](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i database](azure-stack-sql-resource-provider-databases.md)
