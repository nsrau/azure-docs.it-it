<properties
	pageTitle="Limitazioni di Estensione database | Microsoft Azure"
	description="Informazioni sulle limitazioni di Estensione database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Limitazioni di Estensione database

Informazioni sulle limitazioni per le tabelle abilitate per Estensione e sulle limitazioni che attualmente impediscono l'abilitazione di Estensione su una tabella.

##  <a name="Caveats"></a>Limitazioni per le tabelle abilitate per Estensione

Le tabelle abilitate per Estensione presentano le limitazioni seguenti.

### Vincoli

-   L'univocità non viene applicata per i vincoli UNIQUE e i vincoli PRIMARY KEY in una tabella di Azure che contiene i dati migrati.

### Operazioni DML

-   Non è possibile AGGIORNARE o ELIMINARE righe in una tabella abilitata per Estensione o da una vista che include tali tabelle.

-   Non è possibile INSERIRE righe in una tabella abilitata per Estensione su un server collegato.

### Indici

-   Non è possibile creare un indice per una vista che include tabelle abilitate per l'estensione.

-   I filtri sugli indici di SQL Server non vengono propagati alla tabella remota.

##  <a name="Limitations"></a> Limitazioni che attualmente impediscono l'abilitazione dell'Estensione per una tabella

Attualmente, i seguenti elementi impediscono l'abilitazione dell'Estensione per una tabella.

### Proprietà tabella

-   Tabelle che includono più di 1.023 colonne o più di 998 indici

-   FileTable o tabelle contenenti dati FILESTREAM

-   Tabelle replicate o che usano le funzionalità Rilevamento modifiche o Change Data Capture

-   Tabelle con ottimizzazione per la memoria

### Tipi di dati

-   text, ntext e image

-   timestamp

-   sql\_variant

-   XML

-   Tipi di dati CLR tra cui i tipi geometry, geography, hierarchyid e tipi CLR definiti dall'utente

### Tipi di colonna

-   COLUMN\_SET

-   Colonne calcolate

### Vincoli

-   Vincoli predefiniti e vincoli CHECK

-   Vincoli di chiave esterna che fanno riferimento alla tabella. In una relazione padre-figlio (ad esempio Order e Order\_Detail), è possibile abilitare Estensione per la tabella figlio (Order\_Detail) ma non per la tabella padre (Order).

### Indici

-   Indici full-text

-   Indici XML

-   Indici spaziali

-   Viste indicizzate che fanno riferimento alla tabella

## Vedere anche

[Identificare database e tabelle per Database Estensione eseguendo l'ottimizzazione guidata Database Estensione](sql-server-stretch-database-identify-databases.md)

[Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0518_2016-->