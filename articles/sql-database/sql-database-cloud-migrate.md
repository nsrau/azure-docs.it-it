<properties 
   pageTitle="Migrazione al database SQL di Azure" 
   description="Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/15/2015"
   ms.author="pehteh"/>

# Panoramica
La versione 12 del database SQL di Azure offre una compatibilità quasi completa con SQL Server 2014 e quindi semplifica notevolmente l'attività di migrazione della maggior parte dei database da SQL Server al database SQL di Azure. La migrazione di molti database è un'operazione di spostamento semplice che richiede poche modifiche allo schema, con una minima o addirittura nessuna attività di riprogettazione delle applicazioni. Anche quando è necessario apportare modifiche ai database, l'ambito di tali modifiche è più limitato.

Per impostazione predefinita, le funzionalità con ambito server di SQL Server non sono supportate dal database SQL. Pertanto, le applicazioni e i database basati su queste funzionalità continueranno a richiedere alcune attività di riprogettazione prima di poter essere sottoposti a migrazione. Anche se nella versione 12 del database SQL è stata migliorata la compatibilità con SQL Server, la migrazione deve comunque essere pianificata ed eseguita con attenzione, soprattutto per i database più complessi di dimensioni maggiori.

## Riepilogo
Esistono diversi approcci per la migrazione di un database di SQL Server in Azure, ciascuno dei quali prevede l'uso di uno o più strumenti. Alcuni approcci sono semplici e rapidi, mentre altri richiedono un tempo di preparazione maggiore. Tenere presente che la migrazione di un database complesso di grandi dimensioni può richiedere molte ore.

### Opzione 1
***Migrazione di un database compatibile tramite SQL Server Management Studio (SSMS)***

Il database viene distribuito dal database SQL di Azure tramite SSMS. Può essere distribuito direttamente o esportato in un file BACPAC, che viene quindi importato per creare un nuovo database SQL di Azure. Usare questa opzione quando il database di origine è completamente compatibile con il database SQL di Azure.

### Opzione 2
***Migrazione di un database quasi compatibile tramite la Migrazione guidata database SQL di Azure***

Il database viene elaborato tramite la Migrazione guidata database SQL di Azure, che genera uno script di migrazione contenente solo lo schema oppure lo schema con i dati in formato BCP. Usare questa opzione quando lo schema del database deve essere aggiornato e le modifiche possono essere gestite dalla procedura guidata.

### Opzione 3
***Aggiornamento offline dello schema del database tramite Visual Studio e la Migrazione guidata database SQL di Azure e distribuzione con SSMS***

Il database di origine viene importato in un progetto di database di Visual Studio per l'elaborazione offline. La Migrazione guidata database SQL di Azure viene eseguita in tutti gli script del progetto per applicare una serie di trasformazioni e correzioni. Il progetto viene aggiornato in base alla versione 12 del database SQL e compilato. Gli eventuali errori rimanenti vengono segnalati e quindi risolti manualmente con gli strumenti di SQL Server in Visual Studio. Una volta compilato, il progetto viene pubblicato in una copia del database di origine. Il database aggiornato viene quindi distribuito al database  SQL di Azure mediante l'opzione 1. Se è necessario eseguire la migrazione del solo schema, è possibile pubblicarlo direttamente da Visual Studio nel database SQL di Azure. Usare questa opzione quando lo schema del database richiede più modifiche di quelle che possono essere gestite dalla Migrazione guidata database SQL di Azure.

## Scelta delle opzioni
- Se si prevede che la migrazione di un database non richiederà alcuna modifica, è consigliabile usare l'opzione 1, più semplice e veloce. Se non si è certi della necessità di modifiche, iniziare esportando dal database un file BACPAC contenente solo lo schema, come descritto nell'opzione 1. Se l'esportazione viene completata senza errori, è possibile usare l'opzione 1 per eseguire la migrazione del database con i relativi dati.  
- Se si verificano errori durante l'esportazione dell'opzione 1, usare la Migrazione guidata database SQL di Azure per elaborare il database in modalità solo schema, come descritto nell'opzione 2. Se la procedura guidata non restituisce errori, è possibile usare l'opzione 2. 
- Se la Migrazione guidata database SQL di Azure segnala che lo schema richiede altre operazioni, a meno che non siano necessarie solo semplici correzioni, è preferibile scegliere l'opzione 3 e correggere lo schema del database offline in Visual Studio usando una combinazione di modifiche allo schema eseguite mediante la procedura guidata e applicate manualmente. Una copia del database di origine viene quindi aggiornata sul posto e quindi migrata in Azure mediante l'opzione 1.

## Strumenti di migrazione
Tra strumenti disponibili per la migrazione sono inclusi SQL Server Management Studio (SSMS), gli strumenti di SQL Server in Visual Studio (VS, SSDT), la Migrazione guidata database SQL di Azure e il portale di Azure.

> Assicurarsi di installare le versioni più recenti degli strumenti client poiché le versioni precedenti non sono compatibili con la versione 12 del database SQL.

### SQL Server Management Studio (SSMS)
SSMS può essere usato per distribuire un database compatibile direttamente al database SQL di Azure o per esportare un backup logico del database come file BACPAC, che può quindi essere importato, sempre con SSMS, per creare un nuovo database SQL di Azure.

