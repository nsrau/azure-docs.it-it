---
title: Ripristinare un'app in Azure
description: Informazioni su come ripristinare l'app da un backup.
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
ms.openlocfilehash: 79d4084deb6d8c028918690c339c21c720e63594
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-an-app-in-azure"></a>Ripristinare un'app in Azure
Questo articolo illustra come ripristinare un'app nel [Servizio app di Azure](../app-service/app-service-web-overview.md) in precedenza sottoposta a un backup. Vedere [Eseguire il backup di un'app Web del Servizio app di Azure](web-sites-backup.md). È possibile ripristinare su richiesta a uno stato precedente l'app con i relativi database collegati oppure creare una nuova app basata su uno dei backup dell'applicazione originale. Servizio app di Azure supporta il backup e il ripristino dei seguenti database:
- [Database SQL](https://azure.microsoft.com/en-us/services/sql-database/)
- [Database di Azure per MySQL (anteprima)](https://azure.microsoft.com/en-us/services/mysql)
- [Database di Azure per PostgreSQL (anteprima)](https://azure.microsoft.com/en-us/services/postgres)
- [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Il ripristino da backup è disponibile per le app in esecuzione a livello **Standard** e **Premium**. Per informazioni sul passaggio dell'app a un piano superiore, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md). Il livello **Premium** consente un maggior numero di backup giornalieri rispetto al livello **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Ripristinare un'app da un backup esistente
1. Nel pannello **Impostazioni** dell'app nel Portale di Azure fare clic su **Backup** per visualizzare il pannello **Backup**. Fare quindi clic su **Ripristina**.
   
    ![Scegliere Ripristina][ChooseRestoreNow]
2. Nel pannello **Ripristina** , selezionare l'origine di backup.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    L'opzione **Backup dell'app** mostra tutti i backup esistenti dell'app corrente, che possono essere facilmente selezionati.
    L'opzione **Archiviazione** consente di selezionare qualsiasi file ZIP del backup da un account di archiviazione e un contenitore di Azure esistenti nella sottoscrizione.
    Se si sta tentando di ripristinare un backup di un'altra app, usare l'opzione **Archiviazione** .
3. Quindi, specificare la destinazione per il ripristino dell’app in **Destinazione di ripristino**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se si sceglie **Sovrascrivi**, tutti i dati esistenti nell'app corrente verranno cancellati e sovrascritti. Prima di scegliere **OK**, assicurarsi che sia esattamente ciò che si desidera eseguire.
   > 
   > 
   
    È possibile selezionare **App esistente** per ripristinare il backup dell’app in un'altra applicazione nello stesso gruppo di risorse. Prima di utilizzare questa opzione, deve già essere stata creata un'altra app nel gruppo di risorse con mirroring della configurazione del database in quello definito nel backup dell’app. È anche possibile creare una **nuova** app in cui ripristinare il contenuto.

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

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
