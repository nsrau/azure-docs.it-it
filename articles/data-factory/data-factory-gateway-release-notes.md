<properties 
	pageTitle="Note sulla versione di Gateway di gestione dati | Data factory di Azure" 
	description="Note sulla versione di Gateway di gestione dati" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Note sulla versione di Gateway di gestione dati

Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento uniforme di dati da ambienti locali al cloud e viceversa. Data factory facilita questa integrazione con Gateway di gestione dati, un agente che è possibile installare in locale per abilitare lo spostamento di dati ibridi.

Per altre informazioni, vedere [Spostare dati tra origini locali e il cloud mediante il Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

## Versione corrente (1.10.5892.1)

• Miglioramenti delle prestazioni • Correzioni dei bug

## Versioni precedenti

## 1\.9.5865.2

- Funzionalità di aggiornamento automatico senza intervento dell'utente
- Nuova icona dell'area di notifica con indicatori di stato del gateway
- Possibilità di scegliere "Aggiorna adesso" dal client
- Possibilità di impostare l'ora di pianificazione dell'aggiornamento
- Script di PowerShell per attivare o disattivare l'aggiornamento automatico 
- Miglioramenti delle prestazioni
- Correzioni di bug

## 1\.8.5822.1

- Miglioramento dell'esperienza di risoluzione dei problemi
- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.7.5795.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.7.5764.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.6.5735.1

- Supporto di origine/sink HDFS in locale
- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.6.5696.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.6.5676.1

- Supporto degli strumenti di diagnostica in Gestione configurazione
- Supporto delle colonne di tabella per le origini dati tabulari per Data factory di Azure
- Supporto di SQL DW per Data factory di Azure
- Supporto dell'isolamento in BlobSource e FileSource per Data factory di Azure
- Supporto di CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy in BlobSink e FileSink con copia binaria per Data factory di Azure
- Supporto dello stato di avanzamento della creazione di report per l'attività di copia per Data factory di Azure
- Supporto della convalida della connettività dell'origine dati per Data factory di Azure
- Correzioni di bug


### 1\.6.5672.1

- Supporto del nome di tabella per l'origine dati ODBC per Data factory di Azure
- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.6.5658.1

- Supporto del sink dei file per Data factory di Azure
- Supporto del mantenimento della gerarchia nella copia binaria per Data factory di Azure
- Supporto dell'idempotenza dell'attività di copia per Data factory di Azure
- Correzioni di bug

### 1\.6.5640.1

- Supporto di altre 3 origini dati per Data factory di Azure (ODBC, OData, HDFS)
- Supporto delle virgolette nel parser CSV per Data factory di Azure
- Supporto della compressione (BZip2)
- Correzioni di bug

### 1\.5.5612.1

- Supporto di 5 database relazionali per Data factory di Azure (MySQL, PostgreSQL, DB2, Teradata e Sybase)
- Supporto della compressione (Gzip e Deflate)
- Miglioramenti delle prestazioni
- Correzioni di bug


### 1\.4.5549.1

- Aggiunta del supporto dell'origine dati Oracle per Data factory di Azure
- Miglioramenti delle prestazioni
- Correzioni di bug

### 1\.4.5492.1

- File binario unificato che supporta il servizio Data factory di Microsoft Azure e il servizio Power BI di Office 365
- Perfezionamento dell'interfaccia utente di configurazione e del processo di registrazione
- Data Factory di Azure: supporto di ingresso e uscita in Azure per l'origine dati SQL Server

### 1\.2.5303.1

- 	Risoluzione del problema di timeout per supportare connessioni alle origini dati più dispersive in termini di tempo. 
 	
### 1\.1.5526.8

- Richiede .NET Framework 4.5.1 come prerequisito durante l'installazione.

### 1\.0.5144.2

- Nessuna modifica che interessi gli scenari di Data factory di Azure. 

## Domande e risposte

### Perché Gestione origine dati prova a connettersi a un gateway?
Si tratta di una progettazione di sicurezza secondo cui è possibile configurare solo origini dati locali per l'accesso cloud all'interno della rete aziendale, evitando la propagazione delle credenziali all'esterno del firewall aziendale. Assicurarsi che il computer possa raggiungere il computer in cui è installato il gateway.

<!---HONumber=AcomDC_0302_2016-->