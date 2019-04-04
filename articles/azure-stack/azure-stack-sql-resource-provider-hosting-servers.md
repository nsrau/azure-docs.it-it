---
title: SQL Server in Azure Stack di Hosting | Microsoft Docs
description: Come aggiungere istanze di SQL per il provisioning tramite il provider di risorse di Adapter SQL.
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
ms.date: 03/26/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 1b9c7f00c8ec8408547620111634470d455334c8
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499234"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Aggiungere server di hosting per il provider di risorse SQL

È possibile creare database di SQL Server che ospita i server in una macchina virtuale (VM) in [Azure Stack](azure-stack-poc.md), o in una macchina virtuale all'esterno dell'ambiente Azure Stack, a condizione che il provider di risorse SQL può connettersi all'istanza.

> [!NOTE]
> Il provider di risorse SQL deve essere creato nella sottoscrizione del provider predefinito mentre i server di hosting SQL devono essere creati in una sottoscrizione fatturabile, di utente. Non usare server del provider di risorse per ospitare i database utente.

## <a name="overview"></a>Panoramica

Prima di aggiungere un server di hosting SQL, esaminare i seguenti requisiti obbligatori e generali.

### <a name="mandatory-requirements"></a>Requisiti obbligatori

* Abilitare l'autenticazione SQL nell'istanza di SQL Server. Poiché il provider di risorse della macchina virtuale di SQL non è aggiunto a un dominio, può connettersi solo a un server di hosting con l'autenticazione SQL.
* Configurare gli indirizzi IP per le istanze di SQL pubblica durante l'installazione in Azure Stack. Il provider di risorse e utenti, ad esempio App Web di comunicano attraverso la rete di utente, pertanto è necessaria la connettività all'istanza di SQL su questa rete.

### <a name="general-requirements"></a>Requisiti generali

* Dedicare l'istanza di SQL per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile usare un'istanza SQL che è usata da qualsiasi altro consumer. Questa restrizione si applica anche ai servizi App.
* Configurare un account con i livelli di privilegi appropriati per il provider di risorse (come descritto di seguito).
* Si è responsabili della gestione di istanze di SQL e dei relativi host.  Ad esempio, il provider di risorse non applicare gli aggiornamenti, gestione dei backup o di gestire la rotazione delle credenziali.

### <a name="sql-server-virtual-machine-images"></a>Immagini di macchine virtuali di SQL Server

Immagini di macchine virtuali IaaS di SQL sono disponibili tramite la funzionalità di gestione di Marketplace. Queste immagini sono le stesse macchine virtuali di SQL disponibili in Azure.

