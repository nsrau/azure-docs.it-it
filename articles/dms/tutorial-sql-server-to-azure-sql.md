---
title: Usare Servizio Migrazione del database di Azure per eseguire la migrazione di SQL Server nel database SQL di Azure | Microsoft Docs
description: Informazioni su come migrare da SQL Server locale in Azure SQL con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 70127b09e64ea4f19de437297498bdf78d415b99
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Eseguire la migrazione di SQL Server nel database SQL di Azure
È possibile usare Servizio Migrazione del database di Azure per migrare i database da un'istanza di SQL Server locale al database SQL di Azure. In questa esercitazione si esegue la migrazione del database **Adventureworks2012** ripristinato in un'istanza locale di SQL Server 2016 o versione successiva verso un database SQL di Azure tramite Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Valutare il database locale usando Data Migration Assistant.
> * Eseguire la migrazione dello schema di esempio con Data Migration Assistant.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto del Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, sono necessari:

- [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione)
- Con l'installazione di SQL Server Express, il protocollo TCP/IP è disabilitato per impostazione predefinita. Abilitarlo seguendo le [istruzioni di questo articolo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Per configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Un'istanza del database SQL di Azure. È possibile creare un'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versione 3.3 o successiva.
- Servizio Migrazione del database di Azure richiede una rete virtuale creata usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Le credenziali utilizzate per connettersi all'istanza di origine di SQL Server devono avere le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Le credenziali utilizzate per connettersi all'istanza del database SQL di Azure di destinazione devono avere l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.
- È necessario aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine.

## <a name="assess-your-on-premises-database"></a>Valutare il database locale
Prima di eseguire la migrazione dei dati da un'istanza di SQL Server locale al database SQL Azure, è necessario esaminare il database SQL Server per rilevare eventuali problemi di blocco che potrebbero impedire la migrazione. Dopo aver scaricato e installato Data Migration Assistant v3.3, usare la procedura descritta nell'articolo [Eseguire una valutazione della migrazione a SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) per completare la valutazione del database locale. Di seguito è riportato un riepilogo dei passaggi necessari:
1.  In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi selezionare il tipo di progetto **Valutazione**.
2.  Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.
3.  Selezionare **Crea** per creare il progetto.
    Quando si esegue la valutazione della migrazione del database di SQL Server di origine al database SQL di Azure, è possibile scegliere uno o entrambi i tipi di report di valutazione seguenti:
    - Check database compatibility (Verificare la compatibilità del database)
    - Check feature parity (Verificare la parità di funzionalità)

    Entrambi i tipi di report sono selezionati per impostazione predefinita.
