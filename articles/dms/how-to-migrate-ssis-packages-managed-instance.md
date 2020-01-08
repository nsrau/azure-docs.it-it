---
title: Eseguire la migrazione di pacchetti SSIS in un'istanza gestita di SQL
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione di progetti e pacchetti di SQL Server Integration Services (SSIS) a un'istanza gestita di database SQL di Azure usando il servizio migrazione del database di Azure o l'Data Migration Assistant.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: c0ec3cb21c83c7b9f5ff0cec092ab210ea8894eb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437998"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Eseguire la migrazione di pacchetti di SQL Server Integration Services a un'istanza gestita di database SQL di Azure
Se si usa SQL Server Integration Services (SSIS) e si vuole eseguire la migrazione di progetti/pacchetti SSIS dal database di origine SSISDB ospitato da SQL Server al database SSISDB di destinazione ospitato da un'istanza gestita di database SQL di Azure, è possibile usare il servizio migrazione del database di Azure.

Se la versione di SSIS usata è precedente alla 2012 o si usano tipi di archivio pacchetti non SSISDB, prima di eseguire la migrazione di progetti/pacchetti SSIS, è necessario convertirli usando la conversione guidata progetto Integration Services, che può essere avviata anche da SSMS. Per altre informazioni, vedere l'articolo [Converting projects to the project deployment model](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert) (Conversione di progetti nel modello di distribuzione del progetto).

> [!NOTE]
> Il servizio migrazione del database di Azure (DMS) non supporta attualmente il database SQL di Azure come destinazione della migrazione di destinazione. Per ridistribuire i pacchetti e i progetti SSIS nel database SQL di Azure, vedere l'articolo [ridistribuire i pacchetti SQL Server Integration Services nel database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Valutare i progetti/pacchetti SSIS di origine.
> * Eseguire la migrazione di pacchetti/progetti SSIS in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* Per creare una rete virtuale di Azure (VNet) per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni, vedere le [topologie di rete per le migrazioni di istanze gestite del database SQL di Azure con il servizio migrazione del database di Azure]( https://aka.ms/dmsnetworkformi). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.
* Per assicurarsi che le regole del gruppo di sicurezza di rete VNet non blocchino le porte di comunicazione in ingresso seguenti al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Per configurare la [Windows Firewall per l'accesso al motore di database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Per aprire il Windows Firewall per consentire al servizio migrazione del database di Azure di accedere al SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
* Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire al servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione e ai file attraverso la porta SMB 445.
* Istanza gestita di database SQL di Azure per l'hosting di SSISDB. Se è necessario crearne uno, seguire i dettagli nell'articolo [creare un istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Per assicurarsi che gli account di accesso utilizzati per connettere la SQL Server di origine e l'istanza gestita di destinazione siano membri del ruolo del server sysadmin.
* Per verificare che sia stato effettuato il provisioning di SSIS in Azure Data Factory (ADF) contenente Azure-SSIS Integration Runtime (IR) con il database SSISDB di destinazione ospitato da un'istanza gestita di database SQL di Azure, come descritto nell'articolo [creare il runtime di integrazione Azure-SSIS in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

## <a name="assess-source-ssis-projectspackages"></a>Consente di valutare i progetti/pacchetti SSIS di origine

Mentre la valutazione del database di origine SSISDB non è ancora integrata nel database Migration Assistant (DMA), i progetti/pacchetti SSIS verranno valutati/convalidati durante la ridistribuzione nel database SSISDB di destinazione ospitato in un'istanza gestita di database SQL di Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **+ Crea una risorsa**, cercare **Servizio Migrazione del database di Azure** e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una.

    La rete virtuale consente a Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza gestita di database SQL di Azure di destinazione.

    Per altre informazioni su come creare una VNet nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Nella schermata **Servizio Migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionarla.

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **nuovo progetto di migrazione** specificare un nome per il progetto, nella casella di testo **tipo server di origine** Selezionare **SQL Server**, nella casella di testo tipo di **server di destinazione** selezionare **istanza gestita di database SQL di Azure**, quindi selezionare **migrazione pacchetto SSIS**per **scegliere il tipo di attività**.

   ![Creare il progetto del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni SSL crittografate con un certificato autofirmato non forniscono una sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non usare SSL con i certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione migrazione** specificare i dettagli della connessione per la destinazione.

     ![Dettagli destinazione](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selezionare **Salva**.

## <a name="review-the-migration-summary"></a>Esaminare il riepilogo della migrazione

1. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

2. Per l' **opzione di sovrascrittura**dei progetti e degli ambienti SSIS, specificare se sovrascrivere o ignorare gli ambienti e i progetti SSIS esistenti.

    ![Riepilogo del progetto di migrazione](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Esaminare e verificare i dettagli associati al progetto di migrazione.

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.