Assicurarsi di scaricare sempre la versione più recente del **estensione SQL IaaS** prima di distribuire una VM di SQL usando un elemento del Marketplace. L'estensione IaaS e portale corrispondente miglioramenti offrono funzionalità aggiuntive, ad esempio l'applicazione automatizzata di patch e backup. Per altre informazioni su questa estensione, vedere [automatizzare le attività di gestione delle macchine virtuali di Azure con l'estensione di SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> È l'estensione SQL IaaS _obbligatorio_ per tutti i SQL nelle immagini di Windows nel marketplace; della macchina virtuale avrà esito negativo da distribuire se non si è stato scaricato l'estensione. Non viene utilizzato con le immagini di macchina virtuale SQL basati su Linux.

Sono disponibili altre opzioni per la distribuzione di macchine virtuali SQL, inclusi i modelli nel [raccolta di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Qualsiasi server di hosting installato in uno Stack di Azure a più nodi devono essere creati da una sottoscrizione utente e non alla sottoscrizione di Provider predefinito. Che devono essere creati dal portale per gli utenti o da una sessione di PowerShell con un account di accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono disporre delle licenze SQL appropriate. L'amministratore del servizio _possibile_ essere il proprietario della sottoscrizione.

### <a name="required-privileges"></a>Privilegi necessari

È possibile creare un utente amministratore con privilegi inferiori rispetto a un amministratore di sistema SQL. L'utente deve solo le autorizzazioni per le operazioni seguenti:

* Database: Creare, modificare con indipendenza (per Always On unico), eliminare, eseguire il Backup
* Gruppo di disponibilità: Modificare, creare un Join, aggiungere o rimuovere Database
* Nome account di accesso: Creare, selezionare, Alter, Drop, revocare
* Operazioni SELECT: \[master\].\[ Sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[master\].\[ Sys\].\[ dm_os_sys_memory\], SERVERPROPERTY \[master\].\[ Sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

### <a name="additional-security-information"></a>Ulteriori informazioni sulla protezione

Di seguito vengono fornite linee guida sulla sicurezza aggiuntive:

* Tutti gli archivi di Azure Stack viene crittografato con BitLocker, in modo che qualsiasi istanza di SQL in Azure Stack userà l'archivio blob crittografato.
* Il Provider di risorse SQL supporta TLS 1.2. Assicurarsi che qualsiasi Server SQL che viene gestita tramite SQL RP sia configurato per TLS 1.2 _solo_ e l'applicazione relying Party per impostazione predefinita sarà che. Tutte le versioni supportate di SQL Server supporto di TLS 1.2, vedere [supporto di TLS 1.2 per Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Utilizzare Gestione configurazione di SQL Server per impostare il **ForceEncryption** opzione per garantire che tutte le comunicazioni per SQL server vengono sempre crittografate. Visualizzare [per configurare il server per forzare connessioni crittografate](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Assicurarsi che qualsiasi applicazione client sta comunicando anche tramite una connessione crittografata.
* L'applicazione relying Party è configurata per considerare attendibili i certificati usati dalle istanze di SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Specificare capacità tramite la connessione a un computer autonomo che ospita SQL server

È possibile usare autonomo (non-disponibilità elevata) istanze di SQL Server usando qualsiasi edizione di SQL Server 2014 o SQL Server 2016. Assicurarsi di avere le credenziali per un account con privilegi sysadmin.

Per aggiungere un server di hosting autonomo che è già stato configurato, seguire questa procedura:

1. Accedere al portale di operatore di Azure Stack come amministratore del servizio.

2. Selezionare **tutti i servizi** &gt; **risorse amministrative** &gt; **istanze di SQL Server che ospita**.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sotto **istanze di SQL Server che ospita**, è possibile connettersi al provider di risorse SQL alle istanze di SQL Server che servirà come back-end del provider di risorse.

   ![Dashboard di adattatore SQL](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Fare clic su **Add** e quindi fornire i dettagli della connessione per l'istanza di SQL Server nel **aggiungere un Server di Hosting SQL** pannello.

   ![Aggiungere un Server di Hosting SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Facoltativamente, specificare un nome di istanza e specificare un numero di porta, se l'istanza non è assegnato alla porta predefinita 1433.

   > [!NOTE]
   > Fino a quando l'istanza di SQL è accessibile dall'utente e amministratore di Azure Resource Manager, può essere inserito nel controllo del provider di risorse. L'istanza di SQL __necessario__ essere allocata esclusivamente per il provider di risorse.

4. Quando si aggiungono server, è necessario assegnarli a uno SKU esistente o creare un nuovo SKU. Sotto **aggiungere un Server che ospita**, selezionare **SKU**.

   * Per usare uno SKU esistente, scegliere uno SKU disponibile e quindi selezionare **Create**.
   * Per creare uno SKU, selezionare **+ Crea nuovo SKU**. Nelle **creare SKU**, immettere le informazioni necessarie e quindi selezionare **OK**.

     ![Creare uno SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Garantire un'elevata disponibilità tramite SQL gruppi di disponibilità AlwaysOn

Configurazione delle istanze di SQL AlwaysOn richiede passaggi aggiuntivi e richiede tre macchine virtuali (o computer fisici). Questo articolo si presuppone di avere già una conoscenza approfondita dei gruppi di disponibilità Always On. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione a SQL Server AlwaysOn in gruppi di disponibilità in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Gruppi di disponibilità (SQL Server) Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Il provider di risorse SQL adapter _solo_ supporta SQL 2016 SP1 Enterprise in un secondo momento istanze o per gruppi di disponibilità AlwaysOn. Questa configurazione di adapter richiede nuove funzionalità di SQL, ad esempio il seeding automatico.

### <a name="automatic-seeding"></a>Seeding automatico

È necessario abilitare [SEED automatica](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) su ogni gruppo di disponibilità per ogni istanza di SQL Server.

Per abilitare il seeding automatico in tutte le istanze, modificare, quindi eseguire il comando SQL seguente nella replica primaria per ogni istanza secondaria:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Il gruppo di disponibilità deve essere racchiuso tra parentesi quadre.

Nei nodi secondari, eseguiti il comando SQL seguente:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configurare l'autenticazione del database indipendente

Prima di aggiungere un database indipendente a un gruppo di disponibilità, verificare che l'opzione di database indipendente l'autenticazione server è impostato su 1 per ogni istanza del server che ospita una replica di disponibilità per il gruppo di disponibilità. Per altre informazioni, vedere [Server di configurazione opzione contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Usare i comandi seguenti per impostare l'opzione di server di autenticazione di database indipendente per ogni istanza:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Per aggiungere Always On di SQL Server di hosting

1. Accedere al portale di amministrazione di Azure Stack come un amministratore del servizio.

2. Selezionare **esplorare** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **+ Aggiungi**.

   Sotto **istanze di SQL Server che ospita**, il Provider di risorse di SQL Server è possibile connettersi a istanze effettive di SQL Server che fungono da back-end del provider di risorse.

3. Compilare il modulo con i dettagli della connessione per l'istanza di SQL Server. Assicurarsi che si usa l'indirizzo FQDN del Listener AlwaysOn (e nome di istanza e numero di porta facoltativo). Fornire le informazioni per l'account che è configurato con i privilegi sysadmin.

4. Selezionare la casella di gruppo di disponibilità AlwaysOn per abilitare il supporto per le istanze di SQL gruppo di disponibilità AlwaysOn.

   ![Abilitare Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Aggiungere l'istanza SQL Always On a uno SKU.

   > [!IMPORTANT]
   > È possibile combinare i server autonomi con Always On le istanze nello stesso SKU. È stato effettuato un tentativo di combinare tipi dopo aver aggiunto i primi risultati di server di hosting in un errore.

## <a name="sku-notes"></a>Note sulla SKU
Usare un nome SKU che descrive le funzionalità dei server nello SKU, ad esempio capacità e prestazioni. Il nome viene usato come ausilio per consentire agli utenti di distribuire i relativi database per lo SKU appropriato. Ad esempio, è possibile utilizzare nomi SKU per differenziare le offerte di servizio per le caratteristiche seguenti:
  
* un'elevata capacità
* ad alte prestazioni
* disponibilità elevata

Come procedura consigliata, tutti i server di hosting in uno SKU devono avere le stesse caratteristiche di prestazioni e risorse.

SKU non è possibile assegnare a utenti o gruppi specifici.

Gli SKU possono richiedere fino a un'ora siano visibili nel portale. Gli utenti non è possibile creare un database fino a quando non viene completamente creato lo SKU.

Per modificare uno SKU, passare a **tutti i servizi** > **Adapter SQL** > **SKU**. Selezionare lo SKU da modificare, apportare le modifiche necessarie e fare clic su **salvare** per salvare le modifiche. 

Per eliminare uno SKU che non è più necessario, passare a **tutti i servizi** > **Adapter SQL** > **SKU**. Il nome SKU e scegliere **eliminare** per eliminarlo.

> [!IMPORTANT]
> Può richiedere fino a un'ora prima che nuovi SKU a essere disponibile nel portale per gli utenti.

## <a name="make-sql-databases-available-to-users"></a>Rendere disponibili agli utenti i database SQL

Creare i piani e offerte per rendere disponibili i database SQL per gli utenti. Aggiungere il **Microsoft.SqlAdapter** al piano di servizio e creare una nuova quota.

> [!IMPORTANT]
> Può richiedere fino a due ore per le quote di nuovo a essere disponibile nel portale per gli utenti o prima viene applicata una quota modificata.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i database](azure-stack-sql-resource-provider-databases.md)
