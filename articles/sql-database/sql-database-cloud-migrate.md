<properties
   pageTitle="Migrazione di database nel database SQL di Azure"
   description="Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="09/02/2015"
   ms.author="carlrab"/>

# Migrazione di database nel database SQL di Azure

La versione 12 del database SQL di Azure offre una compatibilità quasi completa con SQL Server 2014 e versioni successive. Di conseguenza, la migrazione della maggior parte dei database da un'istanza locale di SQL Server 2005 o versione successiva a un database SQL Azure è molto più semplice. La migrazione per molti database è un'operazione di spostamento semplice che richiede poche modifiche allo schema e ai dati, con una minima o addirittura nessuna attività di riprogettazione dello schema e delle applicazioni. Anche quando è necessario apportare modifiche ai database, l'ambito di tali modifiche è più limitato.

Per impostazione predefinita, le funzionalità con ambito server di SQL Server non sono supportate dalla versione 12 del Database di SQL Azure. I database e le applicazioni basate su queste funzionalità dovranno essere riprogettate prima della migrazione. Anche se nella versione 12 del database SQL di Azure è stata migliorata la compatibilità con i database locali di SQL Server, la migrazione deve comunque essere pianificata ed eseguita con attenzione, soprattutto per i database più complessi e di dimensioni maggiori.

## Determinazione della compatibilità
Per determinare se il database di SQL Server locale è compatibile con la versione 12 del Database di SQL Azure, è possibile avviare la migrazione utilizzando uno dei due metodi illustrati nell’opzione #1 riportata di seguito e vedere se le routine di convalida dello schema rilevano un'incompatibilità o se è possibile utilizzare SQL Server Data Tools in Visual Studio come illustrato nell'opzione #2 di seguito per convalidare la compatibilità. Se il database di SQL Server locale presenta problemi di compatibilità, è possibile utilizzare SQL Server Data Tools in Visual Studio o SQL Server Management Studio per risolvere i problemi di compatibilità.

## Metodi di migrazione
Esistono diversi metodi per eseguire la migrazione di un database di SQL Server locale compatibile al database di SQL di Azure versione 12.

- Per i database di piccole e medie dimensioni, la migrazione di database compatibili con SQL Server 2005 (o versione successiva) è semplice e avviene tramite l’esecuzione della procedura guidata Distribuisci Database a un database Microsoft Azure in SQL Server Management Studio, purché non si disponga di problemi di connettività (nessuna connettività, larghezza di banda ridotta o problemi di timeout).
- Per i database di medie e grandi dimensioni oppure in caso di problemi di connettività, è possibile utilizzare SQL Server Management Studio per esportare i dati e lo schema in un file BACPAC (archiviato localmente o in un BLOB di Azure) e quindi importare il file BACPAC nell'istanza di SQL Azure. Se si archivia il file BACPAC in un BLOB di Azure, è inoltre possibile importare il file BACPAC dal portale di Azure. Per ulteriori informazioni su un file BACPAC, vedere [applicazioni a livello di dati](https://msdn.microsoft.com/library/ee210546.aspx).
- Per i database di grandi dimensioni, si otterranno migliori prestazioni migrando lo schema e i dati separatamente. È possibile estrarre lo schema in un progetto di database utilizzando SQL Server Management Studio o Visual Studio e quindi distribuire lo schema per creare il database di SQL Azure. Successivamente, è possibile estrarre i dati tramite BCP e quindi utilizzare BCP per importare i dati utilizzando flussi paralleli nel database SQL Azure. La migrazione di un database complesso e di grandi dimensioni richiede molte ore indipendentemente dal metodo scelto.

### Opzione 1
***Migrazione di un database compatibile tramite SQL Server Management Studio (SSMS) ***

SQL Server Management Studio fornisce due metodi per la migrazione di un database di SQL Server locale compatibile a un database SQL Azure. È possibile utilizzare la procedura guidata Distribuisci database a un database SQL di Microsoft Azure o esportare il database in un file BACPAC, che può quindi essere importato per creare un nuovo database SQL Azure. La procedura guidata verifica la compatibilità con la versione 12 del Database di SQL Azure, consente di estrarre lo schema e i dati in un file BACPAC e quindi importarli nell'istanza del database SQL Azure specificata. Per utilizzare questa opzione, vedere [utilizzare SSMS](sql-database-migrate-ssms.md).

### Opzione 2
***Aggiornamento offline dello schema del database tramite Visual Studio e distribuzione tramite SQL Server Management Studio***

