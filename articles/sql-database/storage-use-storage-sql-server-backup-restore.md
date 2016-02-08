<properties
	pageTitle="Come usare l'archiviazione di Azure per il backup e il ripristino di SQL Server | Microsoft Azure"
	description="Backup e ripristino di SQL Server e database SQL nell'archiviazione di Azure Vengono illustrati i vantaggi del backup dei database SQL nell'archiviazione di Azure e i componenti necessari del server SQL e dell'archiviazione di Azure"
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="carlrab"/>



# Come usare l'archiviazione di Azure per il backup e ripristino di SQL Server

## Panoramica

La funzionalità che consente la scrittura di backup di SQL Server nel servizio di archiviazione BLOB di Azure è stata rilasciata in SQL Server 2012 SP1 CU2. È possibile usare questa funzionalità per eseguire operazioni di backup e ripristino dal servizio BLOB di Azure, con un database di SQL Server locale o un database di SQL Server in una macchina virtuale di Azure. Il backup nel cloud offre vantaggi in termini di disponibilità, di archiviazione fuori sede con replica geografica illimitata e di semplicità di migrazione dei dati da e verso il cloud. È possibile eseguire istruzioni BACKUP o RESTORE usando T-SQL o SMO. Inoltre, quando i file di database sono archiviati in un BLOB di Azure e si usa SQL Server 2016, è possibile usare il [backup di snapshot di file](http://msdn.microsoft.com/library/mt169363.aspx) per eseguire backup quasi istantanei e ripristini estremamente rapidi.

## Vantaggi dell'uso del servizio BLOB di Azure per i backup di SQL Server

La gestione dell'archiviazione, i rischi correlati agli errori di archiviazione, l'accesso all'archiviazione fuori sede e la configurazione dei dispositivi sono solo alcune delle problematiche generali legate alle attività di backup. Queste problematiche esistono sia per le istanze del database locali che per le istanze del database in macchine virtuali di Azure. Di seguito vengono presentati alcuni dei vantaggi principali dell'uso del servizio di archiviazione BLOB di Azure per i backup di SQL Server:

* Archiviazione fuori sede flessibile, affidabile e illimitata: l'archiviazione dei backup in BLOB di Azure può rappresentare un'opzione fuori sede conveniente, flessibile e semplice per l'accesso. La creazione di un sistema di archiviazione fuori sede per i backup di SQL Server è estremamente semplice ed è realizzabile modificando gli script e i processi esistenti in modo da usare la sintassi **BACKUP TO URL**. L'archiviazione fuori sede dovrebbe in genere essere ubicata a una certa distanza dal luogo fisico in cui si trova il database di produzione, al fine di evitare che nell'impatto di una situazione di emergenza possano trovarsi coinvolti sia la sede remota che quella in cui si trova il database di produzione. Scegliendo di eseguire la [replica geografica dei BLOB di Azure](../storage/storage-redundancy.md), si otterrà un livello aggiuntivo di protezione nel caso in cui si verifichi un'emergenza che potrebbe interessare l'intera area. 
* Archivio di backup: il servizio di archiviazione BLOB di Azure offre un'alternativa migliore alle opzioni più diffuse, come il nastro, per l'archiviazione dei backup. L'archiviazione su nastro può richiedere il trasporto fisico alla struttura fuori sede e l'adozione di determinate misure per la protezione dei supporti. L'archiviazione dei backup nell'archiviazione BLOB di Azure rappresenta un'opzione di archiviazione istantanea, estremamente disponibile e durevole.
* Nessun sovraccarico sulla gestione dell'hardware: non esiste alcun overhead di gestione dell'hardware con servizi di Azure. I servizi di Azure consentono di gestire l'hardware con l'aggiunta della replica geografica per la ridondanza e la protezione dagli errori hardware.
* Per istanze di SQL Server eseguite in una macchina virtuale di Azure, il backup nei servizi di archiviazione BLOB di Azure può attualmente essere realizzato mediante la creazione di dischi collegati. Esiste tuttavia un limite per il numero di dischi che è possibile collegare a una macchina virtuale di Azure per i backup. Tale limite è di 16 dischi per un'istanza molto grande e un numero di dischi inferiore per istanze più piccole. Abilitando un backup diretto sui BLOB di Azure, si ottiene l'accesso a uno spazio di archiviazione teoricamente illimitato.
* I backup archiviati in BLOB di Azure sono disponibili ovunque e in qualsiasi momento e sono facilmente accessibili per il ripristino in un'istanza di SQL Server locale oppure in un'altra istanza eseguita in una macchina virtuale di Azure, senza che sia necessario collegare e scollegare il database o scaricare e collegare il disco rigido virtuale.
* Vantaggi in termini di costo: consente di pagare solo per il servizio usato. Può rivelarsi una soluzione economica per il backup e l'archiviazione fuori sede. Per altre informazioni, vedere il [Calcolatore dei costi di Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calcolatore dei prezzi") e l'articolo [Panoramica dei prezzi di Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Prezzi articolo").
* Usare gli snapshot di archiviazione: quando i file di database sono archiviati in un BLOB di Azure e si usa SQL Server 2016, è possibile usare il [backup di snapshot di file](http://msdn.microsoft.com/library/mt169363.aspx) per eseguire backup quasi istantanei e ripristini estremamente rapidi.

Per altre informazioni, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Nelle due sezioni seguenti viene fornita un'introduzione al servizio di archiviazione BLOB di Azure e ai componenti di SQL Server utilizzati quando si eseguono attività di backup o ripristino da tale servizio. Per l'esecuzione di un backup o ripristino dal servizio di archiviazione BLOB di Azure, è importante comprendere la natura dei componenti e l'interazione che ha luogo tra questi.

Il primo passaggio nel processo consiste nel creare un account Azure. Per una procedura dettagliata completa sulla creazione di un account di archiviazione e sull'esecuzione di un semplice ripristino con SQL Server 2014, vedere [Introduzione al servizio di archiviazione di Azure per il backup e ripristino di SQL Server](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx). Per una procedura dettagliata completa sulla creazione di un account di archiviazione e sull'esecuzione di un semplice ripristino con SQL Server 2014, vedere [Esercitazione: Uso del servizio di archiviazione BLOB di Microsoft Azure con i database di SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Componenti del servizio di archiviazione BLOB di Azure

* Account di archiviazione: l'account di archiviazione è il punto di partenza per tutti i servizi di archiviazione. Per accedere a un servizio di archiviazione BLOB di Azure, è innanzitutto necessario creare un account di archiviazione di Azure. Per ulteriori informazioni sul servizio di archiviazione BLOB di Azure, vedere [Come utilizzare il servizio di archiviazione BLOB di Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Contenitore: un contenitore fornisce il raggruppamento di un set di BLOB ed è in grado di archiviare un numero di BLOB illimitato. Per scrivere un backup di SQL Server in un servizio BLOB di Azure, deve prima essere stato creato almeno il contenitore radice.

* BLOB: un file di qualsiasi tipo e dimensione. I BLOB sono indirizzabili usando il formato di URL seguente: `https://<storage account>.blob.core.windows.net/<container>/<blob>` Per altre informazioni sui BLOB di pagine, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx)

## Componenti di SQL Server

* URL: un URL specifica un URI (Uniform Resource Identifier) per un file di backup specifico. L'URL viene usato per fornire il percorso e il nome del file di backup di SQL Server. L'URL deve puntare a un BLOB effettivo e non a un contenitore. Se il BLOB non esiste, verrà creato. Se viene specificato un BLOB esistente, il comando BACKUP non riuscirà a meno che non sia specificata l'opzione WITH FORMAT. Di seguito è riportato un esempio di URL che verrebbe specificato in un comando BACKUP: ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Nota</b>: HTTPS non è obbligatorio ma è consigliato. <b>Importante</b>: se si sceglie di copiare e caricare un file di backup nel servizio di archiviazione BLOB di Azure e si prevede di usarlo per operazioni di ripristino, è necessario usare il tipo di BLOB di pagine. Il comando RESTORE da un tipo di BLOB in blocchi non riuscirà e restituirà un errore.

* Credenziali: le informazioni necessarie per la connessione e l'autenticazione a un servizio di archiviazione BLOB di Azure vengono archiviate in una credenziale. Per fare in modo che SQL Server sia in grado di scrivere backup in un BLOB di Azure o di eseguire un ripristino da quest'ultimo, è necessario creare una credenziale di SQL Server. Per altre informazioni, vedere [Credenziali di SQL Server](https://msdn.microsoft.com/library/ms189522.aspx).

## Backup e ripristino di database di SQL Server con BLOB di Azure - Concetti e attività

**Concetti, considerazioni ed esempi di codice:**

[Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Esercitazione introduttiva:**

[Esercitazione: Uso del servizio di archiviazione BLOB di Microsoft Azure con i database di SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

**Procedure consigliate e risoluzione dei problemi:**

[Procedure consigliate relative al backup e al ripristino (servizio di archiviazione BLOB di Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=AcomDC_0128_2016-->