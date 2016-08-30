<properties 
   pageTitle="Adattatore StorSimple per SharePoint | Microsoft Azure"
   description="Viene descritto come installare e configurare o rimuovere l'adattatore StorSimple per SharePoint in una server farm di SharePoint."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# Installare e configurare l’adattatore StorSimple per SharePoint

## Overview

L'adattatore StorSimple per SharePoint è un componente che consente di fornire a Microsoft Azure StorSimple archiviazione flessibile e protezione dei dati per server farm di SharePoint. È possibile utilizzare l'adattatore per spostare il contenuto di oggetti BLOB (Binary Large Object) dai database del contenuto di SQL Server al dispositivo di archiviazione cloud ibrida StorSimple di Microsoft Azure.

L'adattatore StorSimple per SharePoint funziona come provider di archiviazione BLOB remoti (RBS) e utilizza la funzionalità di archiviazione BLOB remoti di SQL Server per archiviare il contenuto di SharePoint non strutturato (sotto forma di BLOB) in un file server supportato da un dispositivo StorSimple.

>[AZURE.NOTE] L'adattatore StorSimple per SharePoint supporta Archiviazione BLOB remoti di SharePoint Server 2010 (RBS). Non supporta Archiviazione BLOB esterni di SharePoint Server 2010 (EBS).

- Per scaricare l'adattatore StorSimple per SharePoint, visitare la pagina dell’[adattatore StorSimple per SharePoint][1] nell’Area download di Microsoft.

- Per informazioni sulla pianificazione per RBS e le limitazioni di RBS, vedere [Deciding to use RBS in SharePoint 2013][2] o [Pianificare Archiviazione BLOB remoti (SharePoint Foundation 2010)][3].