4.  In Data Migration Assistant selezionare **Avanti** nella schermata **Opzioni**.
5.  Nella schermata **Seleziona origini** della finestra di dialogo **Connetti a un server** indicare i dettagli della connessione a SQL Server e quindi selezionare **Connetti**.
6.  Selezionare **AdventureWorks2012**, **Aggiungi** e quindi selezionare **Start Assessment** (Avvia valutazione).

    Al termine della valutazione, i risultati vengono visualizzati come mostrato nella figura seguente:

    ![Valutare la migrazione dei dati](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Per database SQL di Azure, le valutazioni identificano i problemi di blocco della migrazione e i problemi di parità della funzionalità.

7.  Esaminare i risultati della valutazione per identificare problemi di blocco della migrazione e problemi di parità della funzionalità selezionando le opzioni specifiche.
    - La categoria di parità delle funzionalità di SQL Server offre un set completo di indicazioni, approcci alternativi disponibili in Azure e procedure di mitigazione che consentono di pianificare le attività nei progetti di migrazione.
    - La categoria relativa ai problemi di compatibilità offre funzionalità parzialmente supportate o non supportate che riflettono i problemi di compatibilità che potrebbero bloccare la migrazione dei database di SQL Server locali ai database SQL di Azure. Vengono anche fornite raccomandazioni che consentono di risolvere tali problemi.


## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio
Dopo aver acquisito familiarità con la valutazione e aver verificato che il database scelto sia un buon candidato per la migrazione al database SQL di Azure, usare Data Migration Assistant per eseguire la migrazione dello schema al database SQL di Azure.

> [!NOTE]
> Prima di creare un progetto di migrazione in Data Migration Assistant, assicurarsi di aver già eseguito il provisioning di un database SQL di Azure come indicato nei prerequisiti. Ai fini di questa esercitazione, si presuppone che il nome del database SQL di Azure sia **AdventureWorks2012**, ma è possibile specificare un nome diverso se lo si desidera.

Per eseguire la migrazione dello schema **AdventureWorks2012** al database SQL di Azure, eseguire le operazioni seguenti:

1.  Avviare Data Migration Assistant.
2.  Selezionare l'icona Nuovo (+), quindi in **Tipo di progetto** selezionare **Migrazione**.
3.  Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.
4.  In **Migration Scope** (Ambito della migrazione) selezionare **Schema only** (Solo schema).

    Dopo aver eseguito i passaggi precedenti, viene visualizzata l'interfaccia di Data Migration Assistant come illustrato nella figura seguente:
    
    ![Creare un progetto di Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Selezionare **Crea** per creare il progetto.
6.  In Data Migration Assistant specificare i dettagli della connessione di origine per Server SQL, selezionare **Connetti**, quindi selezionare il database **AdventureWorks2012**.

    ![Dettagli della connessione di origine Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Selezionare **Avanti** in **Connect to target server** (Connetti al server di destinazione), specificare i dettagli della connessione di destinazione per il database SQL di Azure, selezionare **Connetti**, quindi selezionare il database **AdventureWorks2012** di cui è già stato eseguito il provisioning nel database SQL di Azure.

    ![Dettagli della connessione di destinazione Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Selezionare **Avanti** per passare alla schermata **Seleziona oggetti**, in cui è possibile specificare gli oggetti dello schema nel database **AdventureWorks2012** che devono essere distribuiti in database SQL di Azure.

    Per impostazione predefinita, sono selezionati tutti gli oggetti.

    ![Generare script SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Selezionare **Genera script SQL** per creare gli script SQL e quindi esaminare gli script per individuare eventuali errori.

    ![Script dello schema](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Selezionare **Deploy schema** (Distribuisci schema) per distribuire lo schema nel database SQL di Azure e in seguito alla distribuzione dello schema, verificare che il server di destinazione non presenti anomalie.

    ![Distribuire lo schema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="create-an-instance"></a>Creare un'istanza
1.  Accedere al portale di Azure, selezionare **+ Creare una risorsa**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  Nella schermata **Servizio Migrazione del database di Azure (anteprima)** selezionare **Crea**.
 
    ![Creare l'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  Nella schermata **Servizio Migrazione del database**  specificare un nome per il servizio, la sottoscrizione, una rete virtuale e il piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la pagina relativa ai prezzi.

     ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione
Dopo aver creato il servizio, individuarlo nel portale di Azure e creare un progetto di migrazione.
1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.
 
      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. Nella schermata **Servizio Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.
 
     ![Individuare l'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Selezionare **+ New Migration Project** (+ Nuovo progetto di migrazione).
4. Nella schermata **New migration project** (Nuovo progetto di migrazione) specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.

    ![Creare il progetto del Servizio Migrazione del database](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Selezionare **Crea** per creare il progetto.


## <a name="specify-source-details"></a>Specificare le informazioni di origine
1. Nella schermata **Dettagli origine** specificare i dettagli di connessione per SQL Server di origine.

    ![Selezionare l'origine](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Selezionare **Salva**, quindi selezionare il database **AdventureWorks2012** per la migrazione.

    ![Selezionare il database di origine](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione
1. Selezionare **Salva**, quindi nella schermata **Dettagli destinazione** specificare i dettagli di connessione per la destinazione, ovvero il database SQL di Azure di cui è già stato eseguito il provisioning e in cui lo schema **AdventureWorks2012** è stato distribuito tramite Data Migration Assistant.

    ![Selezionare la destinazione](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Selezionare **Salva** per salvare il progetto.
3. Nella schermata **Migration summary** (Riepilogo migrazione) esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Riepilogo del Servizio Migrazione del database](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Selezionare **Salva**.

## <a name="run-the-migration"></a>Eseguire la migrazione
1.  Selezionare il progetto salvato di recente, selezionare **+ Nuova attività**, quindi selezionare **Run Data Migration** (Esegui la migrazione dei dati).

    ![Nuova attività](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Quando richiesto, immettere le credenziali per i server di origine e di destinazione e quindi selezionare **Salva**.
3.  Nella schermata **Map to target databases** (Esegui il mapping nel database di destinazione) eseguire il mapping del database di origine e del database di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Selezionare **Salva**, nella schermata **Seleziona tabelle** espandere l'elenco delle tabelle ed esaminare l'elenco dei campi interessati.

    ![Selezionare le tabelle](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Selezionare **Salva** nella schermata **Migration summary** (Riepilogo migrazione), nella casella di testo **Nome attività** specificare un nome per l'attività di migrazione.

    In questa schermata è anche possibile espandere la schermata **Choose validation option** (Scegli opzione di convalida) che consente di specificare la convalida del database migrato per:
    - Schema
    - Coerenza dei dati
    - Prestazioni e correttezza delle query

    ![Scegliere l'opzione di convalida](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Selezionare **Salva**, esaminare il riepilogo per assicurarsi che i dettagli di origine e di destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Selezionare **Esegui migrazione** per avviare l'attività di migrazione e quindi selezionare **Aggiorna** per esaminare lo stato corrente.

    ![Stato attività](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione
1. Selezionare l'attività di migrazione per esaminare lo stato dell'attività.
2. Verificare che il database SQL di Azure di destinazione dopo la migrazione sia completo.

    ![Completed](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
