---
title: Ridistribuire pacchetti SSIS in un database SQL singolo
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione o la ridistribuzione di SQL Server Integration Services pacchetti e progetti nel database SQL di Azure single database usando il servizio migrazione del database di Azure e Data Migration Assistant.
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
ms.openlocfilehash: 483f60138dcaa6252999b9d15e846fbd1c68e9a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021518"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Ridistribuire pacchetti SSIS nel database SQL di Azure con il servizio migrazione del database di Azure

Se si usa SQL Server Integration Services (SSIS) e si vuole eseguire la migrazione di progetti/pacchetti SSIS dal database di origine SSISDB ospitato da SQL Server al database SSISDB di destinazione ospitato dal database SQL di Azure, è possibile ridistribuirli usando la distribuzione guidata Integration Services. È possibile avviare la procedura guidata all'interno di SQL Server Management Studio (SSMS).

Se la versione di SSIS usata è precedente al 2012, prima di ridistribuire progetti/pacchetti SSIS nel modello di distribuzione del progetto, è necessario convertirli con la procedura guidata di conversione del progetto Integration Services, che può essere avviata anche da SSMS. Per altre informazioni, vedere l'articolo [Converting projects to the project deployment model](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert) (Conversione di progetti nel modello di distribuzione del progetto).

> [!NOTE]
> Il servizio migrazione del database di Azure (DMS) attualmente non supporta la migrazione di un'origine SSISDB al database SQL di Azure, ma è possibile ridistribuire i progetti/pacchetti SSIS usando il processo seguente.

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Valutare i progetti/pacchetti SSIS di origine.
> * Eseguire la migrazione di pacchetti/progetti SSIS in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* Versione SSMS 17.2 o successiva.
* Un'istanza del server di database di destinazione per ospitare SSISDB. Se non ne è già presente uno, creare un [server SQL logico](../azure-sql/database/logical-servers.md) (senza un database) usando il portale di Azure passando al [modulo](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (solo server logico).
* È necessario eseguire il provisioning di SSIS in Azure Data Factory (ADF) contenente Azure-SSIS Integration Runtime (IR) con il database SSISDB di destinazione ospitato dal database SQL, come descritto nell'articolo effettuare il provisioning [della Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

## <a name="assess-source-ssis-projectspackages"></a>Consente di valutare i progetti/pacchetti SSIS di origine

Mentre la valutazione del database di origine SSISDB non è ancora integrata nel database Migration Assistant (DMA) o nel servizio migrazione del database di Azure (DMS), i progetti e i pacchetti SSIS verranno valutati/convalidati quando vengono ridistribuiti nel database SSISDB di destinazione ospitato dal database SQL di Azure.

## <a name="migrate-ssis-projectspackages"></a>Eseguire la migrazione di pacchetti/progetti SSIS

Per eseguire la migrazione di progetti/pacchetti SSIS nel database SQL di Azure, seguire questa procedura.

1. Aprire SSMS e selezionare **Opzioni** per visualizzare la finestra di dialogo **Connetti a server**.

2. Nella scheda **account di accesso** specificare le informazioni necessarie per connettersi al server che ospiterà il database SSISDB di destinazione.

    ![Scheda Accesso SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Nella scheda **Proprietà connessione**, nella casella di testo **Connetti al database**, selezionare o immettere **SSISDB** e selezionare **Connetti**.

    ![Scheda Proprietà connessione SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. In Esplora oggetti di SQL Server Management Studio, espandere il nodo **Cataloghi di Integration Services**, espandere **SSISDB** e se non esistono già cartelle, fare clic con il tasto destro del mouse su **SSISDB** e creare una nuova cartella.

5. In **SSISDB**, espandere una cartella specifica, fare clic con il tasto destro del mouse su **Progetti** e selezionare **Distribuisci progetto**.

    ![Nodo SSISDB SSIS espanso](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Nella procedura guidata di distribuzione di Integration Services, nella pagina **Introduzione**, esaminare le informazioni e selezionare **Avanti**.

    ![Pagina di introduzione alla procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Nella pagina **Seleziona origine**, specificare il progetto SSIS esistente che si desidera distribuire.

    Se SSMS è connesso anche a SQL Server che ospita il database di origine SSISDB, selezionare **catalogo di Integration Services**, quindi immettere il nome del server e il percorso del progetto nel catalogo per distribuire direttamente il progetto.

    In alternativa, selezionare **File di distribuzione progetto** e specificare il percorso di un file di distribuzione del progetto esistente (.ispac) per distribuire il progetto.

    ![Pagina di origine di selezione della procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selezionare **Avanti**.
9. Nella pagina **Seleziona destinazione**, specificare la destinazione per il progetto.

    a. Nella casella di testo nome server immettere il nome completo del server (<server_name>. database.windows.net).

    b. Specificare le informazioni di autenticazione e selezionare **Connetti**.

    ![Pagina di destinazione di selezione della procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selezionare **Sfoglia** per specificare la cartella di destinazione in SSISDB e quindi fare clic su **Avanti**.

    > [!NOTE]
    > Il pulsante **Avanti** viene abilitato solo dopo aver selezionato **Connetti**.

10. Nella pagina **Convalida**, visualizzare eventuali errori o avvisi e, se necessario, modificare i pacchetti di conseguenza.

    ![Pagina di convalida della procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selezionare **Avanti**.

12. Nella pagina **Revisione**, esaminare le impostazioni di distribuzione.

    > [!NOTE]
    > È possibile modificare le impostazioni selezionando **indietro** o facendo clic su uno dei collegamenti passaggio nel riquadro sinistro.

13. Fare clic su **Distribuisci** per avviare il processo di distribuzione.

14. Al termine del processo di distribuzione, è possibile visualizzare la Pagina risultati, che indica l'esito positivo o negativo di ogni azione di distribuzione.
    a. Se un'azione ha esito negativo, nella colonna **Risultato**, selezionare **Non riuscita** per visualizzare una spiegazione dell'errore.
    b. Facoltativamente, selezionare **Salva report** per salvare i risultati in un file XML.

15. Selezionare **Chiudi** per uscire dalla procedura guidata di distribuzione di Integration Services.

Se la distribuzione del progetto ha esito positivo senza errori, è possibile selezionare tutti i pacchetti contenuti per eseguirli su Azure-SSIS Integration Runtime.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.
