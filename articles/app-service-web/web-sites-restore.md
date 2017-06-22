---
title: Ripristinare un&quot;app in Azure
description: Informazioni su come ripristinare l&quot;app da un backup.
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 49aa35a42e4f6dab2f8d556f7b1b10bfdef7b7db
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="restore-an-app-in-azure"></a>Ripristinare un'app in Azure
Questo articolo illustra come ripristinare un'app nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in precedenza sottoposta a un backup. Vedere [Eseguire il backup di un'app Web del Servizio app di Azure](web-sites-backup.md). È possibile ripristinare su richiesta a uno stato precedente l'app con i relativi database collegati oppure creare una nuova app basata su uno dei backup dell'applicazione originale. Servizio app di Azure supporta il backup e il ripristino dei seguenti database:
- [Database SQL](https://azure.microsoft.com/en-us/services/sql-database/)
- [Database di Azure per MySQL (anteprima)](https://azure.microsoft.com/en-us/services/mysql)
- [Database di Azure per PostgreSQL (anteprima)](https://azure.microsoft.com/en-us/services/postgres)
- [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Il ripristino da backup è disponibile per le app in esecuzione a livello **Standard** e **Premium**. Per informazioni sul passaggio dell'app a un piano superiore, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md). Il livello **Premium** consente un maggior numero di backup giornalieri rispetto al livello **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Ripristinare un'app da un backup esistente
1. Nel pannello **Impostazioni** dell'app nel portale di Azure fare clic su **Backup** per visualizzare il pannello **Backup**. Quindi fare clic su **Ripristina ora** nella barra dei comandi.
   
    ![Scegliere Ripristina][ChooseRestoreNow]
2. Nel pannello **Ripristina** , selezionare l'origine di backup.
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    L'opzione **Backup dell'app** mostra tutti i backup esistenti dell'app corrente, che possono essere facilmente selezionati.
    L'opzione **Archiviazione** consente di selezionare qualsiasi file ZIP del backup da un account di archiviazione e un contenitore di Azure esistenti nella sottoscrizione.
    Se si sta tentando di ripristinare un backup di un'altra app, usare l'opzione **Archiviazione** .
3. Quindi, specificare la destinazione per il ripristino dell’app in **Destinazione di ripristino**.
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > Se si sceglie **Sovrascrivi**, tutti i dati esistenti nell'app corrente verranno cancellati e sovrascritti. Prima di scegliere **OK**, assicurarsi che sia esattamente ciò che si desidera eseguire.
   > 
   > 
   
    È possibile selezionare **App esistente** per ripristinare il backup dell’app in un'altra applicazione nello stesso gruppo di risorse. Prima di usare questa opzione, deve già essere stata creata un'altra app nel gruppo di risorse con mirroring della configurazione in quello definito nel backup dell’app.

4. Fare clic su **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Scaricare o eliminare un backup da un account di archiviazione
1. Dal pannello principale **Sfoglia** del portale di Azure selezionare **Account di archiviazione**. Verrà visualizzato un elenco degli account di archiviazione esistenti.
2. Selezionare l'account di archiviazione che contiene il backup che si intende scaricare o eliminare. Verrà visualizzato il pannello dell'account di archiviazione.
3. Nel pannello dell'account di archiviazione selezionare il contenitore desiderato.
   
    ![Contenitori di visualizzazione][ViewContainers]
4. Selezionare il file di backup che si desidera scaricare o eliminare.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Fare clic su **Scarica** o **Elimina** a seconda dell'azione da eseguire.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorare un'operazione di ripristino
Per visualizzare i dettagli sul successo o sulla mancata riuscita dell'operazione di ripristino dell'app, passare al pannello **Log attività** nel portale di Azure.  
 

Scorrere verso il basso per trovare l'operazione di ripristino desiderata e fare clic su di essa per selezionarla.

Nel pannello dei dettagli verranno visualizzate le informazioni disponibili correlate all'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi
È possibile eseguire il backup e il ripristino delle applicazioni del servizio app usando l'API REST (vedere [Usare REST per eseguire il backup e il ripristino di app del servizio App](websites-csm-backup.md)).


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

