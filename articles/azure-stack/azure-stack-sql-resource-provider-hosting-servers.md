---
title: SQL Server nello Stack Azure Hosting | Documenti Microsoft
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
ms.date: 06/29/2018
ms.author: jeffgilb
ms.openlocfilehash: 74d888ffe28e5428b47bfc73122518c22d0f0918
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128708"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Aggiungere server di hosting per il provider di risorse SQL

È possibile ospitare un'istanza di SQL in una macchina virtuale (VM) nella [Azure Stack](azure-stack-poc.md), o in una macchina virtuale all'esterno dell'ambiente dello Stack di Azure, a condizione che il provider di risorse SQL può connettersi all'istanza.

## <a name="overview"></a>Panoramica

Prima di aggiungere un server di hosting SQL, esaminare i seguenti requisiti obbligatori e generale.

**Requisiti obbligatori**

* Abilitare l'autenticazione SQL nell'istanza di SQL Server. Poiché il provider di risorse SQL VM non è aggiunto a un dominio, può connettersi solo a un server di hosting con l'autenticazione SQL.
* Configurare gli indirizzi IP per le istanze SQL come pubblico. Il provider di risorse e gli utenti, ad esempio le app Web, comunicano tramite la rete dell'utente, pertanto è necessaria una connessione all'istanza di SQL su questa rete.

**Requisiti generali**

* Dedicare l'istanza SQL per l'utilizzo per la risorsa utente e provider i carichi di lavoro. Non è possibile utilizzare un'istanza di SQL che è utilizzata da qualsiasi altro consumer. Questa restrizione si applica anche ai servizi di App.
* Configurare un account con i livelli di privilegio appropriato per il provider di risorse.
* Si è responsabili della gestione di istanze di SQL e dei relativi host.  Ad esempio, il provider di risorse non applicare gli aggiornamenti, gestione dei backup o gestire le credenziali di rotazione.

### <a name="sql-server-virtual-machine-images"></a>Immagini di macchina virtuale di SQL Server

Immagini di macchina virtuale SQL IaaS sono disponibili tramite la funzionalità di gestione di Marketplace. Queste immagini sono le stesse macchine virtuali di SQL disponibili in Azure.

