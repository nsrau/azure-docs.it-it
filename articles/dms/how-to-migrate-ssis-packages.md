---
title: Ridistribuzione di pacchetti di SQL Server Integration Services Database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione di pacchetti di SQL Server Integration Services a Database SQL di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080643"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>Ridistribuzione di pacchetti di SQL Server Integration Services Database SQL di Azure

Se si usa SQL Server Integration Services (SSIS) e si vuole eseguire la migrazione di progetti/pacchetti SSIS dall'origine database SSIS ospitato da SQL Server per il database SSIS ospitato dal Database SQL di Azure di destinazione, è possibile ridistribuire tramite la distribuzione di servizi di integrazione mago. È possibile avviare la procedura guidata all'interno di SQL Server Management Studio (SSMS).

Se la versione di SSIS usata è precedente al 2012, prima di ridistribuire progetti/pacchetti SSIS nel modello di distribuzione del progetto, è necessario convertirli con la procedura guidata di conversione del progetto Integration Services, che può essere avviata anche da SSMS. Per altre informazioni, vedere l'articolo [Converting projects to the project deployment model](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert) (Conversione di progetti nel modello di distribuzione del progetto).

> [!NOTE]
> Azure Database Migration Service (DMS) attualmente non supporta la migrazione di un'origine SSISDB a un server di Database SQL di Azure, ma è possibile ridistribuire progetti/pacchetti SSIS usando la procedura seguente.

In questo articolo viene spiegato come:
> [!div class="checklist"]
>
> * Valutare i progetti/pacchetti SSIS di origine.
> * Eseguire la migrazione di pacchetti/progetti SSIS in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* Versione SSMS 17.2 o successiva.
* Un'istanza del server di database di destinazione per ospitare SSISDB. Se non si ha già uno, creare un server di Database SQL di Azure (senza un database) tramite il portale di Azure, passare a SQL Server (solo server logico) [form](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS deve essere eseguito il provisioning in Azure Data Factory (ADF) che contiene di Azure-SSIS Integration Runtime (IR) con il database SSIS ospitato dall'istanza del server di Database SQL di Azure di destinazione (come descritto nell'articolo [effettuare il provisioning di integrazione SSIS di Azure Fase di esecuzione in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Consente di valutare i progetti/pacchetti SSIS di origine

Mentre della valutazione dell'origine SSISDB non è ancora integrata nel Database Migration Assistant (DMA) o Azure Database Migration Service (DMS), progetti/pacchetti SSIS verranno valutati/convalidati quando essi sono ridistribuiti nella destinazione SSISDB ospitato in un Server di Database SQL di Azure.

## <a name="migrate-ssis-projectspackages"></a>Eseguire la migrazione di pacchetti/progetti SSIS

Per eseguire la migrazione di progetti/pacchetti SSIS per il server di Database SQL di Azure, eseguire la procedura seguente.

1. Aprire SSMS e selezionare **Opzioni** per visualizzare la finestra di dialogo **Connetti a server**.

2. Nel **account di accesso** , specificare le informazioni necessarie per connettersi al server di Database SQL di Azure che ospiterà il database SSISDB di destinazione.

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

    a. Nella casella di testo Nome Server, immettere il nome completo del server Database SQL di Azure (< nome_server >. database.windows.net).

    b. Specificare le informazioni di autenticazione e selezionare **Connetti**.

    ![Pagina di destinazione di selezione della procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selezionare **esplorare** per specificare la cartella di destinazione in SSISDB e quindi selezionare **successivo**.

    > [!NOTE]
    > Il pulsante **Avanti** viene abilitato solo dopo aver selezionato **Connetti**.

10. Nella pagina **Convalida**, visualizzare eventuali errori o avvisi e, se necessario, modificare i pacchetti di conseguenza.

    ![Pagina di convalida della procedura guidata di distribuzione](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selezionare **Avanti**.

12. Nella pagina **Revisione**, esaminare le impostazioni di distribuzione.

    > [!NOTE]
    > È possibile modificare le impostazioni selezionando **Previous** oppure selezionando uno dei collegamenti passaggio nel riquadro sinistro.

13. Fare clic su **Distribuisci** per avviare il processo di distribuzione.

14. Al termine del processo di distribuzione, è possibile visualizzare la Pagina risultati, che indica l'esito positivo o negativo di ogni azione di distribuzione.
    a. Se un'azione ha esito negativo, nella colonna **Risultato**, selezionare **Non riuscita** per visualizzare una spiegazione dell'errore.
    b. Facoltativamente, selezionare **Salva report** per salvare i risultati in un file XML.

15. Selezionare **Chiudi** per uscire dalla procedura guidata di distribuzione di Integration Services.

Se la distribuzione del progetto ha esito positivo senza errori, è possibile selezionare tutti i pacchetti contenuti per eseguirli su Azure-SSIS Integration Runtime.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.
