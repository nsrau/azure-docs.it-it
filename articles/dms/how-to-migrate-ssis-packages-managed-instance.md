---
title: Eseguire la migrazione dei pacchetti SSIS all'istanza gestita SQLMigrate SSIS packages to SQL managed instance
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione di pacchetti e progetti di SQL Server Integration Services (SSIS) in un'istanza gestita del database SQL di Azure usando il servizio Migrazione del database di Azure o l'Assistente migrazione dati.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297189"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Eseguire la migrazione di pacchetti di SQL Server Integration Services a un'istanza gestita di database SQL di Azure
Se si usa SQL Server Integration Services (SSIS) e si desidera eseguire la migrazione dei progetti/pacchetti SSIS dall'origine SSISDB ospitato da SQL Server al SSISDB di destinazione ospitato da un'istanza gestita del database SQL di Azure, è possibile usare il servizio Migrazione del database di Azure.If you use SQL Server Integration Services (SSIS) and want to migrate your SSIS projects/packages from the source SSISDB hosted by SQL Server at the destination SSISDB hosted by an Azure SQL Database managed instance, you can use Azure Database Migration Service.

Se la versione di SSIS in uso è precedente al 2012 o si utilizzano tipi di archivio pacchetti non SSISDB, prima di eseguire la migrazione dei progetti/pacchetti SSIS, è necessario convertirli utilizzando la Conversione guidata progetti di Integration ServicesIntegration Services , che può essere avviata anche da SSMS. Per altre informazioni, vedere l'articolo [Converting projects to the project deployment model](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert) (Conversione di progetti nel modello di distribuzione del progetto).

> [!NOTE]
> Il servizio Di migrazione del database di Azure (DMS) attualmente non supporta il database SQL di Azure come destinazione della migrazione di destinazione. Per ridistribuire i progetti/pacchetti SSIS nel database SQL di Azure, vedere l'articolo Ridistribuire i pacchetti di SQL Server Integration Services nel database SQL di [Azure.](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Valutare i progetti/pacchetti SSIS di origine.
> * Eseguire la migrazione di pacchetti/progetti SSIS in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* Per creare una rete virtuale di Microsoft Azure per il servizio di migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Per altre informazioni, vedere l'articolo Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure usando il servizio Migrazione del database di Azure.For more information, see the article [Network topologies for Azure SQL Database managed instance migrations using Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere gli articoli relativi alla [documentazione](https://docs.microsoft.com/azure/virtual-network/)relativa alla rete virtuale e in particolare gli articoli delle guide introduttive con dettagli dettagliati.
* Per assicurarsi che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino le seguenti porte di comunicazione in ingresso al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000.To ensure that your virtual network Network Network Security Group rules don't block the following inbound communication ports to Azure Database Migration Service: 443, 53, 9354, 445, 12000. Per ulteriori informazioni sul filtro del traffico del gruppo di sicurezza di rete virtuale, vedere l'articolo [Filtrare](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)il traffico di rete con i gruppi di sicurezza di rete .
* Per configurare [Windows Firewall per l'accesso al motore](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)di database di origine.
* Per aprire Windows Firewall per consentire al servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.To open your Windows Firewall to allow the Azure Database Migration Service to access the source SQL Server, which by default is TCP port 1433.
* Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire al servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione e ai file attraverso la porta SMB 445.
* Un'istanza gestita del database SQL di Azure per ospitare SSISDB. Se è necessario crearne uno, seguire i dettagli nell'articolo [Creare un'istanza gestita del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)di Azure.
* Per assicurarsi che gli account di accesso utilizzati per connettere l'istanza gestita di origine e l'istanza gestita di destinazione siano membri del ruolo del server sysadmin.
* Per verificare che venga eseguito il provisioning di SSIS in Azure Data Factory (ADF) contenente Azure-SSIS Integration Runtime (IR) con il SSISDB di destinazione ospitato da un'istanza gestita del database SQL di Azure (come descritto nell'articolo Creare il runtime di [integrazione Azure-SSIS in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Consente di valutare i progetti/pacchetti SSIS di origine

Sebbene la valutazione del sSISDB di origine non sia ancora integrata nell'Assistente migrazione database (DMA), i progetti/pacchetti SSIS verranno valutati/convalidati man mano che vengono ridistribuiti nel sSISDB di destinazione ospitato in un'istanza gestita del database SQL di Azure.While assessment of source SSISDB isn't yet integrated into the Database Migration Assistant (DMA), your SSIS projects/packages will be a ssessed/validated as they're redeployed to the destination SSISDB hosted on an Azure SQL Database managed instance.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Cercare la migrazione e quindi a destra di **Microsoft.DataMigration**, selezionare **Registra**.

    ![Registrare il provider di risorse](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **Crea una risorsa**, cercare Servizio migrazione database di **Azure**e quindi selezionare Servizio migrazione database di **Azure** dall'elenco a discesa.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una.

    La rete virtuale fornisce al servizio di migrazione del database di Azure l'accesso all'istanza gestita di SQL Server e del database SQL di Azure di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo Creare una rete virtuale usando il portale di Azure.For more information on how to create a virtual network in Azure portal, see the article [Create a virtual network using the Azure portal.](https://aka.ms/DMSVnet)

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezione di un piano tariffario.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina dei prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Nella schermata **Servizio migrazione database** di Azure cercare il nome dell'istanza creata e quindi selezionare l'istanza.

3. Selezionare : **Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto** di migrazione specificare un nome per il progetto, nella casella di testo Tipo **di server** di origine selezionare SQL **Server**, nella casella di testo Tipo di server di **destinazione,** selezionare **Istanza gestita database SQL**di Azure e quindi in Scegli tipo di **attività**selezionare Migrazione **pacchetto SSIS**.

   ![Creare il progetto del Servizio Migrazione del database di Azure](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate utilizzando un certificato autofirmato non forniscono una protezione avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non fare affidamento su TLS utilizzando certificati autofirmati in un ambiente di produzione o su server connessi a Internet.You should not rely on TLS using self-signed certificates in a production environment or on servers that are connected to the internet.

   ![Dettagli origine](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione migrazione** specificare i dettagli di connessione per la destinazione.

     ![Dettagli destinazione](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selezionare **Salva**.

## <a name="review-the-migration-summary"></a>Esaminare il riepilogo della migrazione

1. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

2. Per l'opzione di sovrascrittura di **progetti e ambienti SSIS**, specificare se sovrascrivere o ignorare i progetti e gli ambienti SSIS esistenti.

    ![Riepilogo del progetto di migrazione](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Esaminare e verificare i dettagli associati al progetto di migrazione.

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.