Assicurarsi sempre di scaricare la versione più recente del **estensione SQL IaaS** prima di distribuire una VM SQL utilizzando un elemento del Marketplace. L'estensione di IaaS e portale corrispondente i miglioramenti includono funzionalità aggiuntive, ad esempio l'applicazione automatica di patch ed eseguire il backup. Per ulteriori informazioni su questa estensione, vedere [automatizzare le attività di gestione in Azure alle macchine virtuali con l'estensione di SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Sono disponibili altre opzioni per la distribuzione di macchine virtuali di SQL, inclusi i modelli nel [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> È necessario creare tutti i server di hosting installati in uno Stack di Azure a più nodi da una sottoscrizione utente. Non possono essere creati dalla sottoscrizione di Provider predefinito. Essi devono essere creati dal portale per gli utenti o da una sessione di PowerShell con un account di accesso appropriato. Tutti i server di hosting sono fatturabili macchine virtuali e devono disporre delle licenze SQL appropriate. L'amministratore del servizio _possibile_ essere il proprietario della sottoscrizione in questione.

### <a name="required-privileges"></a>Privilegi necessari

È possibile creare un utente amministratore con privilegi minori rispetto a un sysadmin SQL. L'utente deve solo le autorizzazioni per le operazioni seguenti:

* Database: Creare, modificare, con l'opzione Containment (per Always On solo), eliminare, eseguire il Backup
* Gruppo di disponibilità: Alter, creare un Join, Aggiungi/Rimuovi Database
* Account di accesso: Creare, selezionare, Alter, Drop, revocare
* Operazioni SELECT: \[master\].\[ Sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[master\].\[ Sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ Sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Offrono capacità tramite la connessione a un computer autonomo che ospita SQL server

È possibile utilizzare autonomi (non-a disponibilità elevata) SQL Server utilizzando qualsiasi edizione di SQL Server 2014 o SQL Server 2016. Assicurarsi di avere le credenziali per un account con privilegi sysadmin.

Per aggiungere un server di hosting autonomo già configurato, seguire questi passaggi:

1. Accedere al portale di Azure Stack operatore come amministratore del servizio.

2. Selezionare **esplorare** &gt; **risorse amministrative** &gt; **server di Hosting SQL**.

   ![Server di Hosting SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sotto **server di Hosting SQL**, è possibile connettersi al provider di risorse SQL a istanze di SQL Server che servono come back-end del provider di risorse.

   ![Dashboard di Adapter SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Nel **aggiungere un Server di Hosting SQL**, specificare i dettagli di connessione per l'istanza di SQL Server.

   ![Aggiungere un Server di Hosting SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Facoltativamente, specificare un nome di istanza e un numero di porta se l'istanza non è assegnato alla porta predefinita 1433.

   > [!NOTE]
   > Fino a quando l'istanza di SQL è possibile accedere dall'utente e amministratore Gestione risorse di Azure, può essere inserita nel controllo del codice del provider di risorse. L'istanza SQL __necessario__ essere allocata in modo esclusivo al provider di risorse.

4. Quando si aggiungono server, è necessario assegnare loro una SKU esistente o crearne uno nuovo SKU. Sotto **aggiungere un Server che ospita**, selezionare **SKU**.

   * Per utilizzare una SKU esistente, scegliere una SKU disponibile e quindi selezionare **crea**.
   * Per creare un'unità SKU, selezionare **+ creare nuovo SKU**. In **creare SKU**, immettere le informazioni necessarie e quindi selezionare **OK**.

     > [!IMPORTANT]
     > Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati in **nome** campo. Usare gli esempi dell'acquisizione schermo seguente per immettere i valori per il **famiglia**, **livello**, e **edizione** campi.

     ![Creare un'unità SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKU può richiedere fino a un'ora sia visibile nel portale. Gli utenti non è possibile creare un database fino a quando non lo SKU creazione è stata completata.

### <a name="sku-notes"></a>Note sulla SKU

È possibile utilizzare gli SKU per differenziare le offerte di servizio. Ad esempio, si può avere un'istanza di SQL Enterprise che presenta le caratteristiche seguenti:
  
* un'elevata capacità
* prestazioni elevate
* disponibilità elevata

È possibile creare un'unità SKU per l'esempio precedente, limitando l'accesso a gruppi specifici che richiedono un database ad alte prestazioni.

>[!TIP]
>Utilizzare un nome SKU che riflette descrive le funzionalità dei server nella SKU, quali la capacità e prestazioni. Il nome viene utilizzato come un aiuto per consentire agli utenti di distribuire i database per lo SKU appropriato.

Come procedura consigliata, tutti i server di hosting in un'unità SKU devono avere le stesse caratteristiche di prestazioni e risorsa.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornire la disponibilità elevata utilizzando SQL gruppi di disponibilità AlwaysOn

Configurazione delle istanze di SQL AlwaysOn richiede passaggi aggiuntivi e richiede tre macchine virtuali (o computer fisici). Questo articolo si presuppone che si disponga già di una conoscenza approfondita dei gruppi di disponibilità Always On. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione a SQL Server Always On gruppi di disponibilità in macchine virtuali di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On gruppi di disponibilità (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Il provider di risorse SQL adapter _solo_ supporta SQL 2016 Enterprise Edition SP1 o versioni successive le istanze per Always On. Questa configurazione di adapter richiede nuove funzionalità di SQL, ad esempio il seeding automatico.

### <a name="automatic-seeding"></a>Seeding automatico
È necessario abilitare [il Seeding automatico](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) su ogni gruppo di disponibilità per ogni istanza di SQL Server.

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

### <a name="configure-contained-database-authentication"></a>Configurare l'autenticazione di database indipendente
Prima di aggiungere un database indipendente a un gruppo di disponibilità, verificare che l'opzione di server di database indipendente l'autenticazione è impostata su 1 in ogni istanza del server che ospita una replica di disponibilità per il gruppo di disponibilità. Per altre informazioni, vedere [opzione di configurazione Server contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Per impostare l'opzione di server di autenticazione di database indipendente per ogni istanza, utilizzare questi comandi:

  ```
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Per aggiungere SQL sempre sul server di hosting

1. Accedere al portale di amministrazione di Stack di Azure come un amministratore del servizio.

2. Selezionare **esplorare** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **+ Aggiungi**.

   Sotto **server di Hosting SQL**, il Provider di risorse di SQL Server è possibile connettersi a istanze effettive di SQL Server che fungono da back-end del provider di risorse.

3. Compilare il modulo con i dettagli della connessione per l'istanza di SQL Server. Assicurarsi di utilizzare l'indirizzo FQDN del sempre i Listener di (il numero di porta facoltativo.) Fornire le informazioni per l'account che è configurato con i privilegi sysadmin.

4. Selezionare la casella di gruppo di disponibilità AlwaysOn per abilitare il supporto per le istanze di SQL gruppo di disponibilità AlwaysOn.

   ![Abilitare Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Aggiungere l'istanza di SQL AlwaysOn a uno SKU.

   > [!IMPORTANT]
   > Con Always On le istanze nello stesso SKU non è possibile combinare i server autonomi. È stato effettuato un tentativo di combinare tipi dopo l'aggiunta del server di hosting prima comporta un errore.

## <a name="make-the-sql-databases-available-to-users"></a>Rendere disponibili agli utenti i database SQL

Creare i piani e le offerte per rendere disponibili i database SQL per gli utenti. Aggiungere il **Microsoft.SqlAdapter** per il piano di servizio e aggiungere il valore predefinito di Quota o creare una nuova Quota.

![Creare i piani e le offerte per includere i database](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i database](azure-stack-sql-resource-provider-databases.md)
