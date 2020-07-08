---
title: Gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti
description: Informazioni su come gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198868"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Per sollevare & spostare i carichi di lavoro della SQL Server Integration Services locale (SSIS) nel cloud, è possibile effettuare il provisioning di Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). Per altre informazioni, vedere effettuare il [provisioning di un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto)
- Esecuzione di pacchetti distribuiti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di database SQL di Azure (modello di distribuzione del pacchetto)

Quando si usa il modello di distribuzione del pacchetto, è possibile scegliere se eseguire il provisioning della Azure-SSIS IR con gli archivi pacchetti che forniscono un livello di gestione dei pacchetti in file system/File di Azure/MSDB ospitato da Istanza gestita di Azure SQL. Azure-SSIS IR archivio pacchetti consente di importare/esportare, eliminare/eseguire pacchetti e monitorare/arrestare l'esecuzione di pacchetti tramite SQL Server Management Studio (SSMS) simile all' [Archivio pacchetti SSIS legacy](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Connetti a Azure-SSIS IR

Una volta eseguito il provisioning del Azure-SSIS IR, è possibile connettersi a esso per esplorare gli archivi pacchetti in SSMS.

![Connetti a Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Nella finestra di **Esplora oggetti** di SSMS selezionare **Azure-SSIS Integration Runtime** nel menu a discesa **Connetti** . Successivamente, accedere ad Azure e selezionare la sottoscrizione pertinente, ADF e Azure-SSIS IR, a cui sono collegati gli archivi dei pacchetti. Il Azure-SSIS IR verrà visualizzato con i nodi **in esecuzione** e i **pacchetti archiviati** sottostanti. Espandere il nodo **pacchetti archiviati** per visualizzare gli archivi dei pacchetti sottostanti. Espandere gli archivi pacchetti per visualizzare le cartelle e i pacchetti sottostanti. È possibile che venga richiesto di immettere le credenziali di accesso per gli archivi pacchetti, se SSMS non riesce a connettersi automaticamente. Se, ad esempio, si espande un archivio pacchetti su MSDB, potrebbe essere richiesto di connettersi prima al Istanza gestita SQL di Azure.

![Connettersi a Istanza gestita SQL di Azure](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gestione di cartelle e pacchetti

Quando si esplorano i Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su qualsiasi archivio/cartella/Pacchetti di pacchetti per visualizzare un menu e selezionare **nuova cartella**, **Importa pacchetto**, **Esporta pacchetto**, **Elimina**o **Aggiorna**.

   ![Gestione di cartelle e pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selezionare **nuova cartella** per creare una nuova cartella per i pacchetti importati.

   *  Selezionare **Importa pacchetto** per importare pacchetti da **File System**, **SQL Server** (msdb) o dall' **Archivio pacchetti SSIS** legacy nell'archivio pacchetti.

      ![Importare il pacchetto](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      A seconda del **percorso del pacchetto** da cui eseguire l'importazione, selezionare il tipo di autenticazione **Server**pertinente / **Authentication type**, immettere le credenziali di accesso, se necessario, selezionare il **percorso del pacchetto**e immettere il nuovo **nome del pacchetto**. Quando si importano pacchetti, il livello di protezione non può essere modificato. Per modificarlo, utilizzare SQL Server Data Tools (SSDT) o l' `dtutil` utilità della riga di comando.

   *  Selezionare **Esporta pacchetto** per esportare i pacchetti dall'archivio pacchetti nel **file System**, nel **SQL Server** (msdb) o nell' **Archivio pacchetti SSIS**legacy.

      ![Esporta pacchetto](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      A seconda del **percorso del pacchetto** in cui eseguire l'esportazione, selezionare il tipo di autenticazione **Server**pertinente / **Authentication type**, immettere le credenziali di accesso, se necessario, e selezionare il **percorso del pacchetto**. Quando si esportano i pacchetti, se sono crittografati, immettere le password per decrittografarle prima e quindi modificare il livello di protezione, ad esempio per non salvare i dati sensibili o crittografare tutti i dati sensibili con la chiave utente e la password.

   *  Selezionare **Elimina** per eliminare le cartelle o i pacchetti esistenti dall'archivio pacchetti.

   *  Selezionare **Aggiorna** per visualizzare le cartelle o i pacchetti appena aggiunti nell'archivio pacchetti.

## <a name="execute-packages"></a>Esegui pacchetti

Quando si esplorano i Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su tutti i pacchetti archiviati per visualizzare un menu e scegliere **Esegui pacchetto**.  Verrà visualizzata la finestra di dialogo **utilità di esecuzione pacchetti** , in cui è possibile configurare le esecuzioni dei pacchetti in Azure-SSIS IR come eseguire le attività del pacchetto SSIS nelle pipeline di ADF.

![Utilità di esecuzione pacchetti pagine 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Utilità di esecuzione pacchetti pagine 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Le pagine **generale**, **configurazioni**, **Opzioni di esecuzione**e **registrazione** della finestra di dialogo **utilità di esecuzione pacchetti** corrispondono alla scheda **Impostazioni** dell'attività Esegui pacchetto SSIS, in cui è possibile immettere la password di crittografia per il pacchetto, accedere alle informazioni per il file di configurazione del pacchetto, credenziali/proprietà di esecuzione del pacchetto e informazioni di accesso per la cartella dei log.  La pagina **Imposta valori** della finestra di dialogo **utilità di esecuzione pacchetti** corrisponde alla scheda **override proprietà** dell'attività Esegui pacchetto SSIS, in cui è possibile immettere le proprietà del pacchetto esistenti di cui eseguire l'override. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Quando si seleziona il pulsante **Esegui** della finestra di dialogo **utilità di esecuzione pacchetti** , viene automaticamente generata e attivata una nuova pipeline ADF con l'attività Esegui pacchetto SSIS. Se una pipeline ADF con le stesse impostazioni di esecuzione del pacchetto esiste già, verrà rieseguita e non verrà generata una nuova pipeline. La pipeline ADF e l'attività Esegui pacchetto SSIS verranno rispettivamente denominate `Pipeline_SSMS_YourPackageName_HashString` e `Activity_SSMS_YourPackageName` .

![Pulsante Utilità di esecuzione pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Attività Esegui pacchetto SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorare e arrestare l'esecuzione di pacchetti

Quando si esplorano le Azure-SSIS IR in SSMS, è possibile espandere il nodo **pacchetti in esecuzione** per visualizzare i pacchetti attualmente in esecuzione sotto.  Fare clic con il pulsante destro del mouse su uno di essi per visualizzare un menu e selezionare **Arresta** o **Aggiorna**.

   ![Monitorare e arrestare l'esecuzione di pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selezionare **Interrompi** per annullare la pipeline ADF attualmente in esecuzione che esegue il pacchetto come attività Esegui pacchetto SSIS.

   *  Selezionare **Aggiorna** per visualizzare i pacchetti di nuova esecuzione dagli archivi dei pacchetti.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorare Azure-SSIS IR e modificare gli archivi pacchetti

Quando si esplorano le Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su di esso per visualizzare un menu e selezionare **Vai a Azure Data Factory portale** o **Aggiorna**.

   ![Vai al portale di ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selezionare **Vai al portale Azure Data Factory** per aprire la pagina **Integration Runtimes** dell'hub di monitoraggio ADF, in cui è possibile monitorare la Azure-SSIS IR. Nel riquadro **archivi pacchetti** è possibile visualizzare il numero di archivi pacchetti collegati al Azure-SSIS IR.  Se si seleziona tale numero, viene visualizzata una finestra in cui è possibile modificare i servizi collegati ADF in cui sono archiviate le informazioni di accesso per gli archivi dei pacchetti.

      ![Modifica archivi pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selezionare **Aggiorna** per visualizzare le cartelle o i pacchetti appena aggiunti negli archivi pacchetti ed eseguendo i pacchetti dagli archivi dei pacchetti.

## <a name="next-steps"></a>Passaggi successivi

È possibile rieseguire/modificare le pipeline di ADF generate automaticamente con le attività Esegui pacchetto SSIS o crearne di nuove nel portale di ADF. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).