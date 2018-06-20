---
title: SQL Server nello Stack di Azure di Hosting | Documenti Microsoft
description: Come aggiungere istanze SQL per il provisioning tramite il provider di risorse di Adapter SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265229"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Aggiungere server di hosting per il provider di risorse SQL

È possibile ospitare un'istanza di SQL in una macchina virtuale (VM) nella [Azure Stack](azure-stack-poc.md), o in una macchina virtuale all'esterno dell'ambiente dello Stack di Azure, a condizione che il provider di risorse SQL può connettersi all'istanza.

## <a name="overview"></a>Panoramica

I requisiti generali per i server di hosting SQL sono:

* L'istanza di SQL deve essere dedicato per l'utilizzo per la risorsa utente e provider i carichi di lavoro. Non è possibile utilizzare un'istanza di SQL che è utilizzata da qualsiasi altro consumer. Questa restrizione si applica anche ai servizi di App.
* Il provider di risorse SQL VM non è aggiunto a un dominio e può connettersi solo utilizzando l'autenticazione SQL.
* È necessario configurare un account con privilegi appropriati per l'utilizzo dal provider di risorse.
* Il provider di risorse e gli utenti, ad esempio le app Web, utilizzano la rete dell'utente, pertanto è necessaria una connessione all'istanza di SQL su questa rete. Questo requisito significa in genere che l'indirizzo IP per le istanze SQL deve essere in una rete pubblica.
* Gestione di istanze di SQL e dei relativi host è responsabilità dell'utente. Ad esempio, il provider di risorse non applicare gli aggiornamenti, gestione dei backup o gestire le credenziali di rotazione.
* È possibile utilizzare gli SKU che supportano diverse classi diverse abilità: SQL, ad esempio prestazioni e disponibilità elevata con AlwaysOn.

### <a name="sql-server-virtual-machine-images"></a>Immagini di macchina virtuale di SQL Server

Immagini di macchina virtuale SQL IaaS sono disponibili tramite la funzionalità di gestione di Marketplace. Queste immagini sono le stesse macchine virtuali di SQL disponibili in Azure.

Assicurarsi sempre di scaricare la versione più recente del **estensione SQL IaaS** prima di distribuire una macchina virtuale usando un elemento del Marketplace.  L'estensione di IaaS e portale corrispondente i miglioramenti includono funzionalità aggiuntive, ad esempio l'applicazione automatica di patch ed eseguire il backup.