Nella parte rimanente di questa panoramica vengono descritti brevemente il ruolo dell'adattatore StorSimple per SharePoint e i limiti di capacità e prestazioni di SharePoint che è necessario conoscere prima di installare e configurare l'adattatore. Dopo avere esaminato le informazioni, andare a [Installazione dell'adattatore StorSimple per SharePoint](#storsimple-adapter-for-sharepoint-installation) per iniziare a configurare l'adattatore.

### Vantaggi dell’adattatore StorSimple per SharePoint

In un sito di SharePoint, il contenuto viene archiviato come dati BLOB non strutturati in uno o più database del contenuto. Per impostazione predefinita, questi database sono ospitati in computer che esegue SQL Server e si trovano in server farm di SharePoint. Le dimensioni dei BLOB possono aumentare rapidamente, consumando ingenti quantità di spazio di archiviazione locale. Per questo motivo, è utile trovare un’altra soluzione di archiviazione meno costosa. SQL Server fornisce una tecnologia denominata Archiviazione Blob remoti (RBS) che consente di archiviare il contenuto BLOB nel file system, all'esterno del database di SQL Server. Con RBS i BLOB possono risiedere nel file system del computer che esegue SQL Server oppure essere archiviati nel file system di un altro computer server.

RBS richiede l'utilizzo di un provider RBS, ad esempio l'adattatore StorSimple per SharePoint, per abilitare RBS in SharePoint. L'adattatore StorSimple per SharePoint funziona con RBS, consentendo di spostare i BLOB in un server supportato dal sistema di Microsoft Azure StorSimple. Microsoft Azure StorSimple archivia quindi i dati BLOB localmente o nel cloud, in base all'utilizzo. I BLOB molto attivi (in genere denominati livello 1 o hot data) risiedono nel computer locale. I dati meno attivi e i dati di archiviazione si trovano nel cloud. Dopo aver abilitato RBS su un database del contenuto, qualsiasi nuovo contenuto BLOB creato in SharePoint verrà archiviato nel dispositivo StorSimple e non nel database del contenuto.

L’implementazione di RBS in Microsoft Azure StorSimple offre i vantaggi seguenti:

- Spostando il contenuto BLOB in un server separato, è possibile ridurre il carico di query su SQL Server, migliorando la velocità di risposta di SQL Server.

- StorSimple di Azure utilizza deduplicazione e compressione per ridurre la dimensione dei dati,

- nonché protezione dei dati sotto forma di snapshot locali e su cloud. Inoltre, se si colloca il database stesso nel dispositivo StorSimple, è possibile eseguire il backup del database del contenuto e dei BLOB insieme, in un modo coerente con l’arresto anomalo. Lo spostamento del database del contenuto nel dispositivo è supportato solo per il dispositivo StorSimple serie 8000, e non per le serie 5000 e 7000.

- StorSimple di Azure include funzionalità di ripristino di emergenza tra cui il failover, il ripristino di file e volumi (inclusi i test di ripristino) e un ripristino rapido dei dati.

- È possibile utilizzare software di ripristino di dati, ad esempio Kroll Ontrack PowerControls, con gli snapshot di StorSimple dei dati BLOB per eseguire il ripristino a livello di elemento di contenuto di SharePoint. Questo software di ripristino dati costituisce un acquisto separato.

- L'adattatore StorSimple per SharePoint viene inserito nel portale di amministrazione centrale di SharePoint, consentendo di gestire l'intera soluzione SharePoint da una posizione centrale.

Spostando il contenuto BLOB nel file system è possibile ottenere altri vantaggi e risparmi sui costi. Ad esempio, tramite RBS si può diminuire la costosa archiviazione di livello 1 necessaria e, poiché riduce il database del contenuto, RBS consente di ridurre il numero di database richiesti nella server farm di SharePoint. Altri fattori quali i limiti di dimensioni del database e la quantità di contenuto non RBS possono, tuttavia, influire sui requisiti di archiviazione. Per ulteriori informazioni sui costi e sui vantaggi dell'utilizzo di RBS, vedere [Pianificare RBS (SharePoint Foundation 2010)][4] e [Deciding to use RBS in SharePoint 2013][5].

### Limiti di capacità e prestazioni

Prima di prendere in considerazione l'uso di RBS nella soluzione SharePoint, è necessario valutare i limiti di capacità e prestazioni testate di SharePoint Server 2010 e SharePoint Server 2013 e come tali limiti sono correlati a prestazioni accettabili. Per ulteriori informazioni, vedere [Limiti del software e limiti per SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Prima di configurare RBS, effettuare le operazioni seguenti:

- Assicurarsi che la dimensione totale del contenuto (le dimensioni di un database del contenuto più la dimensione di eventuali BLOB esternalizzati) non superi il limite delle dimensioni RBS supportato da SharePoint. Questo limite è pari a 200 GB.

    **Per misurare le dimensioni di BLOB e database del contenuto**

     1. Eseguire questa query sul front-end Web di Amministrazione centrale. Avviare la shell di gestione di SharePoint e quindi immettere il seguente comando di Windows PowerShell per ottenere le dimensioni dei database del contenuto:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         Questo passaggio consente di ottenere la dimensione del database del contenuto sul disco.

     2. Eseguire una delle query SQL seguenti in SQL Management Studio sul server SQL in ogni database del contenuto e aggiungere il risultato al numero ottenuto nel passaggio 1.

        Nei database del contenuto di SharePoint 2013, immettere:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Nei database del contenuto di SharePoint 2010, immettere:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Questo passaggio consente di ottenere le dimensioni dei BLOB che sono stati esternalizzati.

- È consigliabile archiviare localmente tutto il contenuto di BLOB e di database sul dispositivo StorSimple. Il dispositivo StorSimple è un cluster a due nodi per la disponibilità elevata. Collocare i database del contenuto e i BLOB nel dispositivo StorSimple per garantire un'elevata disponibilità.

    Utilizzare le tradizionali procedure consigliate per la migrazione di SQL Server per spostare il database del contenuto nel dispositivo StorSimple. Spostare il database solo dopo che tutto il contenuto BLOB dal database è stato spostato nella condivisione di file tramite RBS. Se si sceglie di spostare il database del contenuto nel dispositivo StorSimple, è consigliabile configurare l'archiviazione del database del contenuto sul dispositivo come volume principale.

- In Microsoft Azure StorSimple, se si utilizzano i volumi a livelli, non è possibile garantire che il contenuto archiviato localmente nel dispositivo StoreSimple non verrà suddiviso in livelli nell’archiviazione cloud di Microsoft Azure. Di conseguenza, si consiglia di utilizzare i volumi StorSimple aggiunti in locale in combinazione con RBS di SharePoint, affinché tutto il contenuto BLOB rimanga in locale sul dispositivo StorSimple, senza essere spostato in Microsoft Azure.

- Se non si archiviano i database del contenuto nel dispositivo StorSimple, utilizzare le tradizionali procedure consigliate per la disponibilità elevata di SQL Server che supportano RBS. Il clustering di SQL Server supporta RBS, mentre il mirroring di SQL Server no.

>[AZURE.WARNING] Se RBS non è abilitato, non è consigliabile spostare il database del contenuto nel dispositivo StorSimple. Si tratta di una configurazione non testata.
 
## Installazione dell’adattatore StorSimple per SharePoint

Prima di installare l'adattatore StorSimple per SharePoint, è necessario configurare il dispositivo StorSimple e verificare che la server farm di SharePoint e la creazione di istanze di SQL Server soddisfino tutti i prerequisiti. In questa esercitazione vengono descritti i requisiti di configurazione, nonché le procedure per installare e aggiornare l’adattatore StorSimple per SharePoint.

## Configurazione dei prerequisiti

Prima di installare l'adattatore StorSimple per SharePoint, è necessario verificare che il dispositivo StorSimple, la server farm di SharePoint e la creazione di istanze di SQL Server soddisfino i prerequisiti seguenti.

### Requisiti di sistema

L'adattatore StorSimple per SharePoint funziona con hardware e software seguenti:

- Sistema operativo a supportato: Windows Server 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2.

- Versioni supportate di SharePoint: SharePoint Server 2010 o SharePoint Server 2013

- Versioni supportate di SQL Server: SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition o SQL Server 2012 Enterprise Edition

- Dispositivi StorSimple supportati: StorSimple serie 8000, StorSimple serie 7000 o StorSimple serie 5000.

### Prerequisiti di configurazione del dispositivo StorSimple

Il dispositivo StorSimple è un dispositivo a blocchi e pertanto richiede un file server in cui possano essere ospitati i dati. È consigliabile utilizzare un server separato anziché un server esistente dalla farm di SharePoint. Questo file server deve risiedere nella stessa rete locale (LAN) del computer SQL Server che ospita i database del contenuto.

>[AZURE.TIP] 
>
>- Se si configura la farm di SharePoint per la disponibilità elevata, è necessario distribuire anche il file server per la disponibilità elevata.
>
>- Se non si archivia il database del contenuto nel dispositivo StorSimple, utilizzare le tradizionali procedure consigliate per la disponibilità elevata che supportano RBS. Il clustering di SQL Server supporta RBS, mentre il mirroring di SQL Server no.

Assicurarsi che il dispositivo StorSimple sia configurato correttamente e che volumi appropriati per supportare la distribuzione di SharePoint siano configurati e accessibili dal computer SQL Server. Andare a [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough.md) se non è stato ancora distribuito e configurato il dispositivo StorSimple. Prendere nota dell'indirizzo IP del dispositivo StorSimple perché sarà necessario durante l'installazione dell’adattatore StorSimple per SharePoint.

Inoltre, assicurarsi che il volume da utilizzare per l'esternalizzazione dei BLOB soddisfi i requisiti seguenti:

- Il volume deve essere formattato con una dimensione di unità di allocazione di 64 KB.

- Il front-end web (WFE) e i server applicazioni devono essere in grado di accedere al volume tramite un percorso UNC (Universal Naming Convention).

- Per scrivere sul volume, è necessario configurare la server farm di SharePoint.

>[AZURE.NOTE] Dopo l'installazione e configurazione dell'adattatore, tutta l’esternalizzazione dei BLOB deve passare attraverso il dispositivo StorSimple (il dispositivo presenterà i volumi a SQL Server e gestirà i livelli di archiviazione). Non è possibile utilizzare altre destinazioni per l'esternalizzazione dei BLOB.
 
Se si prevede di utilizzare Gestione snapshot StorSimple per acquisire snapshot dei dati di BLOB e database, assicurarsi di installare Gestione snapshot StorSimple nel server di database in modo da poter utilizzare il servizio writer SQL per implementare il servizio Copia Shadow del volume (VSS) di Windows.

>[AZURE.IMPORTANT] Gestione snapshot StorSimple non supporta SharePoint VSS Writer e non può eseguire snapshot dei dati di SharePoint coerenti con l’applicazione. In uno scenario di SharePoint, Gestione snapshot StorSimple fornisce soltanto backup coerenti con l'arresto anomalo del sistema.
 
## Prerequisiti di configurazione di SharePoint

Assicurarsi che la server farm di SharePoint sia configurata correttamente, come indicato di seguito:

- Verificare che la server farm di SharePoint sia in uno stato integro e verificare quanto segue:

- Tutti i server applicazioni e WFE di SharePoint registrati nella farm devono essere in esecuzione e poter ricevere il ping dal server in cui verrà installato l'adattatore StorSimple per SharePoint.

- Il servizio Timer di SharePoint (SPTimerV3 o SPTimerV4) deve essere in esecuzione in ogni server WFE e server applicazioni.

- Il servizio Timer di SharePoint e il pool di applicazioni IIS in cui è in esecuzione il sito Amministrazione centrale SharePoint devono disporre di privilegi amministrativi.

- Verificare che Internet Explorer Enhanced Security Context (IE ESC) sia disabilitato. Attenersi alla procedura seguente per disabilitare IE ESC:

    1. Chiudere tutte le istanze di Internet Explorer.

    2. Avviare Server Manager.

    3. Nel riquadro sinistro fare clic su **Server locale**.

    4. Nel riquadro destro, accanto a **Configurazione sicurezza avanzata IE**, fare clic su **Attivo**.

    5. In **Amministratori** fare clic su **Disattivo**.

    6. Fare clic su **OK**.

## Prerequisiti di archiviazione BLOB remoti (RBS)

Verificare che sia in uso una versione supportata di SQL Server. Le seguenti versioni sono supportate e in grado di usare RBS:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

È possibile esternalizzare i BLOB soltanto sui volumi che il dispositivo StorSimple presenta a SQL Server. Non sono supportate altre destinazioni per l'esternalizzazione dei BLOB.

Dopo aver completato tutti i passaggi di configurazione prerequisiti, passare a [Installare l'adattatore StorSimple per SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## Installare l’adattatore StorSimple per SharePoint

Utilizzare la procedura seguente per installare l'adattatore StorSimple per SharePoint. Se si reinstalla il software, vedere [Aggiornare o reinstallare l'adattatore StorSimple per SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Il tempo necessario per l'installazione dipende dal numero totale di database di SharePoint nella server farm di SharePoint.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## Configurare SSL

Dopo aver installato l'adattatore StorSimple per SharePoint, configurare RBS come descritto nella procedura seguente.

>[AZURE.TIP] L'adattatore StorSimple per SharePoint viene inserito nella pagina Amministrazione centrale SharePoint, consentendo di abilitare o disabilitare RBS in ogni database del contenuto nella farm di SharePoint. Tuttavia, l’abilitazione o la disabilitazione di RBS sul database del contenuto comporta una reimpostazione di IIS che, a seconda della configurazione della farm, può interrompere momentaneamente la disponibilità del front-end Web (WFE) di SharePoint. Fattori quali l'utilizzo di un servizio di bilanciamento del carico di front-end, il carico di lavoro server corrente e così via, possono limitare o evitare l'interruzione. Per proteggere gli utenti da un'interruzione, si consiglia di abilitare o disabilitare RBS solo durante un periodo di manutenzione pianificata.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## Configurare Garbage Collection

Quando gli oggetti vengono eliminati da un sito di SharePoint, non vengono eliminati automaticamente dal volume dell'archivio RBS. Al contrario, un programma di manutenzione in background asincrono elimina i BLOB orfani dall'archivio di file. Gli amministratori di sistema possono pianificare di eseguire periodicamente il processo oppure avviarlo quando necessario.

Questo programma di manutenzione (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) viene installato automaticamente su tutti i server WFE e server applicazioni di SharePoint quando si abilita RBS. Il programma viene installato nel percorso seguente: *unità di avvio*:\\Programmi\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer\\

Per informazioni sulla configurazione e l'utilizzo del programma di manutenzione, vedere [Manutenzione di RBS in SharePoint Server 2013][8].

>[AZURE.IMPORTANT] Il programma di manutenzione RBS è ad elevato consumo delle risorse. È consigliabile pianificarne l'esecuzione solo durante i periodi di minore attività della farm di SharePoint.

### Eliminare BLOB orfani immediatamente

Se è necessario eliminare immediatamente BLOB orfani, è possibile utilizzare le istruzioni seguenti. Si noti che queste istruzioni sono un esempio di come questa operazione può essere eseguita in un ambiente SharePoint 2013 con i componenti seguenti:

- Il nome di database del contenuto è WSS\_Content.
- Il nome di SQL Server è SHRPT13 SQL12\\SHRPT13.
- Il nome dell'applicazione Web è SharePoint – 80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## Aggiornare o reinstallare l’adattatore StorSimple per SharePoint

Utilizzare la procedura seguente per eseguire l'aggiornamento del server SharePoint e quindi reinstallare l'adattatore StorSimple per SharePoint o per aggiornare o reinstallare semplicemente l'adattatore in una server farm di SharePoint esistente.

>[AZURE.IMPORTANT] Esaminare le informazioni seguenti prima di tentare di aggiornare il software di SharePoint e/o aggiornare o reinstallare l'adattatore StorSimple per SharePoint:
>
>- Tutti i file precedentemente spostati in una risorsa di archiviazione esterna tramite RBS non saranno disponibili fino al termine della reinstallazione e fino a quando la funzionalità RBS non viene riabilitata. Per limitare l'impatto sugli utenti, eseguire l'aggiornamento o la reinstallazione in un periodo di manutenzione pianificata.
>
>- Il tempo necessario per l'installazione/aggiornamento dipende dal numero totale di database di SharePoint nella server farm di SharePoint.
>
>- Al termine dell'aggiornamento/reinstallazione, è necessario abilitare RBS per i database del contenuto. Per altre informazioni, vedere [Configurare RBS](#configure-rbs).
>
>- Se si configura RBS per una farm di SharePoint che dispone di un numero molto elevato di database (più di 200), la pagina **Amministrazione centrale SharePoint** potrebbe scadere. In questo caso, aggiornare la pagina. Questo evento non influisce sul processo di configurazione.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## Rimozione dell’adattatore StorSimple per SharePoint

Le procedure seguenti descrivono come spostare nuovamente i BLOB nei database del contenuto di SQL Server e quindi disinstallare l'adattatore StorSimple per SharePoint.

>[AZURE.IMPORTANT] È necessario spostare nuovamente i BLOB nei database del contenuto prima di disinstallare il software dell'adattatore.

### Prima di iniziare 

Prima di spostare i dati nei database del contenuto di SQL Server e iniziare il processo di rimozione dell’adattatore, raccogliere le informazioni seguenti:

- I nomi di tutti i database per cui è abilitato RBS
- Il percorso UNC dell'archivio BLOB configurato

### Spostare i BLOB nei database del contenuto

Prima di disinstallare l'adattatore StorSimple per il software di SharePoint, è necessario eseguire la migrazione di tutti i BLOB che sono stati esternalizzati ai database del contenuto di SQL Server. Se si tenta di disinstallare l'adattatore StorSimple per SharePoint prima di spostare nuovamente tutti i BLOB nei database del contenuto, verrà visualizzato il seguente messaggio di avviso.

![Messaggio di avviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####Per spostare nuovamente i BLOB nei database del contenuto 

1. Scaricare ciascun oggetto esternalizzato.

2. Aprire la pagina **Amministrazione centrale SharePoint** e passare a**Impostazioni di sistema**.

3. Nella sezione**Azure StorSimple**fare clic su**Configura l'adattatore StorSimple**.

4. Nella pagina **Configura l'adattatore StorSimple** fare clic sul pulsante **Disattiva** sotto ciascuno dei database di contenuto che si desidera rimuovere dall'archiviazione BLOB esterna.

5. Eliminare gli oggetti da SharePoint e caricarli nuovamente.

In alternativa, è possibile utilizzare il cmdlet di Microsoft PowerShell ` RBS Migrate()` incluso in SharePoint. Per ulteriori informazioni, vedere [Migrazione del contenuto in o da RBS](https://technet.microsoft.com/library/ff628255.aspx).

Dopo aver spostato nuovamente i BLOB nel database di contenuto, andare al passaggio successivo: [Disinstallare l'adattatore](#uninstall-the-adapter).

### Disinstallare l'adattatore

Dopo aver spostato nuovamente i BLOB nei database del contenuto di SQL Server, utilizzare una delle seguenti opzioni per disinstallare l'adattatore StorSimple per SharePoint.

#### Per utilizzare il programma di installazione per disinstallare l'adattatore 

1. Utilizzare un account con privilegi di amministratore per l'accesso al server front-end Web (WFE).
2. Fare doppio clic sull'adattatore StorSimple per il programma di istallazione di SharePoint. Verrà avviata l'installazione guidata.

    ![Installazione guidata](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. Fare clic su **Avanti**. Verrà visualizzata la pagina seguente.

    ![Pagina di rimozione dell’istallazione guidata](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. Fare clic su **Rimuovi** per selezionare il processo di rimozione. Verrà visualizzata la pagina seguente.

    ![Pagina di conferma dell'installazione guidata](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. Fare clic su **Rimuovi** per confermare la rimozione. Verrà visualizzata la seguente pagina di avanzamento.

    ![Pagina di stato di avanzamento dell'installazione guidata](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. Una volta completata la rimozione, viene visualizzata la pagina di fine. Fare clic su**Fine**per chiudere l'installazione guidata.

#### Per utilizzare il Pannello di controllo per disinstallare l'adattatore 

1. Aprire il Pannello di controllo, quindi fare clic su **Programmi e funzionalità**.

2. Selezionare l’**Adattatore StorSimple per SharePoint**, quindi fare clic su **Disinstalla**.

## Passaggi successivi

[Ulteriori informazioni su StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/it-IT/library/ff943565.aspx

<!---HONumber=AcomDC_0817_2016-->