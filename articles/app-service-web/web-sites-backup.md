---
title: Eseguire il backup dell&quot;app in Azure
description: Informazioni sulla creazione dei backup delle app nel Servizio app di Azure.
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b00c320eeb1e8b30a0fad9634844ceeca60d1c29


---
# <a name="back-up-your-app-in-azure"></a>Eseguire il backup dell'app in Azure
La funzionalità di backup e ripristino nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) consente di creare facilmente backup di app in modo manuale o automatico. È possibile ripristinare lo stato precedente dell'app oppure creare una nuova app basata su uno dei backup dell'app originale. 

Per informazioni sul ripristino di un'app dal backup, vedere [Ripristinare un'app nel Servizio app di Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Elementi di cui viene eseguito il backup
Il servizio app può eseguire il backup delle seguenti informazioni:

* Configurazione dell'app
* Contenuto del file
* Qualsiasi database SQL o MySQL (ClearDB) di Azure connesso all'app (è possibile scegliere quali includere nel backup)

Il backup di tali informazioni viene effettuato nell'account di archiviazione di Azure e nel contenitore specificato. 

> [!NOTE]
> Ciascun backup è una copia offline completa dell'app, non un aggiornamento incrementale.
> 
> 

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisiti e restrizioni
* Per usufruire della funzionalità di backup e ripristino è necessario che il piano di servizio app si trovi in un livello **Standard** o superiore. Per altre informazioni sul ridimensionamento del piano di servizio app per usare un livello superiore, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md). Si noti che il livello **Premium** consente un maggior numero di backup giornalieri rispetto al livello **Standard**.
* Sono necessari un account di archiviazione e un contenitore di Azure nella stessa sottoscrizione dell'applicazione di cui si desidera eseguire il backup. Per altre informazioni sugli account di archiviazione di Azure, vedere i [collegamenti](#moreaboutstorage) al termine di questo articolo.
* È possibile eseguire il backup di un massimo di 10 GB di contenuto del database e dell'app. Se la dimensione del backup supera questo limite, verrà visualizzato un messaggio di errore. 

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Creare un backup manuale
1. Nel [Portale di Azure](https://portal.azure.com) passare al pannello dell'app, selezionare **Impostazioni**, quindi **Backup**. Viene visualizzato il pannello **Backup** .
   
    ![Pagina Backups][ChooseBackupsPage]
   
   > [!NOTE]
   > Se viene visualizzato il messaggio seguente, fare clic per aggiornare il piano di servizio app prima di procedere con i backup.
   > Per altre informazioni, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md) .  
   > ![Scelta dell'account di archiviazione](./media/web-sites-backup/01UpgradePlan.png)
   > 
   > 
2. Nel pannello **Backup** fare clic su **Archiviazione: non configurata** per configurare un account di archiviazione.
   
    ![Scelta dell'account di archiviazione][ChooseStorageAccount]
3. Scegliere la destinazione del backup selezionando un **Account di archiviazione** e un **Contenitore**. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app da sottoporre a backup. Se si desidera, è possibile creare un nuovo account di archiviazione o un nuovo contenitore nei rispettivi pannelli. Al termine, fare clic su **Seleziona**.
   
    ![Scelta dell'account di archiviazione](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Nel pannello **Configura impostazioni di backup** rimasto aperto fare clic su **Impostazioni database**, quindi selezionare i database da includere nei backup (database SQL o MySQL), quindi fare clic su **OK**.  
   
    ![Scelta dell'account di archiviazione](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Per visualizzare un database nell'elenco, è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni dell'applicazione** per l'app.
   > 
   > 
5. Nel pannello **Configura impostazioni di backup** fare clic su **Salva**.    
6. Nella barra dei comandi del pannello **Backup** fare clic su **Esegui backup ora**.
   
    ![Pulsante BackUp Now][BackUpNow]
   
    Durante il processo di backup verrà visualizzato un messaggio di stato.

Dopo aver configurato un account di archiviazione e un contenitore per backup, è possibile eseguire un backup manuale in qualsiasi momento.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurazione dei backup automatici
1. Nel pannello **Backup** fare clic su **Pianificazione: non configurata**. 
   
    ![Scelta dell'account di archiviazione](./media/web-sites-backup/05ScheduleBackup.png)
2. Nel pannello **Impostazioni di pianificazione backup** impostare **Backup pianificati** su **Attivato**, quindi configurare la pianificazione dei backup come desiderato e fare clic su **OK**.
   
    ![Abilitazione dei backup automatici][SetAutomatedBackupOn]
3. Nel pannello **Configura impostazioni di backup** rimasto aperto fare clic su **Impostazioni di archiviazione**, quindi scegliere la destinazione del backup selezionando un **Account di archiviazione** e un **Contenitore**. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app da sottoporre a backup. Se si desidera, è possibile creare un nuovo account di archiviazione o un nuovo contenitore nei rispettivi pannelli. Al termine, fare clic su **Seleziona**.
   
    ![Scelta dell'account di archiviazione](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Nel pannello **Configura impostazioni di backup** fare clic su **Impostazioni database**, quindi selezionare i database da includere nei backup (database SQL o MySQL), quindi fare clic su **OK**.  
   
    ![Scelta dell'account di archiviazione](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Per visualizzare un database nell'elenco, è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** del pannello **Impostazioni dell'applicazione** per l'app.
   > 
   > 
5. Nel pannello **Configura impostazioni di backup** fare clic su **Salva**.    

<a name="partialbackups"></a>

## <a name="backup-just-part-of-your-app"></a>Eseguire il backup solo di una parte dell'app
In alcuni casi non si vuole eseguire il backup di tutti gli elementi dell'app. Di seguito sono disponibili alcuni esempi:

* Si [configurano backup settimanali](web-sites-backup.md#configure-automated-backups) dell'app che contiene contenuto statico che non cambia mai, ad esempio immagini o post di blog precedenti.
* L'app include oltre 10 GB di contenuto, ovvero la quantità massima per volta di cui è possibile eseguire il backup.
* Non si vuole eseguire il backup dei file di log.

I backup parziali consentono di scegliere esattamente i file di cui si desidera eseguire il backup.

### <a name="exclude-files-from-your-backup"></a>Escludere file dal backup
Per escludere i file e le cartelle dai backup, creare un file `_backup.filter` nella cartella D:\home\site\wwwroo dell'app e specificare l'elenco di file e cartelle da escludere. È possibile eseguire facilmente l'accesso attraverso la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). 

Si supponga di avere un'app che contiene file di log e immagini statiche degli anni precedenti che non verranno mai modificati e di avere già un backup completo dell'app che include le vecchie immagini. Si vuole ora eseguire il backup dell'app ogni giorno, ma senza pagare per l'archiviazione di file di log o dei file di immagini statici che non vengono mai modificati.

![Cartella log][LogsFolder]
![Cartella Immagini][ImagesFolder]

I passaggi seguenti illustrano come escludere tali file dal backup.

1. Passare a `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e identificare le cartelle da escludere dai backup. In questo esempio si vogliono escludere i file e le cartelle seguenti visualizzati nell'interfaccia utente:
   
        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png
   
    [AZURE.NOTE] L'ultima riga mostra che è possibile escludere singoli file e cartelle.
2. Creare un file denominato `_backup.filter` e inserire l'elenco precedente nel file, ma rimuovere `D:\home`. Elencare una directory o un file per ogni riga. Il contenuto del file dovrebbe essere analogo al seguente:
   
    \site\wwwroot\Logs  \LogFiles  \site\wwwroot\Images\2013  \site\wwwroot\Images\2014  \site\wwwroot\Images\brand.png
3. Caricare questo file nella directory `D:\home\site\wwwroot\` del sito usando [ftp](web-sites-deploy.md#ftp) o qualsiasi altro metodo. Se si desidera, è possibile creare il file direttamente in `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e inserire i contenuti nel file.
4. Eseguire i backup secondo la procedura consueta, ovvero [manualmente](#create-a-manual-backup) o [automaticamente](#configure-automated-backups).

Eventuali file e cartelle specificati in `_backup.filter` verranno ora esclusi dal backup. In questo esempio i file di log e i file di immagine del 2013 e del 2014 non verranno più inclusi nel backup, oltre al file brand.png.

> [!NOTE]
> È possibile ripristinare i backup parziali del sito nello stesso modo in cui si [ripristina un backup regolare](web-sites-restore.md). Il processo di ripristino eseguirà le operazioni corrette.
> 
> Dopo il ripristino di un backup completo, tutti i contenuti del sito vengono sostituiti dai contenuti del backup. Se un file è presente nel sito ma non nel backup, verrà eliminato. Quando viene ripristinato un backup parziale, tuttavia, eventuali contenuti presenti in una delle directory disattivate o in un file disattivato verranno lasciati invariati.
> 
> 

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Modalità di archiviazione dei backup
Dopo l'esecuzione di uno o più backup per l'app, i backup saranno visibili nel pannello **Contenitori** dell'account di archiviazione, così come l'app. Nell'account di archiviazione ogni backup è costituito da un file con estensione zip contenente i dati di backup e un file con estensione xml contenente un manifesto dei contenuti del file con estensione zip. È possibile decomprimere e sfogliare questi file se si vuole accedere ai backup senza eseguire effettivamente un ripristino delle app.

Il backup del database per l'app viene archiviato nella radice del file con estensione zip. Per un database SQL può essere un file BACPAC (nessuna estensione di file) e può essere importato. Per creare un nuovo database SQL basato sull'esportazione BACPAC, vedere [Importare un file BACPAC per creare un nuovo database utente](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sul ripristino di un'app da un backup, vedere [Ripristinare un'app nel Servizio app di Azure](web-sites-restore.md). È anche possibile eseguire il backup e il ripristino delle applicazioni del servizio app mediante l'API REST (vedere [Usare REST per eseguire il backup e il ripristino di app del servizio App](websites-csm-backup.md)).

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png




<!--HONumber=Feb17_HO2-->