Se il database di SQL Server locale non è compatibile o per determinare se è compatibile, è possibile importare lo schema del database in un progetto di database di Visual Studio per l'analisi. Per eseguire l’analisi, specificare Database SQL versione 12 come piattaforma di destinazione per il progetto, quindi compilare il progetto. Se la compilazione ha esito positivo, il database è compatibile. Se la compilazione ha esito negativo, è possibile risolvere gli errori con SQL Server Data Tools per Visual Studio ("SSDT"). Una volta creato il progetto, è possibile pubblicarlo nuovamente come una copia del database di origine e quindi utilizzare la funzione di confronto di dati in SSDT per copiare i dati dal database di origine al database compatibile alla versione 12 di SQL Azure. Il database aggiornato viene quindi distribuito al database SQL di Azure mediante l'opzione 1. Se è necessario eseguire la migrazione solo dello schema, è possibile pubblicarlo direttamente da Visual Studio nel database SQL di Azure. Utilizzare questo metodo quando lo schema del database richiede altre modifiche che è possibile gestire solo attraverso la migrazione guidata. Per utilizzare questa opzione, vedere [Utilizzare Visual Studio](sql-database-migrate-visualstudio-ssdt.md).

## Scelta delle opzioni
- Se si prevede che la migrazione di un database non richiederà alcuna modifica, è consigliabile usare l'opzione #1, più semplice e veloce. Se non si è certi della necessità di modifiche, iniziare esportando dal database un file BACPAC contenente solo lo schema, come descritto nell'opzione #1. Se l'esportazione viene completata senza errori, è possibile usare l'opzione #1 per eseguire la migrazione del database con i relativi dati.  
- Se si verificano errori durante l'esportazione dell'opzione#1, utilizzare l'opzione #2 e correggere lo schema del database non in linea in Visual Studio utilizzando una combinazione della migrazione guidata e delle modifiche dello schema applicate manualmente. Una copia del database di origine viene quindi aggiornata sul posto e quindi migrata in Azure mediante l'opzione 1.

## Strumenti di migrazione
Tra strumenti disponibili per la migrazione sono inclusi SQL Server Management Studio (SSMS), gli strumenti di SQL Server in Visual Studio (VS, SSDT), e il portale di Azure.

> [AZURE.IMPORTANT]Assicurarsi di installare le versioni più recenti degli strumenti client poiché le versioni precedenti non sono compatibili con la versione 12 del database SQL.

### SQL Server Management Studio (SSMS)
SSMS può essere usato per distribuire un database compatibile direttamente al database SQL di Azure o per esportare un backup logico del database come file BACPAC, che può quindi essere importato, sempre con SSMS, per creare un nuovo database SQL di Azure.

[Scaricare la versione più recente di SSMS](https://msdn.microsoft.com/library/mt238290.aspx)

### Strumenti di SQL Server in Visual Studio (VS, SSDT)
Gli strumenti di SQL Server in Visual Studio possono essere usati per creare e gestire un progetto di database che include un set di file Transact-SQL per ogni oggetto nello schema. Il progetto può essere importato da un database o da un file di script. Una volta creato, il progetto può essere aggiornato alla versione 12 del database SQL di Azure. La compilazione del progetto ha quindi l'effetto di convalidare la compatibilità dello schema. Se si fa clic su un errore, viene aperto il file Transact-SQL corrispondente, in cui è possibile eseguire le opportune modifiche per correggere l'errore. Una volta risolti tutti gli errori, il progetto può essere pubblicato, direttamente nel database SQL, in modo da creare un database vuoto, oppure usando una copia del database originale di SQL Server, per aggiornarne lo schema. In questo modo, il database può essere distribuito con i dati tramite SSMS, come illustrato in precedenza.

Utilizzare il[più recente SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)con Visual Studio 2013 aggiornamento 4 o versione successiva.

## Confronti
| Opzione 1 | Opzione 2 |
| ------------ | ------------ | ------------ |
| Distribuire un database compatibile nel database SQL di Azure | Aggiornare il database sul posto e quindi distribuirlo nel database SQL di Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Modifica offline](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Uso di SSMS | Utilizza Visual Studio e SQL Server Management Studio |
|Processo semplice che richiede la compatibilità dello schema. Viene eseguita la migrazione dello schema senza modifiche. | Lo schema viene importato in un progetto di database in Visual Studio. Altri aggiornamenti vengono eseguiti mediante gli strumenti di SQL Server in Visual Studio e lo schema finale usato per aggiornare il database sul posto. |
| Viene sempre distribuito o esportato l'intero database. | Viene eseguito un controllo completo degli oggetti inclusi nella migrazione. |
| Non è prevista la modifica dell'output in caso di errori; lo schema di origine deve essere compatibile. | Funzionalità complete di SSDT di Visual Studio disponibili. Lo schema viene modificato offline. | La convalida dell'applicazione viene eseguita in Azure. Dovrebbe essere minima, dato che lo schema viene migrato senza modifiche. | La convalida dell'applicazione può essere eseguita in SQL Server prima che il database venga distribuito in Azure. |
| Viene eseguito un semplice processo di configurazione, in uno o due passaggi. | Viene eseguito un processo complesso, in più passaggi (più semplice se viene distribuito solo lo schema). |

<!----HONumber=Sept15_HO3-->