È necessario usare la versione più recente di SSMS (CU6 in SQL Server 2013 e versioni successive) oppure scaricare la [versione più recente](http://msdn.microsoft.com/evalcenter/dn434042.aspx) dello strumento.

### Migrazione guidata database SQL di Azure
La Migrazione guidata database SQL di Azure può essere usata per analizzare lo schema di un database esistente e determinarne la compatibilità con il database SQL di Azure e in molti casi può essere usata per generare e distribuire uno script T-SQL contenente lo schema e i dati. Se durante la trasformazione la procedura guidata incontra del contenuto dello schema che non riesce a trasformare, segnalerà degli errori. In tal caso, lo script generato richiederà altre modifiche prima di poter essere distribuito correttamente. La Migrazione guidata database SQL di Azure elabora il corpo delle funzioni o delle stored procedure che normalmente è escluso dalla convalida eseguita dagli strumenti di SQL Server in Visual Studio (vedere di seguito). Pertanto, potrebbe rilevare problemi che non verrebbero segnalati dalla convalida in Visual Studio. L'uso combinato della Migrazione guidata database SQL di Azure e degli strumenti di SQL Server in Visual Studio può ridurre notevolmente la quantità di lavoro necessaria per eseguire la migrazione di uno schema complesso.

Assicurarsi di usare la versione più recente della [Migrazione guidata database SQL di Azure](http://sqlazuremw.codeplex.com/) da CodePlex.

### Strumenti di SQL Server in Visual Studio (VS, SSDT)
Gli strumenti di SQL Server in Visual Studio possono essere usati per creare e gestire un progetto di database che include un set di file T-SQL per ogni oggetto nello schema. Il progetto può essere importato da un database o da un file di script. Una volta creato, il progetto può essere aggiornato alla versione 12 del database SQL di Azure. La compilazione del progetto ha quindi l'effetto di convalidare la compatibilità dello schema. Se si fa clic su un errore, viene aperto il file T-SQL corrispondente, in cui è possibile eseguire le opportune modifiche per correggere l'errore. Una volta risolti tutti gli errori, il progetto può essere pubblicato, direttamente nel database SQL, in modo da creare un database vuoto, oppure usando una copia del database originale di SQL Server, per aggiornarne lo schema. In questo modo, il database può essere distribuito con i dati tramite SSMS, come illustrato in precedenza.

Per la versione 12 del database SQL di Azure è necessario usare la versione più recente degli strumenti di database di SQL Server per Visual Studio. Assicurarsi che sia installato Visual Studio 2013 con Update 4.

## Confronti
| Opzione 1 | Opzione 2 | Opzione 3 |
| ------------ | ------------ | ------------ |
| Distribuire un database compatibile nel database SQL di Azure | Generare uno script di migrazione con modifiche ed eseguirlo sul database SQL di Azure | Aggiornare il database sul posto e quindi distribuirlo nel database SQL di Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Migrazione guidata database SQL di Azure](./media/sql-database-cloud-migrate/02SAMWDiagram.png) | ![Modifica offline](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Uso di SSMS | Uso della Migrazione guidata database SQL di Azure | Uso della Migrazione guidata database SQL di Azure, Visual Studio e SSMS |
|Processo semplice che richiede la compatibilità dello schema. Viene eseguita la migrazione dello schema senza modifiche. | La Migrazione guidata database SQL di Azure genera uno script T-SQL che include le modifiche necessarie per garantire la compatibilità. Alcune funzionalità non supportate vengono rimosse dallo schema; molte sono contrassegnate come errori. | Lo schema viene importato in un progetto di database in Visual Studio e (facoltativamente) trasformato con la Migrazione guidata database SQL di Azure. Altri aggiornamenti vengono eseguiti mediante gli strumenti di SQL Server in Visual Studio e lo schema finale usato per aggiornare il database sul posto. |
| Se si esporta un file BACPAC, è possibile scegliere di eseguire la migrazione del solo schema. | È possibile configurare la procedura guidata in modo da generare lo script del solo schema o quello dello schema con i dati. | È possibile pubblicare lo schema solo direttamente in Azure da Visual Studio. Il database viene aggiornato sul posto con le modifiche necessarie per consentire la distribuzione o l'esportazione dello schema e dei dati. |
| Viene sempre distribuito o esportato l'intero database. | È possibile scegliere di escludere oggetti specifici dalla migrazione. | Viene eseguito un controllo completo degli oggetti inclusi nella migrazione. |
| Non è prevista la modifica dell'output in caso di errori; lo schema di origine deve essere compatibile. | Un singolo script monolitico può essere difficile da modificare, se necessario. Lo script può essere aperto e modificato in SSMS o in Visual Studio con gli strumenti di database di SQL Server. È necessario correggere tutti gli errori prima di poter distribuire lo script al database SQL di Azure.| Sono disponibili le funzionalità complete degli strumenti di SQL Server in Visual Studio. Lo schema viene modificato offline. |
| La convalida dell'applicazione viene eseguita in Azure. Dovrebbe essere minima, dato che lo schema viene migrato senza modifiche. | La convalida dell'applicazione viene eseguita in Azure dopo la migrazione. Lo script generato può anche essere installato in locale per la convalida iniziale dell'applicazione. | La convalida dell'applicazione può essere eseguita in SQL Server prima che il database venga distribuito in Azure. |
| Strumento supportato da Microsoft. | Strumento supportato dalla community e scaricato da CodePlex. | Strumenti supportati da Microsoft con l'uso facoltativo dello strumento supportato dalla community e scaricato da CodePlex. |
| Viene eseguito un semplice processo di configurazione, in uno o due passaggi. | La trasformazione dello schema e la generazione e la distribuzione nel cloud vengono coordinate mediante una singola procedura guidata facile da usare. | Viene eseguito un processo complesso, in più passaggi (più semplice se viene distribuito solo lo schema). |

<!---HONumber=58--> 