Sono disponibili altre opzioni per la distribuzione di macchine virtuali di SQL, inclusi i modelli nel [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> È necessario creare tutti i server di hosting, installati in uno Stack di Azure a più nodi da una sottoscrizione utente. Non possono essere creati dalla sottoscrizione di Provider predefinito. Essi devono essere creati dal portale per gli utenti o da una sessione di PowerShell con un account di accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono disporre delle licenze SQL appropriate. L'amministratore del servizio _possibile_ essere il proprietario della sottoscrizione.

### <a name="required-privileges"></a>Privilegi necessari

È possibile creare un utente amministratore con privilegi minori che un sysadmin SQL. L'utente deve solo le autorizzazioni per le operazioni seguenti:

- Database: Creare, modificare, con l'opzione Containment (per Always On solo), eliminare, eseguire il Backup
- Gruppo di disponibilità: Alter, creare un Join, Aggiungi/Rimuovi Database
- Account di accesso: Creare, selezionare, modificare, eliminare, revocare
- Le operazioni di selezione: \[master\].\[ Sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[master\].\[ Sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ Sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Offrono capacità connettendosi a un computer autonomo che ospita SQL server

È possibile utilizzare autonomi (non-a disponibilità elevata) SQL Server utilizzando qualsiasi edizione di SQL Server 2014 o SQL Server 2016. Assicurarsi di avere le credenziali per un account con privilegi sysadmin.

Per aggiungere un server di hosting autonomo già configurato, seguire questi passaggi:

1. Accedere al portale di Azure Stack operatore come amministratore del servizio.

2. Selezionare **esplorare** &gt; **risorse amministrative** &gt; **server di Hosting SQL**.

   ![Server di Hosting SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sotto **server di Hosting SQL**, è possibile connettersi al provider di risorse SQL a istanze di SQL Server che servono come back-end del provider di risorse.

   ![Dashboard di Adapter SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Riempire il form con i dettagli della connessione dell'istanza di SQL Server.

   ![Aggiungere un Server di Hosting SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    È facoltativamente possibile includere un nome di istanza e specificare un numero di porta se l'istanza non è assegnato alla porta predefinita 1433.

   > [!NOTE]
   > Fino a quando l'istanza SQL è possibile accedere dall'utente e amministratore Gestione risorse di Azure, può essere inserita nel controllo del codice del provider di risorse. L'istanza SQL __necessario__ essere allocata in modo esclusivo al provider di risorse.

4. Quando si aggiungono server, è necessario assegnare a uno SKU di nuovo o esistente per differenziare le offerte di servizio. Ad esempio, si può avere un'istanza di SQL Enterprise Edition che offre:
  
   - capacità di database
   - backup automatico
   - server ad alte prestazioni per i singoli reparti di riserva

   Tutti i server di hosting in un'unità SKU devono avere le stesse funzionalità. Il **nome** deve riflettere le proprietà della SKU in modo che gli utenti possono distribuire i database per lo SKU appropriato.

   > [!IMPORTANT]
   > Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server.

   Ad esempio: 

   ![Creare SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKU può richiedere a un'ora per essere visibile nel portale. Gli utenti non è possibile creare un database fino a quando non lo SKU creazione è stata completata.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornire la disponibilità elevata utilizzando SQL gruppi di disponibilità AlwaysOn

Configurazione delle istanze di SQL AlwaysOn richiede passaggi aggiuntivi e richiede un minimo di tre macchine virtuali (o computer fisici). Questo articolo si presuppone che si disponga già di una conoscenza approfondita dei gruppi di disponibilità Always On. Per altre informazioni, vedere:

* [Introduzione a SQL Server Always On gruppi di disponibilità in macchine virtuali di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Sempre in gruppi di disponibilità (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Il provider di risorse SQL adapter _solo_ supporta SQL 2016 Enterprise Edition SP1 o versioni successive le istanze per Always On. Questa configurazione di adapter richiede nuove funzionalità di SQL, ad esempio il seeding automatico.

Oltre all'elenco di requisiti precedente è necessario abilitare [il Seeding automatico](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) su ogni gruppo di disponibilità per ogni istanza di SQL Server.

Per abilitare il seeding automatico in tutte le istanze, modificare, quindi eseguire il comando SQL seguente per ogni istanza:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Nelle istanze del secondarie, modificare, quindi eseguire il comando SQL seguente per ogni istanza:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Per aggiungere SQL sempre sul server di hosting

1. Accedere al portale di amministrazione di Stack di Azure come un amministratore del servizio.

2. Selezionare **esplorare** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **+ Aggiungi**.

   Sotto **server di Hosting SQL** il Provider di risorse di SQL Server è possibile connettersi a istanze effettive di SQL Server che fungono da back-end del provider di risorse.

3. Compilare il modulo con i dettagli della connessione per l'istanza di SQL Server. Assicurarsi di utilizzare l'indirizzo FQDN del sempre i Listener di (il numero di porta facoltativo.) Fornire le informazioni per l'account che è configurato con i privilegi sysadmin.

4. Selezionare la casella di gruppo di disponibilità AlwaysOn per abilitare il supporto per le istanze di SQL gruppo di disponibilità AlwaysOn.

   ![Abilitare Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Aggiungere l'istanza SQL Always On a uno SKU.

   > [!IMPORTANT]
   > Con Always On le istanze nello stesso SKU non è possibile combinare i server autonomi. È stato effettuato un tentativo di combinare tipi dopo l'aggiunta del server di hosting prima comporta un errore.

## <a name="make-the-sql-databases-available-to-users"></a>Rendere disponibili agli utenti i database SQL

Creare i piani e le offerte per rendere disponibili i database SQL per gli utenti. Aggiungere il **Microsoft.SqlAdapter** per il piano di servizio e aggiungere il valore predefinito di Quota o creare una nuova Quota.

![Creare i piani e le offerte per includere i database](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i database](azure-stack-sql-resource-provider-databases.md)
